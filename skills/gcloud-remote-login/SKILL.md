---
name: gcloud-remote-login
description: "Complete `gcloud auth login` on a machine whose browser the user cannot reach — logging in from a phone over SSH, a headless server, or any pane where the local browser is on someone else's screen. Use when the user must authenticate gcloud remotely, or hits `redirect_uri_mismatch`, `missing_code`, or `insecure_transport` from a gcloud login. Do not use for Application Default Credentials on a machine with a working local browser."
---

# gcloud remote login

Run `gcloud auth login --no-browser` on the headless machine, let the user authorize on another device, and paste the callback URL back by hand. Verified 2026-09-11 with Google Cloud SDK 583.0.0 on macOS.

## Only `--no-browser` works

| Flag | Result |
|---|---|
| `--no-browser` | **Use this.** Waits at `Enter the output of the above command:` with no timeout. |
| *(none)* | Listens on `127.0.0.1:8085` and gives up after about 25–30 s with `(missing_code) Missing code parameter in response`. A phone round trip never makes it, and `auth login` has no `--port` flag. |
| `--no-launch-browser` | Dead: uses the retired out-of-band flow and always fails with `Error 400: redirect_uri_mismatch`. |

## Steps

**0. Pick the machine.** Run everything on the machine that needs the credentials; a devcontainer's `localhost` is not the host's. When the container has no `gcloud`, log in on the host. With herdr-core, add `--on <machine>` from `hc machine ls` to `hc run`, and address the terminal as `<machine>/gcloud-login`.

**1. Start the login in a new terminal you own.**

```bash
hc run --bg --no-notify --name gcloud-login gcloud auth login --no-browser
hc wait gcloud-login --output 'Enter the output'
hc read gcloud-login --lines 40 | tr -d '\n' | grep -o 'remote-bootstrap="[^"]*"'
```

The screen hard-wraps the URL, which is why the lines are joined before extracting it. Without herdr-core, run `gcloud auth login --no-browser` in a shell.

Never `hc send` into a terminal you did not start: a TUI such as k9s takes the text as hotkeys (once that opened a shell in a production pod). If `--name gcloud-login` is taken, `hc get gcloud-login`, and `hc kill` + `hc rm` it only when it is your own stale login.

**2. Add the missing `redirect_uri`.** The bootstrap URL lacks it (a second gcloud normally adds it), and opening it as-is fails with `Missing required parameter: redirect_uri`. Append:

```
&redirect_uri=http%3A%2F%2Flocalhost%3A8085%2F
```

Optional check before sending: `curl -sS -o /dev/null -w '%{http_code} %{redirect_url}\n' "<URL>"`. A 302 to `accounts.google.com/v3/signin/identifier` is good; a 302 to `/signin/oauth/error` is not. Give the user the URL as a bare tappable link, outside a code fence.

**3. The user authorizes.** Their browser lands on `http://localhost:8085/?state=…&code=…` and fails to load. Tell them the failure is expected and to copy the whole address bar.

**4. Paste it back as `https://`.** Replace a leading `http://`, or prepend `https://` when the copy has no scheme (mobile Safari and Chrome drop it). oauthlib rejects an `http` callback with `(insecure_transport) OAuth 2 MUST utilize https`; gcloud only reads `code` and `state` from the string, so the scheme change is harmless. Do not set `OAUTHLIB_INSECURE_TRANSPORT=1` instead: Claude Code's auto-mode classifier blocks it.

```bash
hc send gcloud-login 'https://localhost:8085/?state=...&code=...'
hc wait gcloud-login
hc read gcloud-login --lines 20
hc rm gcloud-login
```

**5. Verify:** `gcloud auth list` and `gcloud config get-value project`.

## When it fails

- Restart from step 1 on any failure. Every run mints a new `state` and PKCE challenge, so a `code` from an earlier run is useless.
- The `code` is single-use and expires within minutes: paste it as soon as it arrives, and never echo it back into the conversation.
- `missing_code` on the plain flow is gcloud's own timeout, not a port conflict (checked with `python3 -m http.server 8085` and `BROWSER=echo`). Don't hunt for one.
- To abort a hung login: `hc interrupt gcloud-login`.

## Automation needs a service account

Cloud SDK sessions expire after 16 hours, so this login has to be repeated about daily. For anything long-running in a container or automation, recommend `gcloud auth activate-service-account --key-file=KEY.json` instead (no browser, no expiry), and keep the key file out of the repo.
