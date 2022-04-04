# Run nginx with https over tls_v1.3 only

## Create directory for tls certificates
```shell
mkdir -p certificates/ca
mkdir -p certificates/server
```

## Create self-signed root CA certificate
```shell
openssl req -x509 -newkey rsa:4096 -keyout certificates/ca/rootCA.key -out certificates/ca/rootCA.pem -sha256 -days 365
```
Then follow the command prompt and fill data of your Demo Organization

## Create certificate request (csr)
```shell
openssl req -new -newkey rsa:2048 -nodes -keyout certificates/server/nginx-server.key -days 365 -out certificates/server/nginx-server.csr -config openssl_conf/csr.cnf
```

## Sign the certificate
```shell
openssl x509 -req -in certificates/server/nginx-server.csr -CA certificates/ca/rootCA.pem -CAkey certificates/ca/rootCA.key -CAcreateserial -out certificates/server/nginx-server.pem -days 500 -sha256
```

## Before you run
need to add nginx-server.csr to your system trust store 

## Run nginx
```shell
docker run -p 443:443 -v ${PWD}/nginx_conf/nginx.conf:/etc/nginx/nginx.conf -v ${PWD}/certificates/ca/rootCA.pem:/etc/nginx/ssl/rootCA.pem -v ${PWD}/certificates/server:/etc/nginx/ssl --name webserver nginx
```

