---
name: containerized-flux-gitops
description: Containerize an application and deploy it through GitHub Actions, a container registry, Flux CD image automation, and Flux reconciliation. Use when adding or reviewing Docker, GitHub Actions image publishing, Kubernetes manifests in kubernetes/, Flux ImageRepository/ImagePolicy/ImageUpdateAutomation resources, or GitOps rollout troubleshooting.
---

# Containerized Flux GitOps

Use this skill to establish the deployment pattern used by this project:

```text
push to main → GitHub Actions builds registry/app:main-N → registry
  → Flux ImageRepository scans → ImagePolicy selects main-N
  → ImageUpdateAutomation commits the tag in kubernetes/kustomization.yaml
  → Flux GitRepository + Kustomization reconciles kubernetes/ → Kubernetes rollout
```

The production reference files are `Dockerfile`, `.github/workflows/container.yml`, and
`kubernetes/`. Read them before changing this project; preserve its image name, tag contract,
probe paths, secret names, and Flux source topology unless the request explicitly changes them.

## Scope and invariants

Use this for requests such as:

- “Containerize this service and deploy it with Flux.”
- “Add automated image promotion after GitHub Actions publishes an image.”
- “Diagnose why Flux did not roll out the latest main build.”

Collect or infer these inputs before generating configuration: application and namespace names,
registry/image path, deployment branch, exposed port, health path, package/build/runtime command,
registry credential-secret names, and whether the app repository is also the Flux configuration
repository. Do not invent a public hostname, secret value, or cluster source name.

Keep these boundaries intact:

- Put application manifests, image automation, and their Kustomize file in `kubernetes/`.
- Build only immutable, uniquely named image tags; this project uses `main-<GitHub run number>`.
- Make `ImagePolicy` accept only production tags. PR tags must not be eligible for production.
- Let Flux change the image tag through its marker in `kubernetes/kustomization.yaml`. Do not
  update a live Deployment with `kubectl set image` or edit the Deployment tag independently.
- Keep credentials out of Git. Registry secrets are namespace-scoped: the workload pull secret and
  Flux's registry-scanning secret may need separate Secret objects in different namespaces.
- Ensure the Git credential used by the Flux `GitRepository` can push automation commits to the
  configured branch. Branch protection must permit that bot or use a reviewed update-branch flow.

Read `references/templates.md` when creating files or troubleshooting the pipeline. It contains the
complete, placeholder-based Docker, workflow, Kubernetes, Flux, SOPS, reconciliation, and command
examples.

## Implement in this order

1. **Inspect the runtime first.** Find the app’s production build, server command, port, and a
   lightweight health endpoint. Use a multi-stage Dockerfile: install from the lockfile, build,
   prune to production dependencies where appropriate, copy only runtime artifacts, and run as a
   non-root user. Build the image locally before wiring deployment automation.

2. **Publish safely from GitHub Actions.** Use Buildx and one shared tag decision. A `main` push is
   trusted and is pushed; pull requests are built for validation, with fork PRs never receiving
   registry credentials. The current workflow also publishes same-repository PR images, so retain
   that distinction if reproducing it. Ignore `kubernetes/**` in the workflow triggers: otherwise a
   Flux image-update commit starts a needless build-and-push feedback loop.

3. **Create the `kubernetes/` tree.** At minimum include a Namespace, Deployment, Service,
   `kustomization.yaml`, and `image-automation.yaml`. The Deployment should reference a harmless
   bootstrap tag, while the Kustomize `images[].newTag` is the sole deployable tag and carries:

   ```yaml
   newTag: main-0 # {"$imagepolicy": "flux-system:<app>:tag"}
   ```

   Kustomize then replaces the image in the Deployment. Use startup, readiness, and liveness probes
   that exercise the real HTTP service. Add route, middleware, and encrypted Secret manifests only
   when the target cluster uses those APIs.

4. **Connect Flux image automation.** Put `ImageRepository`, `ImagePolicy`, and
   `ImageUpdateAutomation` in `flux-system`. The policy's tag regex must match the workflow's tag
   format and select the greatest build number. Set `spec.update.path: ./kubernetes` and use the
   `Setters` strategy, which recognizes the marker above. The automation `sourceRef` must name the
   `GitRepository` which represents this repository in the cluster.

5. **Handle reconciliation at the right ownership boundary.** Image automation only commits Git;
   it does not apply manifests. A Flux `GitRepository` plus `Kustomization` must reconcile
   `./kubernetes`. In this project that source (`webpage-repo`) is cluster-owned and external to
   the application manifests. Do not add a second source or self-managing Kustomization without
   checking the cluster configuration repository. If the application repository is the Flux config
   repository, an existing parent bootstrap Kustomization must apply the source/reconciliation
   resources; see the explicit template and bootstrap warning in the reference.

6. **Validate the whole handoff.** Render `kubernetes/`, run the app build and a local Docker build,
   inspect the image policy and automation status, then force one controlled reconciliation. Confirm
   that Flux commits only the Kustomize image tag and that the Deployment becomes available.

## Troubleshooting order

Follow the artifact flow rather than editing the workload:

1. GitHub Actions: did a trusted `main` run publish the intended `main-N` tag?
2. `ImageRepository`: can Flux authenticate to and scan the registry?
3. `ImagePolicy`: does the tag regex accept the image and select it as `status.latestImage`?
4. `ImageUpdateAutomation`: can it fetch, modify the marker under `./kubernetes`, and push Git?
5. `GitRepository` and Flux `Kustomization`: did they fetch that commit and apply the same path?
6. Deployment: do the image pull secret, Service, startup/readiness/liveness probes, and route work?

Use `flux get` and `flux reconcile` commands from the reference before making configuration changes.
For encrypted manifests, decrypt only in an approved local environment; never commit `*.dec.yaml`
or decrypted hostnames, registry credentials, or other secret values.
