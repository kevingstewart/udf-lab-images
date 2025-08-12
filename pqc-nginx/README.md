## NGINX 1.27 with PQC Support

#### Description

NGINX 1.27 web server configured to support post-quantum cryptography. The web server exposes two ports/listeners:
- Port 443: Typical (non-PQC) website with sample travel agency content
- Port 444: PQC site with simple cipher info output

#### Usage
```bash
docker run -d --name pqc-nginx --rm -p 9443:443 -p 9444:444 kevingstewart/udf-nginx-pqc:1.1
```

#### Build
```bash
git clone https://github.com/kevingstewart/udf-lab-images.git
cd udf-lab-images/pqc-nginx
docker build --no-cache -t udf-nginx-pqc:1.1 .
docker run -d --name pqc-nginx --rm -p 9443:443 -p 9444:444 udf-nginx-pqc:1.1
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
