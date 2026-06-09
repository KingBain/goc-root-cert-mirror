# GoC Root CA Certificate Mirror

This repository provides a stable GitHub-hosted copy of the Government of Canada Root CA certificate.

The upstream certificate is published by the Government of Canada PKI service, but that endpoint can be unreliable from some CI/CD environments. This repository mirrors the certificate so automated tests, dev containers, and build pipelines can download a stable copy from GitHub instead.

## Repository

```text
https://github.com/KingBain/goc-root-cert-mirror
```

## Certificate

The mirrored certificate is stored here:

```text
certs/GoC-GdC-Root-A.crt
```

The SHA-256 fingerprint is stored here:

```text
certs/GoC-GdC-Root-A.sha256-fingerprint
```

## Download the certificate

Use the GitHub raw file URL to download the certificate directly:

```text
https://raw.githubusercontent.com/KingBain/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.crt
```

Download with `curl`:

```sh
curl -fsSL \
  "https://raw.githubusercontent.com/KingBain/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.crt" \
  -o GoC-GdC-Root-A.crt
```

Download with `wget`:

```sh
wget \
  "https://raw.githubusercontent.com/KingBain/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.crt" \
  -O GoC-GdC-Root-A.crt
```

## Download the fingerprint

```text
https://raw.githubusercontent.com/KingBain/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.sha256-fingerprint
```

Download with `curl`:

```sh
curl -fsSL \
  "https://raw.githubusercontent.com/KingBain/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.sha256-fingerprint"
```

Download with `wget`:

```sh
wget -qO- \
  "https://raw.githubusercontent.com/KingBain/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.sha256-fingerprint"
```

## Use in a Dev Container Feature

This mirror can be used as the certificate source for a Dev Container Feature:

```jsonc
"features": {
  "ghcr.io/KingBain/devcontainer-features/enhanced-custom-root-ca:2": {
    "sources": "https://raw.githubusercontent.com/KingBain/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.crt"
  }
}
```

With fingerprint verification:

```jsonc
"features": {
  "ghcr.io/KingBain/devcontainer-features/enhanced-custom-root-ca:2": {
    "sources": "https://raw.githubusercontent.com/KingBain/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.crt",
    "fingerprints": "PASTE_SHA256_FINGERPRINT_HERE"
  }
}
```

## How updates work

This repository is updated by a scheduled GitHub Actions workflow.

The workflow attempts to download the latest certificate from the upstream Government of Canada PKI endpoint. If the download succeeds, the workflow compares the downloaded certificate fingerprint with the certificate currently stored in this repository.

If the fingerprint is the same, nothing changes.

If the fingerprint is different, the workflow commits the updated certificate and fingerprint file.

If the upstream web server is unavailable or fails to provide a valid certificate, the workflow exits successfully and leaves the existing repository copy unchanged.

## Why this repo exists

The upstream PKI endpoint can intermittently fail from CI/CD environments. That makes automated tests unreliable when they depend directly on the upstream server.

This repository provides a stable GitHub-hosted copy of the certificate so automated workflows can use a predictable download path.

## Important note

This repository is a mirror, not the authoritative source.

For validation-sensitive use cases, verify the certificate fingerprint before trusting the certificate.
