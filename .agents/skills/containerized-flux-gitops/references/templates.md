# Containerized Flux GitOps templates

Replace every `<placeholder>` before applying these examples. The Node Dockerfile is a shape, not a
requirement: preserve the application's actual package manager, build output, server command, port,
and health endpoint. Read the parent skill first for ownership and security constraints.

## Resulting application repository layout

Keep deployable application manifests in one path so both Flux automation and Flux reconciliation
operate on the same files.

```text
Dockerfile
.github/workflows/container.yml
kubernetes/
├── namespace.yaml
├── deployment.yaml
├── service.yaml
├── kustomization.yaml
├── image-automation.yaml
├── secret.yaml                 # encrypted or created outside Git; optional
├── httproute.yaml              # optional: Gateway API cluster
└── middleware.yaml             # optional: Traefik cluster
```

A cluster configuration repository may additionally own the `GitRepository` and Flux
`Kustomization` that reconciles this directory. The **Flux reconciliation template** below belongs
there unless the repository already has a parent bootstrap Kustomization that applies it.

## Dockerfile: Node service example

```dockerfile
FROM node:22-bookworm-slim AS build

ENV PNPM_HOME="/pnpm"
ENV PATH="$PNPM_HOME:$PATH"
WORKDIR /app

RUN corepack enable && corepack prepare pnpm@<pnpm-version> --activate

COPY package.json pnpm-lock.yaml ./
RUN pnpm install --frozen-lockfile

COPY . .
RUN pnpm build && pnpm prune --prod

FROM node:22-bookworm-slim AS runtime

ENV NODE_ENV=production
ENV PORT=<port>
WORKDIR /app

COPY --from=build --chown=node:node /app/package.json ./package.json
COPY --from=build --chown=node:node /app/node_modules ./node_modules
COPY --from=build --chown=node:node /app/<build-output> ./<build-output>

USER node
EXPOSE <port>
CMD ["node", "<build-output>/server/server.mjs"]
```

Before adding GitOps files, prove the runtime contract locally:

```sh
docker build -t <registry>/<owner>/<app>:local .
docker run --rm -p <port>:<port> <registry>/<owner>/<app>:local
curl --fail http://localhost:<port><health-path>
```

## GitHub Actions: build and publish

This workflow's tag scheme intentionally aligns with the Flux numerical policy. It builds all PRs,
but a fork PR cannot obtain registry credentials. Same-repository PRs are trusted in this example
and may publish a `pr-...` tag; the policy below excludes those tags from production.

```yaml
name: Container

on:
  push:
    branches: [main]
    paths-ignore: [kubernetes/**]
  pull_request:
    branches: [main]
    paths-ignore: [kubernetes/**]

permissions:
  contents: read

jobs:
  container:
    runs-on: ubuntu-latest
    steps:
      - name: Check out repository
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Determine image tag
        id: image
        shell: bash
        env:
          EVENT_NAME: ${{ github.event_name }}
          RUN_NUMBER: ${{ github.run_number }}
          PR_NUMBER: ${{ github.event.pull_request.number }}
          PR_HEAD_SHA: ${{ github.event.pull_request.head.sha }}
          REPOSITORY: ${{ github.repository }}
          PR_HEAD_REPOSITORY: ${{ github.event.pull_request.head.repo.full_name }}
        run: |
          if [[ "$EVENT_NAME" == "push" ]]; then
            tag="main-${RUN_NUMBER}"
            trusted=true
          else
            tag="pr-${PR_NUMBER}-${PR_HEAD_SHA:0:7}"
            trusted=false
            if [[ "$REPOSITORY" == "$PR_HEAD_REPOSITORY" ]]; then
              trusted=true
            fi
          fi
          echo "tag=$tag" >> "$GITHUB_OUTPUT"
          echo "trusted=$trusted" >> "$GITHUB_OUTPUT"

      - name: Log in to registry
        if: steps.image.outputs.trusted == 'true'
        uses: docker/login-action@v3
        with:
          registry: <registry>
          username: ${{ secrets.REGISTRY_USERNAME }}
          password: ${{ secrets.REGISTRY_PASSWORD }}

      - name: Build and optionally push image
        id: build
        uses: docker/build-push-action@v6
        with:
          context: .
          file: ./Dockerfile
          push: ${{ steps.image.outputs.trusted == 'true' }}
          tags: <registry>/<owner>/<app>:${{ steps.image.outputs.tag }}
          cache-from: type=gha
          cache-to: ${{ steps.image.outputs.trusted == 'true' && 'type=gha,mode=max' || '' }}

      - name: Report published digest
        if: steps.image.outputs.trusted == 'true'
        run: |
          echo "Published <registry>/<owner>/<app>:${{ steps.image.outputs.tag }}"
          echo "Digest: ${{ steps.build.outputs.digest }}"
```

