# docker-digi
Packet Radio Digi based on Docker Images

# description
Mithilfe dieser Docker-Beschreibungen soll es einfach und unkompliziert sein, einen kompletten Packet Radio Digi auf aktueller Hardware aufzusetzen.
Das gesamte Setup ist über das docker-compose.yml gesteuert. Weiterhin sollen Wizards fuer die einzelnen Images/Container dem Benutzer die Konfiguration erleichtern.

# usage
Um ein Digi Setup zu erstellen, bitte die docker-compose.yml anpassen und anschliessend deployen:
```
docker-compose up -d
```

# tnn: configuration
Nachfolgend werden die Parameter fuer TNN (TheNetNode) genauer beschrieben.
```
  tnn:
    build:
     context: <dir>               Verzeichnis vom Dockerfile
     args:
      pwsysop: <pw1>              Sysop-Passwort vom TNN bei AX-Verbindungen
      pwconsole: <pw2>            Sysop-Passwort fuer den Consolen Zugriff
      nodeident: <ident>          Ident des Digis
      nodecall: <callsign>        Rufzeichen vom Digi
      port1: <options>            \
      [...]                        |- port1 bis port15 sind moegliche Ports (Beschreibung unten)
      port15: <options>           /
    ports:
     - 10093:10093/udp            Veroeffentlichte Ports (hier vom AX25IP Interface)
    volumes:
     - ./tnn179mh06/:/cfg         TNN Konfigurationen werden ausserhalb vom Docker abgelegt
    devices:
     - /dev/ttyS0:/dev/ttyS0      Beispiel-Mount von seriellen Ports
    restart: unless-stopped
```

# tnn: ports
Folgende Parameter/Optionen sind fuer die Ports in der docker-compose.yml vorhanden:
```
Aufbau:
port<nr>=<name>|<device>|<type>|[<speed>]|[<lockfile>]

Beispiele:
port1=9k6_User|/dev/ttyS0|1|9600|/var/lock/LCK..ttyS0
port2=AX25IP|AX25IP|8

# type of devices/ports:
# 0 = KISS, 1 = SMACK, 2 = RMNC-KISS,
# 4 = Vanessa, 5 = SCC, 6 = TF (experimental!),
# 7 = IPX (1 IPX #port only),
# 8 = AX25IP (1 AX25IP #port only)
# 10 = Kernel-AX.25, 11 = DG1KJD Kernel-AX.25
# 12 = 6PACK
```
