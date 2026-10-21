# Costkeel releases

Costkeel shows where your AWS money goes and what you could stop paying for. It reads your
consolidated bill through a read-only role in your payer account and runs on your own
infrastructure with Docker Compose. Nothing in your AWS account is changed.

This repository holds the **release files** for self-hosted installs. The source code is not
public.

## What each release contains

| File | What it is |
|---|---|
| `compose.yaml` | The Docker Compose file, pinned to that release's image |
| `backup.sh`, `restore.sh` | Back up and restore the database and data volume |
| `costkeel-access.yaml` | CloudFormation: the read-only role and billing export in your payer account |
| `costkeel-member-access.yaml` | CloudFormation StackSet (optional): read-only RDS and EKS describe calls in member accounts, for end-of-support warnings |
| `costkeel-terraform.tar.gz` | The same two as Terraform modules |
| `INSTALL.md` | The full install and upgrade guide |

The image is `ghcr.io/costkeel/costkeel`, for amd64 and arm64.

Latest release: [Releases › Latest](https://github.com/Costkeel/releases/releases/latest).

## Before you start

- A **licence file** from Sarv Systems. Without one, Costkeel is read-only.
- A Linux host with Docker Engine 24+ and Docker Compose v2. 2 vCPU and 2 GiB RAM (for example,
  an AWS t4g.small) with 20 GB disk covers most organisations. `INSTALL.md` sizes larger bills.
- An IAM role on the host (an EC2 instance profile or an ECS task role).
- HTTPS in front of it (a reverse proxy, a load balancer or a Cloudflare tunnel).
- Someone who can deploy a CloudFormation stack in your payer (management) account.

The [latest release](https://github.com/Costkeel/releases/releases/latest)'s `INSTALL.md` has
every step, about an hour of work. AWS then takes up to 24 hours to write the first billing
export.

## Verify the image

Each image is signed in the release workflow, with no long-lived keys. Check it before you run
it:

```bash
cosign verify ghcr.io/costkeel/costkeel:<version> \
  --certificate-identity-regexp 'https://github.com/Costkeel/app/.github/workflows/release.yml@refs/tags/v.*' \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com
```

Each release's notes also give its image digest, and the image carries an SBOM and build
provenance.

## Upgrade

1. Back up: `./backup.sh /path/to/backups`.
2. Download the new release's `compose.yaml`, or set `COSTKEEL_VERSION` to the new version.
3. Run `docker compose pull` and then `docker compose up -d`. The `migrate` service updates the
   database before the app starts.

If a release ships a new access template version, its notes say so, and Costkeel's Access and
scope page shows the deployed version against the shipped one.

## Outbound calls

Costkeel calls AWS read-only APIs and nothing else, unless you turn something on: SMTP, Slack,
Microsoft Teams, Jira, a webhook, or Google or Microsoft sign-in. The full list is in
`INSTALL.md`.

## Support

Contact Sarv Systems, and include the diagnostics file from
**Settings › Plan and licence › Download diagnostics**. It holds versions, states and recent
errors: no figures, resource names, ARNs, account IDs or email addresses.

Costkeel is made by Sarv Systems.
