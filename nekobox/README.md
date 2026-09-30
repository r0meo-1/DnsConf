# DNS AI + Travelpayouts for NekoBox

`Bypass_Russia_Payouts.json` is the routing profile. Its updater preserves proxy
routing for Travelpayouts and emrldco. It does not install DNS settings.

`DNS_AI_Travelpayouts.json` is a separate DNS object for the installed
sing-box 1.13.19 core (typed DNS servers, supported since 1.12).

- Default: DNS AI DoH, directly connected so DNS AI sees the local region.
- Travelpayouts service domains: Cloudflare DoH through the existing `proxy`.
  This is a DNS-only exception; it does not add or change traffic routing rules.
- `travelpayouts.com` and all its subdomains cover the website, dashboard,
  passport/login, API, support, academy, widgets and email-link hosts.
- `tp.media` and its subdomains cover affiliate redirects and widget scripts;
  `tp.st` and its subdomains cover short affiliate links.
- `emrldco.com` and its subdomains retain DNS coverage for the legacy redirect
  domain already present in the existing routing profile.
- The exact host `travelpayouts.github.io` covers the official Data API reference.
  Other GitHub Pages sites do not match this exception. External brand websites
  and third-party login/CDN services continue using their existing settings.
- Both A and AAAA queries follow the same exception; no query-type restriction.
- DNS AI endpoint bootstrap uses the published IPv4/IPv6 addresses locally, with
  TLS certificate verification for dns.dns-ai.ru. No plaintext DNS bootstrap.
- Cloudflare uses 1.1.1.1 with verified TLS name cloudflare-dns.com.
- No global Cloudflare fallback. Unrelated domains keep DNS AI filtering.
- Resolver aliases `dns-direct`, `dns-remote`, and `direct` also use DNS AI.
  NekoBox-generated outbound resolver references require these tags. Typed HTTPS
  servers connect directly by default; an explicit detour to an empty direct
  outbound is invalid at runtime and is intentionally omitted.

## Install / Установка

1. Copy/export the current routing and DNS settings as a backup.
2. Open NekoBox: **Маршрутизация → DNS**.
3. Enable **Использовать DNS-объект**.
4. Replace the entire JSON editor content with `DNS_AI_Travelpayouts.json`.
   Paste the object itself; do not wrap it inside a `dns` property.
5. Click **Проверка форматирования**, then **OK**. Keep default DNS tag `remote`.
6. Save all parent dialogs, stop and start the connection, then reopen Travelpayouts.

For a DNS-only update, leave **routing** unchanged. Do not import or update
`Bypass_Russia_Payouts.json` as part of these instructions. The DNS object expects
your current configuration to have an outbound tagged `proxy`, as before.

Raw file:
https://raw.githubusercontent.com/r0meo-1/DnsConf/main/nekobox/DNS_AI_Travelpayouts.json

The routing profile's manual update button does not update this separate DNS
object. A change here requires pasting the new object again.

## Verification and limits

The installed core accepted and started a temporary full configuration containing
this DNS object, a direct outbound, an unused SOCKS proxy placeholder and an
explicit default/outbound resolver reference to `dns-direct`. The temporary core
had no listening inbound or TUN and was stopped after startup. This verifies
parsing and service initialization; it does not test the active VLESS proxy,
UI import or live site login.

On 2026-09-30, the expanded object passed `check` and reached `sing-box started`
with the installed 1.13.19 core in this same isolated setup. Separate direct
Cloudflare DoH checks returned nonzero A records for dashboard, login, API,
support, email-link, widget, short-link, legacy redirect and API documentation
hosts. These requests verify the resolver's answers, not the active proxy path.
The system resolver returned `0.0.0.0` for `api.travelpayouts.com`, and the
homepage failed to resolve before applying the update. Recheck live access
after importing and restarting; no live NekoBox settings were edited here.

If access still fails, inspect Windows hosts overrides and browser DNS cache
separately. The OS/browser may consult a hosts override first. This DNS-only
repository change does not edit Windows hosts, system DNS or browser settings.

To revert, restore the backed-up DNS object or turn off the custom DNS-object
checkbox and restore both normal DNS fields to https://dns.dns-ai.ru/dns-query.
Restart the connection. DNS AI filtering will again apply to Travelpayouts.

References:
- https://dns-ai.ru/#setup
- https://sing-box.sagernet.org/configuration/dns/server/https/
- https://sing-box.sagernet.org/configuration/dns/server/hosts/
- https://developers.cloudflare.com/1.1.1.1/encryption/dns-over-https/make-api-requests/
- https://support.travelpayouts.com/hc/en-us/articles/26856689805586
- https://support.travelpayouts.com/hc/en-us/articles/12729746524050
- https://travelpayouts.github.io/slate/
