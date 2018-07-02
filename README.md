# docker-digi
Packet Radio Digi based on Docker Images

# description
Mithilfe dieser Docker-Beschreibungen soll es einfach und unkompliziert sein, einen kompletten Packet Radio Digi auf aktueller Hardware aufzusetzen.
Das gesamte Setup ist über das docker-compose.yml gesteuert. Weiterhin sollen Wizards fuer die einzelnen Images/Container dem Benutzer die Konfiguration erleichtern.

# contents
Es werden folgende Programme ausgerollt:
```
Diginode: TheNetNode, TNN, Version 1.79 mh06
Mailbox:  OpenBCM, Version 1.07 b12
```

# usage
Um ein Digi Setup zu erstellen, bitte die docker-compose.yml anpassen und anschliessend deployen:
```
docker-compose up -d
```
Fuer einen Neustart der Container:
```
docker-compose restart
```
Um sich auf die Konsole vom TNN zu schalten, ist das Tool Screen installiert. Dieses kann virtuelle Konsolen erstellen und laesst diese auch nach einem Disconnect weiter offen:
```
docker exec -i -t digi_tnn_1 /bin/bash
screen -r tnn
```
Logs von der Mailbox/OpenBCM lassen sich abrufen mit:
```
docker logs --tail 100 digi_openbcm_1
```


# topology
Folgende Topologie wird erzeugt:
```
                            Docker Host
                      |---------------------|
                      |                     |
                      |    |----------|     |
 /dev/ttyS0 ----------|----|    TNN   |-----|--- 10093/udp fuer AX25IP Verbindungen
 TNC im Smack-Modus   |    |  CALL-0  |     |              direkt auf der Docker Host IP
 als Benutzereinstieg |    |----------|     |              erreichbar
 via HF               |          |          |
                      |          |  <-------|------------- AX25IP Verbindung zwischen TNN und OpenBCM
                      |          |          |
                      |    |----------|     |
                      |    |  OpenBCM |-----|--- 8081/tcp  Webinterface von der Mailbox
                      |    |  CALL-4  |     |    8025/tcp  SMTP Port
                      |    |----------|     |    8110/tcp  POP3 Port
                      |                     |    8119/tcp  NNTP Port
                      |---------------------|              alle direkt auf der Docker Host IP
                                                           erreichbar
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
      location: <location>        Standort vom Digi
      locator: <locator>          Locator vom Standort
      port1: <options>            \
      [...]                        |- port1 bis port15 sind moegliche Ports (Beschreibung unten)
      port15: <options>           /
    ports:
     - 10093:10093/udp            Veroeffentlichte Ports (hier vom AX25IP Interface)
    volumes:
     - ./conf/tnn/:/cfg         TNN Konfigurationen werden ausserhalb vom Docker abgelegt
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

# openbcm: configuration
Nachfolgend eine Uebersicht ueber die Konfiguration der OpenBCM:
```
  openbcm:
    build:
     context: ./openbcm1.07b12/      Verzeichnis vom Dockerfile
     args:
      boxaddr: DL1NE.#NDS.DEU.EU     Mailbox Adresse
      mycall: DL1NE                  Rufzeichen der Mailbox (ohne -SSID!)
      sysop: DL1NE                   Rufzeichen vom Sysop
    ports:
     - 8081:8080                     Veroeffentlichte Ports (hier vom Webinterface)
     - 8025:8025                                                  ... SMTP
     - 8110:8110                                                  ... POP3
     - 8119:8119                                                  ... NNTP
    volumes:
     - ./conf/openbcm/:/cfg          Konfigurationen werden ausserhalb vom Docker abgelegt
```

# todo
```
- Docker Setup auf Raspberry testen (kein AMD64!)

- IP Routing fuer TNN hinzufuegen
--> Kernel Konfiguration
--> iptables Regeln vom Docker-Host
--> Routing

- Wizard fuer TNN Konfiguration von erweiterten Parametern bauen
--> Links
--> AX25IP Routing
--> Meldungen (Connect, Aktuell, etc)

- Wizard fuer OpenBCM Konfiguration von erweiterten Parametern bauen
--> Forwarding
--> etc.

- Hilfe Dateien fuer OpenBCM finden
```