Store `REGISTRY_USERNAME` and `REGISTRY_PASSWORD` as GitHub Actions secrets. Give the CI principal
only the registry permissions it needs. Do not replace the unique production tag with `latest`:
Flux needs an unambiguous immutable candidate.

## Application manifests in `kubernetes/`

### `namespace.yaml`

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: <app>
```

### `deployment.yaml`

The image tag below is a bootstrap value. Kustomize owns the effective tag, through the `images`
entry in `kustomization.yaml`.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: <app>
  namespace: <app>
  labels:
    app: <app>
spec:
  replicas: 1
  strategy:
    type: RollingUpdate
  selector:
    matchLabels:
      app: <app>
  template:
    metadata:
      labels:
        app: <app>
    spec:
      imagePullSecrets:
        - name: <workload-registry-pull-secret>
      containers:
        - name: <app>
          image: <registry>/<owner>/<app>:main-0
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: <port>
          startupProbe:
            httpGet:
              path: <health-path>
              port: http
            periodSeconds: 5
            failureThreshold: 24
          readinessProbe:
            httpGet:
              path: <health-path>
              port: http
            periodSeconds: 10
            timeoutSeconds: 5
            failureThreshold: 3
          livenessProbe:
            httpGet:
              path: <health-path>
              port: http
            periodSeconds: 30
            timeoutSeconds: 5
            failureThreshold: 3
```

Add a `Host` header only when the application or ingress genuinely requires one. Tune the startup
window to the measured cold-start time; a liveness probe must not kill a process that is still
legitimately starting.

### `service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: <app>
  namespace: <app>
spec:
  type: ClusterIP
  selector:
    app: <app>
  ports:
    - name: http
      port: 80
      targetPort: http
```

### `kustomization.yaml`

The marker must stay on the exact field Flux updates. The image name must exactly match the one in
the Deployment and ImageRepository.

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - namespace.yaml
  - deployment.yaml
  - service.yaml
  - secret.yaml
  - httproute.yaml
  - middleware.yaml
  - image-automation.yaml
images:
  - name: <registry>/<owner>/<app>
    newTag: main-0 # {"$imagepolicy": "flux-system:<app>:tag"}
```

Remove optional resources from this list when the corresponding files or CRDs do not exist. Do not
put the image policy marker on both this tag and `deployment.yaml`; one authoritative location
prevents conflicting updates.

### `image-automation.yaml`

`ImageRepository` uses a registry-scanning Secret in `flux-system`, while the workload's
`imagePullSecrets` value names a pull Secret in the application namespace. Kubernetes secrets are
not shared across namespaces, even when they contain the same credentials.

```yaml
apiVersion: image.toolkit.fluxcd.io/v1
kind: ImageRepository
metadata:
  name: <app>
  namespace: flux-system
spec:
  image: <registry>/<owner>/<app>
  interval: 1m0s
  secretRef:
    name: <flux-registry-credentials>
---
apiVersion: image.toolkit.fluxcd.io/v1
kind: ImagePolicy
metadata:
  name: <app>
  namespace: flux-system
spec:
  imageRepositoryRef:
    name: <app>
  filterTags:
    pattern: '^main-(?P<build>[0-9]+)$'
    extract: '$build'
  policy:
    numerical:
      order: asc
---
apiVersion: image.toolkit.fluxcd.io/v1
kind: ImageUpdateAutomation
metadata:
  name: <app>
  namespace: flux-system
spec:
  interval: 1m0s
  sourceRef:
    kind: GitRepository
    name: <app>-repo
  git:
    checkout:
      ref:
        branch: main
    commit:
      author:
        name: fluxcdbot
        email: fluxcdbot@users.noreply.github.com
      messageTemplate: 'chore(images): update <app> image'
    push:
      branch: main
  update:
    path: ./kubernetes
    strategy: Setters
```

