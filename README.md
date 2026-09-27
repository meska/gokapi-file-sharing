# Gokapi File Sharing

An OpenClaw skill for sending files to other people through a Gokapi instance, or uploading to Gokapi on request. It asks for retention before upload, then returns or delivers the download link.

[Install from ClawHub](https://clawhub.ai/meska/skills/gokapi-file-sharing) or use the `SKILL.md` and `references/` directory in this repository.

## Setup

The skill needs two separate protected-store entries:

- `GOKAPI_BASE_URL`: the HTTPS base URL of your own Gokapi instance. Store it through the local secret-store CLI or Settings, then bind its store SecretRef to `skills.entries.gokapi-file-sharing.apiKey`. The skill's `primaryEnv` maps this generic OpenClaw config field to `GOKAPI_BASE_URL`; do not use its API-key-labeled prompt to collect the URL.
- `GOKAPI_API_KEY`: a Gokapi API key with `UPLOAD` permission. The skill requests this value through OpenClaw's masked secrets prompt. Allow egress only to the exact hostname of your instance.

Gateway secret egress must be enabled. Do not paste either value into chat or into this repository. The skill uses Gateway-host execution for uploads; native/sandbox shells do not receive protected credentials.

Gokapi's API accepts retention in whole days. Downloads are unlimited within the chosen retention unless a limit is requested. The ordinary and chunked API endpoints do **not** provide end-to-end encryption.

## Files

- `SKILL.md`: activation and sharing workflow.
- `references/api-upload.md`: API fields, response checks, and chunked-upload fallback.

Licensed under MIT-0.
