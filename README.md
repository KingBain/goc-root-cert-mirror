# GoC Root CA Certificate Mirror
[![Update GoC Root CA](https://github.com/gccloudone-aurora-collab/goc-root-cert-mirror/actions/workflows/update-goc-root-ca.yml/badge.svg)](https://github.com/gccloudone-aurora-collab/goc-root-cert-mirror/actions/workflows/update-goc-root-ca.yml)

This repository provides a stable GitHub-hosted copy of the Government of Canada Root CA certificate.

The upstream certificate is published by the Government of Canada PKI service, but that endpoint can be unreliable from some CI/CD environments. This repository mirrors the certificate so automated tests, dev containers, and build pipelines can download a stable copy from GitHub instead.

## GC Service Page

<https://gcxgce.sharepoint.com/teams/10003392/SitePages/Device-Certificates.aspx>

## Repository

```text
https://github.com/gccloudone-aurora-collab/goc-root-cert-mirror
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
https://raw.githubusercontent.com/gccloudone-aurora-collab/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.crt
```

Download with `curl`:

```sh
curl -fsSL \
  "https://raw.githubusercontent.com/gccloudone-aurora-collab/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.crt" \
  -o GoC-GdC-Root-A.crt
```

Download with `wget`:

```sh
wget \
  "https://raw.githubusercontent.com/gccloudone-aurora-collab/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.crt" \
  -O GoC-GdC-Root-A.crt
```

## Download the fingerprint

```text
https://raw.githubusercontent.com/gccloudone-aurora-collab/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.sha256-fingerprint
```

Download with `curl`:

```sh
curl -fsSL \
  "https://raw.githubusercontent.com/gccloudone-aurora-collab/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.sha256-fingerprint"
```

Download with `wget`:

```sh
wget -qO- \
  "https://raw.githubusercontent.com/gccloudone-aurora-collab/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.sha256-fingerprint"
```

## Use in a Dockerfile

The mirrored certificate can also be downloaded and installed directly in a Dockerfile.

For Debian or Ubuntu based images:

```dockerfile
FROM ubuntu:24.04

RUN apt-get update \
    && apt-get install -y --no-install-recommends ca-certificates curl \
    && curl -fsSL \
        "https://raw.githubusercontent.com/gccloudone-aurora-collab/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.crt" \
        -o /usr/local/share/ca-certificates/GoC-GdC-Root-A.crt \
    && update-ca-certificates \
    && rm -rf /var/lib/apt/lists/*
```

For Alpine based images:

```dockerfile
FROM alpine:latest

RUN apk add --no-cache ca-certificates curl \
    && curl -fsSL \
        "https://raw.githubusercontent.com/gccloudone-aurora-collab/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.crt" \
        -o /usr/local/share/ca-certificates/GoC-GdC-Root-A.crt \
    && update-ca-certificates
```

This installs the mirrored certificate into the container trust store so tools inside the image can trust certificates chained to the Government of Canada Root CA.


## Use in a Dev Container Feature

This mirror can be used as the certificate source for a Dev Container Feature:

```jsonc
"features": {
  "ghcr.io/gccloudone-aurora-collab/devcontainer-features/enhanced-custom-root-ca:2": {
    "sources": "https://raw.githubusercontent.com/gccloudone-aurora-collab/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.crt"
  }
}
```

With fingerprint verification:

```jsonc
"features": {
  "ghcr.io/gccloudone-aurora-collab/devcontainer-features/enhanced-custom-root-ca:2": {
    "sources": "https://raw.githubusercontent.com/gccloudone-aurora-collab/goc-root-cert-mirror/main/certs/GoC-GdC-Root-A.crt",
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
