# Cloudflare Dynamic DNS Updater

_Simple Bash Script to update your Cloudflare DNS._

This Script is also as Docker Container available here:

- Docker Hub: [ellerbrock/alpine-cloudflare-dyndns](https://hub.docker.com/r/ellerbrock/alpine-cloudflare-dyndns/)
- Quay: [ellerbrock/alpine-cloudflare-dyndns](https://quay.io/repository/ellerbrock/alpine-cloudflare-dyndns)

## Parameter

- `EXTERNAL_IP`: optional, if not set it uses the current external ip addr
- `CF_EMAIL`: required, your cloudflare login email
- `CF_TOKEN`: required, your cloudflare api key (you can find it under [Settings](https://www.cloudflare.com/a/account/my-account) -> `API Key` -> `Global API Key`)
- `CF_ZONE_NAME` required, your root domain name (eg. example.tld)
- `CF_DOMAIN_NAME` required, your domain or subdomain name you want to update (eg. subdomain.example.tld)


## Cloudflare Script

```bash
#!/usr/bin/env sh

# Developer: Maik Ellerbrock <opensource@frapsoft.com>
#
# GitHub:  https://github.com/ellerbrock
# Twitter: https://twitter.com/frapsoft
# Docker:  https://hub.docker.com/u/ellerbrock
# Quay:    https://quay.io/user/ellerbrock

# Required Parameter
CF_EMAIL=""
CF_TOKEN=""
CF_ZONE_NAME=""
CF_DOMAIN_NAME=""

# Optional Parameter
EXTERNAL_IP=$(curl -s https://api.ipify.org)

# we get them automatically for you
CF_ZONEID=""
CF_DOMAINID=""

function getZoneID() {
  CF_ZONEID=$(curl -s \
    -X GET "https://api.cloudflare.com/client/v4/zones?name=${CF_ZONE_NAME}" \
    -H "X-Auth-Email: ${CF_EMAIL}" \
    -H "X-Auth-Key: ${CF_TOKEN}" \
    -H "Content-Type: application/json" | \
    jq -r .result[0].id)
}

function getDomainID() {
  CF_DOMAINID=$(curl -s \
    -X GET "https://api.cloudflare.com/client/v4/zones/${CF_ZONEID}/dns_records?name=${CF_DOMAIN_NAME}" \
    -H "X-Auth-Email: ${CF_EMAIL}" \
    -H "X-Auth-Key: ${CF_TOKEN}" \
    -H "Content-Type: application/json" | \
  jq -r .result[0].id)
}

function updateDomain() {
  curl -s \
    -X PUT "https://api.cloudflare.com/client/v4/zones/${CF_ZONEID}/dns_records/${CF_DOMAINID}" \
    -H "X-Auth-Email: ${CF_EMAIL}" \
    -H "X-Auth-Key: ${CF_TOKEN}" \
    -H "Content-Type: application/json" \
    --data '{"type":"A","name":"'${CF_DOMAIN_NAME}'","content":"'${EXTERNAL_IP}'","ttl":1,"proxied":false}'
}

getZoneID
getDomainID
updateDomain
```

## Contact / Social Media

_Get the latest News about Web Development, Open Source, Tooling, Server & Security_

[![Github](https://github.frapsoft.com/social/github.png)](https://github.com/ellerbrock/)[![Docker](https://github.frapsoft.com/social/docker.png)](https://hub.docker.com/u/ellerbrock/)[![npm](https://github.frapsoft.com/social/npm.png)](https://www.npmjs.com/~ellerbrock)[![Twitter](https://github.frapsoft.com/social/twitter.png)](https://twitter.com/frapsoft/)[![Facebook](https://github.frapsoft.com/social/facebook.png)](https://www.facebook.com/frapsoft/)[![Google+](https://github.frapsoft.com/social/google-plus.png)](https://plus.google.com/116540931335841862774)[![Gitter](https://github.frapsoft.com/social/gitter.png)](https://gitter.im/frapsoft/frapsoft/)
