# Use ssl_bump and cache_peer in Squid

## run SSL bumped squid server

```bash
$ docker-compose up -d squid-ssl-bump
$ curl https://example.com -x localhost:3128 -k
```

## run SSL bumped and cache_peered squid server

Using `squid-child` as a foreground squid proxy.

### Using squid parent:3128 (http_port)

```bash
$ docker-compose up -d squid-child
$ curl https://example.com -x localhost:3128 -k
```

It returns following error:

```
<div id="sysmsg">
<p>The system returned:</p>
<blockquote id="data">
<pre>(71) Protocol error (TLS code: SQUID_ERR_SSL_HANDSHAKE)</pre>
<p>Handshake with SSL server failed: error:140770FC:SSL routines:SSL23_GET_SERVER_HELLO:unknown protocol</p>
</blockquote>
</div>

<p>This proxy and the remote host failed to negotiate a mutually acceptable security settings for handling your request. It is possible that the remote host does not support secure connections, or the proxy is not satisfied with the host security credentials.</p>
```

### Using squid parent:3129 (https_port)

Change `squid.conf` of squid-child to use port 3129 of parent squid.

When squid-child forward to parent's https port, then it returns following error:

```
<blockquote id="error">
<p><b>Unable to forward this request at this time.</b></p>
</blockquote>

<p>This request could not be forwarded to the origin server or to any parent caches.</p>

<p>Some possible problems are:</p>
<ul>
<li id="network-down">An Internet connection needed to access this domains origin servers may be down.</li>
<li id="no-peer">All configured parent caches may be currently unreachable.</li>
<li id="permission-denied">The administrator may not allow this cache to make direct connections to origin servers.</li>
</ul>

<p>Your cache administrator is <a href="mailto:root?subject=CacheErrorInfo%20-%20ERR_CANNOT_FORWARD&amp;body=CacheHost%3A%20fb12a44a3b19%0D%0AErrPage%3A%20ERR_CANNOT_FORWARD%0D%0AErr%3A%20%5Bnone%5D%0D%0ATimeStamp%3A%20Thu,%2020%20Oct%202016%2009%3A35%3A54%20GMT%0D%0A%0D%0AClientIP%3A%20172.18.0.1%0D%0AServerIP%3A%20squid%0D%0A%0D%0AHTTP%20Request%3A%0D%0AGET%20%2Faates%20HTTP%2F1.1%0AUser-Agent%3A%20curl%2F7.49.1%0D%0AAccept%3A%20*%2F*%0D%0AHost%3A%20example.com%0D%0A%0D%0A%0D%0A">root</a>.</p>
```

### When use openssl server instead of parent squid

When using openssl server as parent squid server, squid child send empty request.

```bash
$ openssl s_server -cert /etc/squid/ssl/squid.pem -accept 3129
Using default temp DH parameters
Using default temp ECDH parameters
ACCEPT
-----BEGIN SSL SESSION PARAMETERS-----
MFUCAQECAgMDBALAMAQABDBwKep5/u8L449L3hSrMW3ypkRKUziorTQmdOK8vnzE
FN95O89i5oIbQC9dIDxFKeahBgIEWAiQpKIEAgIBLKQGBAQBAAAA
-----END SSL SESSION PARAMETERS-----
Shared ciphers:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-DSS-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA256:DHE-RSA-AES256-SHA:DHE-DSS-AES256-SHA:DHE-RSA-CAMELLIA256-SHA:DHE-DSS-CAMELLIA256-SHA:ECDH-RSA-AES256-GCM-SHA384:ECDH-ECDSA-AES256-GCM-SHA384:ECDH-RSA-AES256-SHA384:ECDH-ECDSA-AES256-SHA384:ECDH-RSA-AES256-SHA:ECDH-ECDSA-AES256-SHA:AES256-GCM-SHA384:AES256-SHA256:AES256-SHA:CAMELLIA256-SHA:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:DHE-DSS-AES128-GCM-SHA256:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES128-SHA256:DHE-DSS-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA:DHE-RSA-SEED-SHA:DHE-DSS-SEED-SHA:DHE-RSA-CAMELLIA128-SHA:DHE-DSS-CAMELLIA128-SHA:ECDH-RSA-AES128-GCM-SHA256:ECDH-ECDSA-AES128-GCM-SHA256:ECDH-RSA-AES128-SHA256:ECDH-ECDSA-AES128-SHA256:ECDH-RSA-AES128-SHA:ECDH-ECDSA-AES128-SHA:AES128-GCM-SHA256:AES128-SHA256:AES128-SHA:SEED-SHA:CAMELLIA128-SHA:ECDHE-RSA-DES-CBC3-SHA:ECDHE-ECDSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:EDH-DSS-DES-CBC3-SHA:ECDH-RSA-DES-CBC3-SHA:ECDH-ECDSA-DES-CBC3-SHA:DES-CBC3-SHA:IDEA-CBC-SHA:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:ECDH-RSA-RC4-SHA:ECDH-ECDSA-RC4-SHA:RC4-SHA:RC4-MD5
CIPHER is ECDHE-RSA-AES256-GCM-SHA384
Secure Renegotiation IS supported
DONE
shutting down SSL
CONNECTION CLOSED
ACCEPT
```

## Using a mitmproxy

Yeah!
mitmproxy can bump https request and set upstream proxy!

```bash
$ docker build -t squid .
$ docker run -p 3128:3128 -t squid

$ cd /tmp
$ mitmproxy -U http://localhost:3128
```
