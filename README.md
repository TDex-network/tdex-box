# tdex-box
Docker Compose for running TDex Daemon in production environments

 
## Configure

1. Clone this repository and enter in the folder

```sh
$ git clone https://github.com/TDex-network/tdex-box
$ cd tdex-box
```

2. Edit [feederd/config.json](https://github.com/TDex-network/tdex-feeder#config-file) file if you wish. By default it defines a market with LBTC-USDt and uses Kraken as price feed.


3. Export ENV variable for Esplora REST endpoint

```sh
# if left blank will default https://blockstream.info/liquid/api 
$ export EXPLORER=zzz
```

4. **OPTIONAL** TLS or Onion

#### TLS

Uncomment the in the compose file (`docker-compose.yml`) the TLS related stuff and export ENV with the asbolute path to the SSL Certificate and Key to be used.

```sh
$ export SSL_CERT_PATH=/path/to/fullchain.pem
$ export SSL_KEY_PATH=/path/to/privatekey.pem
```

#### Onion

Add this compose service at the bottom of the compose file (either `docker-compose.yml`)

```yml
  # Tor Onion Hidden service
  tor:
    container_name: "tor"
    image: goldy/tor-hidden-service:latest
    restart: unless-stopped
    depends_on:
      - tdexd
    environment:
      # Set version 3 on TDEX
      TDEX_TOR_SERVICE_HOSTS: "80:tdexd:9945"
      TDEX_TOR_SERVICE_VERSION: "3"
      TDEX_TOR_SERVICE_KEY: ${ONION_KEY}
    # Keep keys in volumes
    volumes:
      - ./tor:/var/lib/tor/hidden_service/
```

Export you Onion service V3 private key or leave it blank to create a new one

```sh
$ export ONION_KEY=base64_Onion_V3_Private_Key
```

## Run 

```sh
$ docker-compose -f docker-compose.yml up -d
```

Check the Logs

```
$ docker logs tdexd --tail 20
$ docker logs feederd --tail 20
```


**Onion-only** Check the onion endpoint

```sh
$ docker exec tor onions
```



