# DNS AI + Travelpayouts for NekoBox

`Bypass_Russia_Payouts.json` is the routing profile. Its updater preserves proxy
routing for Travelpayouts and emrldco. It does not install DNS settings.

`DNS_AI_Travelpayouts.json` is a separate DNS object for the installed
sing-box 1.13.19 core (typed DNS servers, supported since 1.12).

- Default: DNS AI DoH, directly connected so DNS AI sees the local region.
- Only travelpayouts.com and its subdomains: Cloudflare DoH through `proxy`.
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
Default DNS AI returned 0.0.0.0 for travelpayouts.com and passport.travelpayouts.com
before this exception. Recheck live resolution after importing and restarting.

Windows currently has a separate hosts override for app.travelpayouts.com. This
repository change does not remove it, and the OS/browser may consult it first.
If access still fails, inspect that override and browser DNS cache separately.

To revert, restore the backed-up DNS object or turn off the custom DNS-object
checkbox and restore both normal DNS fields to https://dns.dns-ai.ru/dns-query.
Restart the connection. DNS AI filtering will again apply to Travelpayouts.

References:
- https://dns-ai.ru/#setup
- https://sing-box.sagernet.org/configuration/dns/server/https/
- https://sing-box.sagernet.org/configuration/dns/server/hosts/
- https://developers.cloudflare.com/1.1.1.1/encryption/dns-over-https/make-api-requests/
