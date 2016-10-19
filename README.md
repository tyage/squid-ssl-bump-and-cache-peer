# Use ssl_bump and cache_peer in Squid

## run SSL bumped squid server

```sh
$ docker-compose up -d squid-ssl-bump
$ curl https://example.com -x localhost:3128 -k
```
