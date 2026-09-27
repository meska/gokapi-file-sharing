# Gokapi File Sharing

An OpenClaw skill for sending files to other people through a Gokapi instance, uploading on request, or revoking a share link. It asks for retention and an optional download limit before upload, then returns or delivers the link.

[Install from ClawHub](https://clawhub.ai/meska/skills/gokapi-file-sharing) or use the `SKILL.md` and `references/` directory in this repository.

## Setup

The skill needs two separate store entries:

- `GOKAPI_BASE_URL`: the HTTPS base URL of your own Gokapi instance. Store it as an **env-kind** entry through the local secret-store CLI or Settings, then bind its store SecretRef to `skills.entries.gokapi-file-sharing.apiKey`. The skill's `primaryEnv` maps this generic OpenClaw config field to `GOKAPI_BASE_URL`; do not use its API-key-labeled prompt to collect the URL. A secret-kind entry is opaque to the command and cannot serve as a request destination.
- `GOKAPI_API_KEY`: a Gokapi API key with `UPLOAD` permission. Revocation also needs `DELETE`; metadata verification uses `VIEW`. The skill requests this value through OpenClaw's masked secrets prompt. Allow egress only to the exact hostname of your instance.

Gateway secret egress must be enabled. Keep both values out of this repository and never paste the API key into chat. The env-kind URL is locally readable; the API key remains protected and is available only to Gateway-host execution, not native/sandbox shells.

Gokapi's API accepts retention in whole days. Downloads are unlimited within the chosen retention unless a limit is requested; a positive limit disables the link after that many file downloads. The ordinary and chunked API endpoints do **not** provide end-to-end encryption.

## Files

- `SKILL.md`: activation and sharing workflow.
- `references/api-upload.md`: API fields, response checks, chunked-upload fallback, and link-management notes.

Licensed under MIT-0.
