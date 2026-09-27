# API upload reference

Use the instance's /apidocumentation/openapi.json as the authoritative schema. API base path: /api/. Authentication: apikey HTTP header with UPLOAD permission.

Validate GOKAPI_BASE_URL as HTTPS without userinfo, query, or fragment; normalize a trailing slash. GOKAPI_API_KEY is the Gateway-host exec inherited protected-store sentinel. Do not copy it into literal arguments, temporary files, logs, or native/sandbox shell. Do not follow redirects.

Ordinary upload: POST /api/files/add as multipart/form-data with file=@<absolute-path>, expiryDays=<chosen integer>, allowedDownloads=<chosen integer>. Command shape (set FILE_PATH with shell-safe quoting and numeric values; never paste secrets):

    curl --silent --show-error --fail --max-redirs 0 \
      --header "apikey: $GOKAPI_API_KEY" \
      --form "file=@$FILE_PATH" \
      --form "expiryDays=$EXPIRY_DAYS" \
      --form "allowedDownloads=$ALLOWED_DOWNLOADS" \
      "$GOKAPI_BASE_URL/api/files/add"

Parse JSON: Result must be OK; FileInfo.UrlDownload must be HTTPS; FileInfo.ExpireAt and UnlimitedTime must match the intended retention. Do not construct the public link from the ID when UrlDownload is supplied.

For large files or a proxy request-size failure, inspect GET /api/info/config (UPLOAD permission) for MaxFilesize and MaxChunksize in MB. Stop if the total file exceeds MaxFilesize; otherwise use the documented chunk workflow:

1. Generate one UUID of at least 10 characters per file. Split the source into sequential chunks no larger than MaxChunksize; every non-final chunk must be at least 5 MB.
2. POST each chunk to /api/chunk/add as multipart/form-data: file=<chunk bytes>, uuid=<same UUID>, filesize=<total bytes>, offset=<byte offset>. Require Result=OK for each response before continuing. Preserve the source; obey local temporary-storage policy if staging is needed.
3. POST /api/chunk/complete with headers uuid, filename (for non-ANSI names, base64: followed by Base64 of the UTF-8 filename), filesize, expiryDays, allowedDownloads, and optional contenttype. Require Result=OK and verify the returned FileInfo.
4. After an ambiguous chunk or completion response, do not restart blindly: check server state or report uncertainty. Reusing a UUID for a fresh attempt may duplicate or corrupt an upload.

The API defaults to 14 days and one download when those fields are omitted; always supply the chosen values. Password protection is available but a plaintext password must not appear in a logged command; use a supported secret-handling path or stop.
