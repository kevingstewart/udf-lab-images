## NGINX 1.27 with PQC Support

#### Description

NGINX 1.27 web server configured to support post-quantum cryptography. The web server exposes two ports/listeners:
- Port 443: Typical (non-PQC) website with sample travel agency content
- Port 444: PQC site with simple cipher info output

Tags: https://hub.docker.com/r/kevingstewart/udf-nginx-pqc/tags

#### Usage
```bash
docker run -d --name pqc-nginx --rm -p 9443:443 -p 9444:444 kevingstewart/udf-nginx-pqc:<tag>
```

#### NGINX Configuration
/opt/nginx/nginx.conf
```bash
user  www-data;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}
http {
    server {
        listen                  0.0.0.0:443 ssl;
        server_name             webserver.local;
        ssl_certificate         /etc/server.crt;
        ssl_certificate_key     /etc/server.key;
        location ./images/ {
            root                /var/www/site/html/images;
        }
        location / {
            root                /var/www/site/html;
            index               index.html;
            include             /opt/nginx/mime.types;
        }
    }
    server {
        listen                  0.0.0.0:444 ssl;
        ssl_certificate         /etc/server.crt;
        ssl_certificate_key     /etc/server.key;
        ssl_protocols           TLSv1.3;
        ssl_ecdh_curve          X25519MLKEM768;

        location / {
            default_type text/html;
            return 200 "<html><head><title>PQC Test Success!</title></head><body><H2>PQC Test Success!</H1><p><b>Negotiated Protocol</b>: \$ssl_protocol </p><p><b>Negotiated Cipher</b>: \$ssl_cipher </p><p><b>Negotiated Curve</b>: \$ssl_curve </p><p><b>Supported Curves</b>: \$ssl_curves </p></body></html>";
        }
    }
}
```


#### Container Build
```bash
git clone https://github.com/kevingstewart/udf-lab-images.git
cd udf-lab-images/pqc-nginx
docker build --no-cache -t udf-nginx-pqc:<tag> .
docker run -d --name pqc-nginx --rm -p 9443:443 -p 9444:444 udf-nginx-pqc:<tag>
```

#### References
- Dockerfile: https://github.com/kevingstewart/udf-lab-images/tree/main/pqc-nginx
- Additional PQC Ciphers: https://github.com/open-quantum-safe/oqs-provider
- PQC on NGINX: https://github.com/linode/docs/blob/develop/docs/guides/security/encryption/post-quantum-encryption-nginx-ubuntu2404/index.md

#### Additional
To update the ciphers:
- Console shell to the running container (```docker exec -it <container-name> /bin/bash```)
- Install a console editor of choice (ex. ```apt update && apt install -y vim```)
- Edit /opt/nginx/nginx.conf
- Update the **ssl_ecdh_curve** value per the *oqc-provider* reference above
- Save and close the config file, reload nginx (```nginx -s reload```)

