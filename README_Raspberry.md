# installation 1: raspbian
Fuer den Start wird das Raspbian via SD-Karte installiert. Anleitungen finden sich dazu im Internet genug...

# installation 2: docker
Wir brauchen Docker um spaeter die Container sauber ausfuehren zu koennen. Dazu wird Docker mit folgendem Befehl installiert:
```
curl -sSL https://get.docker.com | sh
```

# installation 3: docker-compose
Nun wird es etwas schwieriger; ich konnte kein docker-compose fertig für den RPi in Version 3 finden. Somit habe ich auf die Hilfe im Netz zurückgegriffen:
```
cd /opt
git clone https://github.com/docker/compose.git
cd compose
docker build -t docker-compose:armhf -f Dockerfile.armhf .
docker run --rm --entrypoint="script/build/linux-entrypoint" -v $(pwd)/dist:/code/dist -v $(pwd)/.git:/code/.git "docker-compose:armhf"
cp dist/docker-compose-Linux-armv7l /usr/local/bin/docker-compose
chown root:root /usr/local/bin/docker-compose
chmod 0755 /usr/local/bin/docker-compose
```
Quelle: https://www.berthon.eu/2017/getting-docker-compose-on-raspberry-pi-arm-the-easy-way/

# installation 4: docker-digi
```
cd /opt
git clone https://github.com/dl1ne/docker-digi.git
cd docker-digi
docker-compose up -d
```

