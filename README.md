# riegan-ublue

Custom Fedora Atomic image based on [Bluefin NVIDIA](https://projectbluefin.io/) with development tools, gaming support, productivity apps, and CUDA.

## Features

- **Base**: Bluefin NVIDIA (Fedora Atomic with NVIDIA drivers)
- **Development**: VSCode, Git, GCC, Clang, Rust, Go, Node.js, Python, Neovim, Homebrew
- **Gaming**: Steam, Lutris, GameMode, MangoHud
- **Productivity**: LibreOffice, GIMP, OBS Studio, Obsidian, Discord
- **AI/ML**: CUDA via RPM Fusion (xorg-x11-drv-nvidia-cuda)
- **Dotfiles**: Chezmoi auto-sync from GitHub

## Installation

### From existing Fedora Atomic / Universal Blue

```bash
rpm-ostree rebase ostree-unverified-registry:quay.io/riegan/riegan-ublue:latest

systemctl reboot

rpm-ostree rebase ostree-image-signed:docker://quay.io/riegan/riegan-ublue:latest

systemctl reboot
```

## Setup (For Building)

### 1. Fork/Clone this repository

### 2. Generate signing keys (cosign)

```bash
# Install cosign
# Fedora: sudo dnf install cosign
# Brew: brew install cosign

# Generate key pair (DO NOT add password)
cosign generate-key-pair
```

### 3. Create Quay.io Robot Account

1. Go to [quay.io](https://quay.io) and sign in
2. Create a new repository named `riegan-ublue`
3. Go to Account Settings → Robot Accounts → Create Robot Account
4. Give it write permissions to your repository
5. Note the username (format: `yourusername+robotname`) and token

### 4. Add secrets to GitHub

Add these secrets in Settings → Secrets and Variables → Actions:

| Secret Name | Value |
|-------------|-------|
| `SIGNING_SECRET` | Contents of `cosign.key` file |
| `QUAY_USERNAME` | Your Quay.io username (e.g., `riegan`) |
| `QUAY_TOKEN` | Robot account token from step 3 |

```bash
# Using GitHub CLI
gh secret set SIGNING_SECRET < cosign.key
gh secret set QUAY_USERNAME
gh secret set QUAY_TOKEN
```

### 5. Commit public key

```bash
git add cosign.pub
git commit -m "Add container signing public key"
git push
```

## Verification

```bash
cosign verify --key cosign.pub quay.io/riegan/riegan-ublue:latest
```

## Customization

Edit `recipes/recipe.yml` to:
- Add/remove packages in the `dnf` module
- Add/remove Flatpak apps in `default-flatpaks`
- Add custom files in `files/system/`

## Resources

- [BlueBuild Documentation](https://blue-build.org)
- [Bluefin Project](https://projectbluefin.io/)
- [Universal Blue](https://universal-blue.org/)
