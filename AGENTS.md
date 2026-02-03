# AGENTS.md

This repository contains a BlueBuild custom OS image configuration.

## Project Structure

```
riegan-ublue/
├── .devcontainer/devcontainer.json  # Dev container for local builds
├── .github/workflows/build.yml      # GitHub Actions workflow (Quay.io)
├── recipes/recipe.yml               # Main BlueBuild recipe
├── files/system/                    # Custom system files copied to /
├── cosign.pub                       # Container signing public key
└── README.md                        # Setup instructions
```

## Key Files

### recipes/recipe.yml
The main configuration file. Follows BlueBuild recipe schema.
- Base image: `ghcr.io/ublue-os/bluefin-nvidia`
- Modules: dnf, default-flatpaks, brew, chezmoi, signing

### .github/workflows/build.yml
GitHub Actions workflow that builds and pushes to Quay.io.
Required secrets: `SIGNING_SECRET`, `QUAY_USERNAME`, `QUAY_TOKEN`

## BlueBuild Conventions

- Recipe schema: https://schema.blue-build.org/recipe-v1.json
- Documentation: https://blue-build.org
- Modules reference: https://blue-build.org/reference/module/

## Common Tasks

### Add a package
Edit `recipes/recipe.yml`, add to `dnf.install.packages` list.

### Add a Flatpak
Edit `recipes/recipe.yml`, add to `default-flatpaks.configurations[0].install` list.

### Add custom files
Place files in `files/system/` mirroring the target path (e.g., `files/system/etc/foo` → `/etc/foo`).

## Build

Manual trigger only via GitHub Actions workflow_dispatch or local devcontainer build.

### Local Build (Devcontainer)
```bash
bluebuild build ./recipes/recipe.yml
```

### Local Build & Push
```bash
bluebuild build --push ./recipes/recipe.yml \
  --registry quay.io \
  --registry-namespace riegan \
  --signing-key /run/secrets/cosign.key
```

Image publishes to: `quay.io/riegan/riegan-ublue:latest`
