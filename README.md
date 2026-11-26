# restart-wifi-network

A [Raycast script command](https://github.com/raycast/script-commands) that restarts a Google Nest Wi-Fi network using the same private API as the Google Home app.

## How it works

1. **Setup:** Exchanges an `oauth_token` cookie from Google's `EmbeddedSetup` sign-in page for a long-lived master token (via [`gpsoauth`](https://github.com/simon-weber/gpsoauth)), lists the account's Wi-Fi networks from the Google Home Foyer API, and saves the credentials and chosen network ID to the macOS Keychain.
2. **Restart:** Uses the master token to get a short-lived access token scoped to the Google Home app, then calls `POST /v2/groups/{group_id}/reboot` on `googlehomefoyer-pa.googleapis.com`.

## Requirements

- macOS (credentials are stored with `/usr/bin/security`)
- [uv](https://docs.astral.sh/uv/) (the script declares its dependencies inline with [PEP 723](https://peps.python.org/pep-0723/) metadata)
- A Google account that owns the Wi-Fi network

## Usage

```sh
./restart-wifi-network.py --setup  # Sign in and choose the network (once)
./restart-wifi-network.py          # Restart the network
```

To use it from Raycast, add this directory under Extensions → Script Commands → Script Folders.

### Setup steps

1. Run `./restart-wifi-network.py --setup` and enter the Google account email address.
2. Open <https://accounts.google.com/EmbeddedSetup> in a browser and sign in with that account.
3. Click "I agree" (the page won't redirect, which is expected).
4. Copy the `oauth_token` cookie value from Developer Tools → Application → Cookies → `accounts.google.com` and paste it into the prompt.
5. If the account has more than one network, choose the one to restart.

The configuration is saved as a generic password in the Keychain under the service name `restart-wifi-network`. To remove it:

```sh
security delete-generic-password -s restart-wifi-network
```
