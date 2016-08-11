tutum-docker-hipache
====================

Extends the official `hipache:0.3.1` image to provide configuration at runtime via environment variables.

The configuration file used is:

    {
        "server": {
            "debug": {{ SERVER_DEBUG }},
            "workers": {{ SERVER_WORKERS }},
            "maxSockets": {{ SERVER_MAXSOCKETS }},
            "tcpTimeout": {{ SERVER_TCPTIMEOUT }},
            "deadBackendTTL": {{ SERVER_DEADBACKENDTTL }},
            "retryOnError": {{ SERVER_RETRYONERROR }},
            "accessLog": "{{ SERVER_ACCESSLOG }}",
            "httpKeepAlive": {{ SERVER_HTTPKEEPALIVE }},
            "deadBackendOn500": {{ SERVER_DEADBACKENDON500 }},
            "staticDir": null
            "lruCache": {
                "size": {{ SERVER_LRUCACHE_SIZE }},
                "ttl": {{ SERVER_LRUCACHE_TTL }}
            },
        },
        "http": {
            "port": {{ SERVER_HTTP_PORT }},
            "bind": ["{{ SERVER_HTTP_BIND }}"]
        },
        "https": {
            "port": {{ SERVER_HTTPS_PORT }},
            "bind": ["{{ SERVER_HTTPS_BIND }}"]
            "ca": [],
            "secureProtocol": "SSLv23_method",
            "secureOptions": 50331648,
            "key": "{{ SERVER_HTTPS_KEY }}",
            "cert": "{{ SERVER_HTTPS_CERT }}",
            "passphrase": {{ SERVER_SSL_PASSPHRASE }},
            "ciphers": "DH+ECDSA+AESGCM EECDH+aRSA+AESGCM EECDH+ECDSA+SHA384 EECDH+ECDSA+SHA256 EECDH+a RSA+SHA384 EECDH+aRSA+SHA256 EECDH+aRSA+RC4 EECDH EDH+aRSA RC4 !aNULL !eNULL !LOW !3DES !MD5 !EXP !PSK !SRP !DSS !RC4",
            "honorCipherOrder": true
        },
        "driver": "{{ DRIVER }}"
        "user": "{{ SERVER_USER }}",
        "group": "{{ SERVER_GROUP }}"
    }

You can override settings via environment variables when launching the container:

	docker run -d -p 80:80 -e DRIVER="redis://:password@externalredishost" tutum/hipache

The default values are defined in `Dockerfile`:

    ENV SERVER_ACCESSLOG /var/log/hipache-access.log
    ENV SERVER_WORKERS 10
    ENV SERVER_MAXSOCKETS 100
    ENV SERVER_DEADBACKENDTTL 30
    ENV SERVER_TCPTIMEOUT 30
    ENV SERVER_RETRYONERROR 3
    ENV SERVER_DEADBACKENDON500 false
    ENV SERVER_HTTPKEEPALIVE false
    ENV SERVER_LRUCACHE_SIZE 5
    ENV SERVER_LRUCACHE_TTL 5
    ENV SERVER_HTTP_PORT 80
    ENV SERVER_HTTP_BIND 0.0.0.0
    ENV SERVER_HTTPS_PORT 43
    ENV SERVER_HTTPS_BIND
    ENV SERVER_USER www-data
    ENV SERVER_GROUP www-data
    ENV SERVER_DEBUG false
    ENV SERVER_HTTPS_KEY /etc/ssl/ssl.key
    ENV SERVER_HTTPS_CERT /etc/ssl/ssl.crt
    ENV SERVER_SSL_PASSPHRASE undefined
    ENV DRIVER redis:


Linking a redis container
-------------------------

You can also link a redis container using `redis` as a link name to automatically configure the `driver` setting:

	docker run -d -p 80:80 --link redis:redis tutum/hipache

In this case, there are two other environment variables that apply:

* `REDIS_ENV_REDIS_PASS` (optional) with the password to authenticate to the redis instance. Set automatically if using `tutum/redis` as the image for the redis container.
* `REDIS_DB` with the redis database to use (default: `0`)