The `GitRepository/<app>-repo` must be ready before the automation can commit. Its credential must
be able to clone **and push** the `main` branch. If direct automation commits violate repository
policy, use a separate update branch and a reviewed merge mechanism; do not silently make Flux a
branch-protection bypass.

## Optional ingress and encrypted secrets

Use these only with the matching cluster CRDs and controller. Do not assume an HTTPRoute,
Traefik Middleware, or SOPS are universally installed.

### `httproute.yaml` (Gateway API)

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: <app>
  namespace: <app>
spec:
  parentRefs:
    - name: <gateway-name>
      namespace: <gateway-namespace>
      sectionName: https
  hostnames:
    - <public-hostname>
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /
      backendRefs:
        - name: <app>
          port: 80
```

### SOPS workflow

Commit encrypted manifests only. For example, create a local, ignored decrypted file, edit it with
approved credentials, then encrypt it in place:

```sh
sops --encrypt --in-place kubernetes/secret.yaml
sops --encrypt --in-place kubernetes/httproute.yaml
```

The Flux `Kustomization` which applies this path must be configured for SOPS decryption and have
access to the decryption key. Never commit a `*.dec.yaml` file or print a decrypted Secret in CI.

## Flux reconciliation template: cluster configuration owner

Image automation writes Git; a Flux `Kustomization` performs the deployment. This pair usually
belongs to the cluster configuration repository, not the application repo. It may be managed by an
existing root/parent Flux Kustomization. In that case, add it to the parent repository's resource
list rather than adding it to the application `kubernetes/kustomization.yaml` (which would make the
application reconcile itself).

```yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: <app>-repo
  namespace: flux-system
spec:
  interval: 1m0s
  url: ssh://git@github.com/<owner>/<repository>.git
  ref:
    branch: main
  secretRef:
    name: <app>-repo-auth
---
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: <app>
  namespace: flux-system
spec:
  interval: 5m0s
  retryInterval: 1m0s
  path: ./kubernetes
  prune: true
  wait: true
  timeout: 5m0s
  sourceRef:
    kind: GitRepository
    name: <app>-repo
  decryption:
    provider: sops
    secretRef:
      name: sops-age
```

Remove `decryption` when this path contains no SOPS-encrypted fields. Do not set
`targetNamespace` for a path that includes Flux resources in `flux-system`; the manifests already
state their namespaces explicitly.

If this repository is itself the cluster configuration repository, it is acceptable to store the
pair in `kubernetes/flux-reconciliation.yaml`, but a previously bootstrapped parent must apply that
file initially. Do not list that self-reconciliation file in the same application Kustomize resource
list unless the resulting ownership model has been deliberately reviewed.

## Validate and reconcile

Render locally, then observe each controller stage. Replace placeholders with real names.

```sh
# Local configuration and image build
kubectl kustomize kubernetes
pnpm build
docker build -t <registry>/<owner>/<app>:local .

# Image discovery and Git update
flux get image repository <app> -n flux-system
flux get image policy <app> -n flux-system
flux get image update <app> -n flux-system
flux reconcile image repository <app> -n flux-system
flux reconcile image policy <app> -n flux-system
flux reconcile image update <app> -n flux-system

# Fetch the Git source, apply the path, and inspect the workload
flux reconcile kustomization <app> -n flux-system --with-source
flux get kustomizations -n flux-system
kubectl rollout status deployment/<app> -n <app> --timeout=5m
kubectl get pods,service -n <app>
```

For a failure, check the ready condition and controller events before changing YAML:

```sh
kubectl describe imagerepository/<app> -n flux-system
kubectl describe imagepolicy/<app> -n flux-system
kubectl describe imageupdateautomation/<app> -n flux-system
kubectl describe kustomization/<app> -n flux-system
kubectl get events -n flux-system --sort-by=.lastTimestamp
```

Expected Git behavior: after a successful new `main-N` tag, Flux makes a small automation commit
that changes only `kubernetes/kustomization.yaml`'s `newTag`. The `GitRepository` detects that
commit, and the Flux `Kustomization` applies it to produce a normal rolling Deployment update.
