# Aufgaben

Übersicht aller 29 Aufgaben des Projekts. Jede Aufgabe hat ein Ziel, einen Praxisbezug und die wichtigsten Schritte. Die Umgebung ist in [NETZWERK-TOPOLOGIE.md](NETZWERK-TOPOLOGIE.md) beschrieben.

---

## 1. VMware Virtual Network Topology

**Ziel:** Zwei virtuelle Netzwerke für pfSense einrichten: WAN (VMnet8, NAT) und LAN (VMnet2, Host-only).

**Praxisbezug:** Eine Firewall sitzt zwischen einem externen und einem internen Netzwerk. Im Labor bilden VMnet8 und VMnet2 diese beiden Seiten nach.

**Schritte:**
- VMnet2 im **Virtual Network Editor** als Host-only anlegen (Subnetz 10.10.10.0/24, kein DHCP)
- pfSense-VM mit zwei Network Adapters ausstatten (VMnet8 und VMnet2)
- Ubuntu Server an VMnet2 anschließen

---

## 2. Initial Setup Wizard

**Ziel:** Die Grundkonfiguration von pfSense mit dem **Setup Wizard** abschließen.

**Praxisbezug:** Nach der Installation müssen Hostname, Domain, DNS, Zeitzone, WAN, LAN und das Admin-Passwort gesetzt werden.

**Schritte:**
- Hostname `pfsense`, Domain `lab.local`, DNS-Server und Zeitzone `Europe/Berlin` eintragen
- WAN auf DHCP setzen, **Block bogon networks** aktivieren
- LAN-IP `10.10.10.2/24` prüfen und neues Admin-Passwort setzen

---

## 3. Interface Assignment und IP Configuration

**Ziel:** Die Zuordnung der Interfaces und ihre IP-Konfiguration prüfen und festlegen.

**Praxisbezug:** Alle Rules und Dienste hängen an den Interfaces. Deshalb müssen WAN und LAN richtig zugeordnet und konfiguriert sein.

**Schritte:**
- In **Interfaces > Assignments** die Ports von WAN und LAN kontrollieren
- WAN per DHCP, LAN mit statischer Adresse `10.10.10.2/24` konfigurieren
- Mit **Diagnostics > Ping** die Verbindung zu LAN und WAN testen

---

## 4. Web GUI-Zugriff und Admin-Sicherheit

**Ziel:** Den Zugriff auf die Verwaltung absichern.

**Praxisbezug:** Die Firewall selbst ist ein Angriffsziel. Deshalb nutze ich HTTPS, SSH mit Public Key, **Login Protection** und ein Session Timeout.

**Schritte:**
- Protokoll HTTPS (Port 443) einstellen
- SSH aktivieren und den öffentlichen Schlüssel beim Benutzer `admin` hinterlegen
- **Session Timeout** auf 15 Minuten setzen

---

## 5. DHCP Server

**Ziel:** Einen DHCP Server für das LAN einrichten.

**Praxisbezug:** Clients bekommen Adresse, Gateway und DNS automatisch. Der Bereich liegt getrennt von den festen Adressen.

**Schritte:**
- **Services > DHCP Server > LAN** aktivieren
- Bereich `10.10.10.100` bis `10.10.10.199`, DNS `10.10.10.2`
- Status des Dienstes `dhcpd` prüfen

---

## 6. Static DHCP Mappings

**Ziel:** Dem Ubuntu Server per DHCP immer dieselbe Adresse geben.

**Praxisbezug:** Server brauchen feste Adressen. Mit einem **Static Mapping** wird die Adresse zentral in pfSense verwaltet.

**Schritte:**
- MAC-Adresse des Servers ermitteln
- Mapping `10.10.10.10` mit Hostname `ubuntu-server` anlegen
- Netplan auf DHCP umstellen und in **Status > DHCP Leases** prüfen

---

## 7. DNS Resolver

**Ziel:** Den **DNS Resolver** (Unbound) für das LAN konfigurieren.

**Praxisbezug:** Clients lösen interne und externe Namen über pfSense auf. Mit einem **Host Override** bekommen interne Server einen Namen.

**Schritte:**
- Resolver für LAN aktivieren, **DNSSEC** einschalten
- **Host Override** `ubuntu-server.lab.local` anlegen
- Auflösung mit `dig` testen

---

## 8. Default Firewall Rules

**Ziel:** Das Standardverhalten der Firewall Rules untersuchen.

**Praxisbezug:** Ich muss wissen, was ohne eigene Rules passiert: LAN darf nach außen, eingehender WAN-Verkehr wird blockiert. Rules werden von oben nach unten geprüft, die erste passende gilt.

**Schritte:**
- Rules auf LAN und WAN ansehen
- Logging für die Default Rule aktivieren
- Verkehr erzeugen und in **Status > System Logs > Firewall** prüfen

---

## 9. Aliases

**Ziel:** **Aliases** für Hosts, Netze und Ports anlegen und in einer Rule nutzen.

**Praxisbezug:** Aliases machen Rules lesbar und leichter änderbar, weil Werte nur an einer Stelle stehen.

**Schritte:**
- Aliases `UBUNTU_SERVER`, `LAN_NET`, `WEB_PORTS` erstellen
- Eine Test-Rule mit den Aliases anlegen und die Sperre mit `curl` prüfen
- Test-Rule wieder löschen

---

## 10. Outbound NAT

**Ziel:** Die Modi von **Outbound NAT** untersuchen und eine manuelle Rule anlegen.

**Praxisbezug:** Interne Adressen sind privat. Beim Zugriff ins Internet übersetzt pfSense sie in die WAN-Adresse.

**Schritte:**
- Modus **Automatic** prüfen
- Auf **Hybrid** umstellen und eine Rule für `10.10.10.0/24` anlegen
- Übersetzung in **Diagnostics > States** prüfen, danach zurück auf **Automatic**

---

## 11. Port Forward

**Ziel:** SSH (Port 2222) und HTTP (Port 8080) vom WAN zum Ubuntu Server weiterleiten.

**Praxisbezug:** Ein interner Dienst wird von außen erreichbar. Die Option **Filter rule association** erzeugt die passende WAN-Rule automatisch.

**Schritte:**
- Auf dem Server `openssh-server` und `nginx` installieren
- Zwei **Port Forward**-Einträge mit Filter-Rule anlegen
- Zugriff vom Host mit `ssh -p 2222` und `curl` testen

---

## 12. Firewall Logs

**Ziel:** Die **Firewall Logs** lesen, filtern und die Entscheidungen nachvollziehen.

**Praxisbezug:** Mit den Logs sehe ich, welche Rule einen Verkehr erlaubt oder blockiert hat. Das ist die Grundlage für die Fehlersuche.

**Schritte:**
- Logging für die Port-Forward-Rule aktivieren
- Erlaubten und blockierten Verkehr erzeugen
- **Normal View**, **Dynamic View** und **Advanced Log Filter** nutzen

---

## 13. Diagnostics

**Ziel:** Die Diagnose-Werkzeuge von pfSense einsetzen.

**Praxisbezug:** Verbindungsprobleme lassen sich mit Ping, Traceroute, DNS Lookup und der State-Tabelle eingrenzen.

**Schritte:**
- **Diagnostics > Ping** und **Traceroute** ausführen
- **DNS Lookup** für `ubuntu-server.lab.local` testen
- **Diagnostics > States** filtern

---

## 14. DMZ Interface

**Ziel:** Ein drittes Netz (DMZ) mit eigenem Interface und eigenem Server einrichten.

**Praxisbezug:** Server, die von außen erreichbar sind, stehen in einem eigenen Netz und nicht im internen LAN.

**Schritte:**
- VMnet3 (`10.10.20.0/24`) anlegen und als dritten Adapter an pfSense hängen
- Interface `OPT1` als `DMZ` mit `10.10.20.2/24` konfigurieren
- DHCP Server mit Static Mapping für `dmz-server` (`10.10.20.10`) einrichten

---

## 15. DMZ Firewall Rules

**Ziel:** Die DMZ isolieren.

**Praxisbezug:** Die DMZ darf DNS nutzen und ins Internet, aber keine privaten Netze erreichen. Das LAN darf auf den Webserver der DMZ zugreifen.

**Schritte:**
- Aliases `RFC1918_NETWORKS` und `DMZ_SERVER` anlegen
- DMZ-Rules: DNS erlauben, private Netze blockieren, Internet erlauben (in dieser Reihenfolge)
- Zugriff vom LAN auf den Webserver und das Blockieren der DMZ testen

---

## 16. Schedules

**Ziel:** Eine **Schedule** erstellen und mit einer Rule verbinden.

**Praxisbezug:** Rules können nur zu bestimmten Zeiten gelten, zum Beispiel an Arbeitstagen von 08:00 bis 18:00 Uhr.

**Schritte:**
- Schedule `WORKDAYS_BUSINESS_HOURS` anlegen
- Block-Rule mit der Schedule verknüpfen
- Verhalten mit aktiver und inaktiver Schedule testen, danach Rule löschen

---

## 17. User Management und Privileges

**Ziel:** Benutzer und eine Gruppe mit eingeschränkten Rechten anlegen.

**Praxisbezug:** Nicht jeder Benutzer braucht alle Rechte. Eine Gruppe mit wenigen **Privileges** begrenzt den Zugriff.

**Schritte:**
- Gruppe `readonly-admins` mit Privileges für Dashboard, Firewall Logs, Interfaces und Ping
- Benutzer `auditor` (Gruppe) und `netadmin` (Gruppe `admins`) anlegen
- Zugriff im privaten Browserfenster testen

---

## 18. Backup/Restore und System Update

**Ziel:** Die Konfiguration sichern, wiederherstellen und den Update-Status prüfen.

**Praxisbezug:** Vor Änderungen und Updates brauche ich ein Backup. Ein Restore-Test zeigt, dass das Backup funktioniert.

**Schritte:**
- Verschlüsseltes Backup als XML herunterladen
- Alias `LAN_NET` löschen und die Konfiguration wiederherstellen
- **System > Update** prüfen und installierte Pakete notieren

---

## 19. VLANs (802.1Q)

**Ziel:** Zwei VLANs (10 und 20) auf dem LAN-Port einrichten.

**Praxisbezug:** VLANs trennen Netze logisch auf demselben Port, zum Beispiel ein Server-Netz und ein Client-Netz.

**Schritte:**
- VLAN 10 (`10.10.110.0/24`) und VLAN 20 (`10.10.120.0/24`) anlegen und als Interfaces zuweisen
- DHCP Server mit Static Mapping pro VLAN einrichten
- VLAN-Interfaces im Ubuntu Server per Netplan konfigurieren

---

## 20. Inter-VLAN Routing und Segmentation Policy

**Ziel:** Den Verkehr zwischen den VLANs steuern.

**Praxisbezug:** VLAN 20 (Clients) darf nur Web auf VLAN 10 (Server). VLAN 10 darf VLAN 20 nicht erreichen. Beide VLANs nutzen DNS und Internet, aber keine anderen privaten Netze.

**Schritte:**
- Zwei Test-VMs `server-vlan10` und `client-vlan20` einrichten
- Rules pro VLAN anlegen (DNS, Web bzw. Block, private Netze blockieren, Internet erlauben)
- Erlaubte und blockierte Verbindungen mit `curl` und `ping` testen

---

## 21. Multi-WAN (Gateway Groups und Failover)

**Ziel:** Eine zweite WAN-Leitung einrichten und einen Failover konfigurieren.

**Praxisbezug:** Bei Ausfall der ersten Leitung soll der Verkehr automatisch über die zweite laufen. **Gateway Monitoring** erkennt den Ausfall.

**Schritte:**
- WAN2 über VMnet0 (Bridged) einrichten
- **Gateway Group** `WAN_FAILOVER` mit Tier 1 und Tier 2 anlegen
- LAN-Rule auf die Gateway Group setzen, **State Killing on Gateway Failure** aktivieren
- Ausfall und Rückkehr der Leitung in **Diagnostics > States** prüfen

---

## 22. Virtual IPs und 1:1 NAT

**Ziel:** Eine zusätzliche WAN-Adresse (**Virtual IP**) per 1:1 NAT auf den `dmz-server` abbilden.

**Praxisbezug:** Ein Server bekommt eine eigene Adresse nach außen. Anders als **Port Forward** gilt 1:1 NAT für die ganze Adresse. Mit **Split DNS** lösen interne Clients den Namen direkt auf die interne Adresse auf.

**Schritte:**
- **Virtual IP** `192.168.197.50` (IP Alias) auf dem WAN anlegen
- 1:1 NAT auf `10.10.20.10` und eine WAN-Rule nur für Web erstellen
- Zugriff vom Host testen und die ausgehende Adresse in **Diagnostics > States** prüfen
- **Host Override** `www.lab.local` auf `10.10.20.10` für Split DNS anlegen

---

## 23. OpenVPN Remote Access

**Ziel:** Einen **OpenVPN**-Server für den Fernzugriff einrichten.

**Praxisbezug:** Mitarbeiter greifen von außen über einen verschlüsselten Tunnel auf interne Ressourcen zu. Die Anmeldung erfolgt mit Zertifikat und Passwort, der Zugriff ist per Rule begrenzt.

**Schritte:**
- Certificate Authority, Server-Zertifikat und Benutzer `vpnuser1` mit Zertifikat anlegen
- OpenVPN-Server konfigurieren (UDP 1194, Tunnel `10.10.100.0/24`)
- Rules für WAN und OpenVPN erstellen (nur SSH zum Ubuntu Server und Web zur DMZ)
- Client-Konfiguration exportieren und Tunnel vom Host testen

---

## 24. WireGuard

**Ziel:** Einen **WireGuard**-Tunnel für den Fernzugriff einrichten.

**Praxisbezug:** WireGuard authentifiziert mit Schlüsselpaaren. Jeder Peer hat feste **Allowed IPs**. Den Zugriff steuern Firewall Rules.

**Schritte:**
- Paket installieren, Tunnel (UDP 51820, Netz `10.10.101.0/24`) und Interface `WG_VPN` anlegen
- Peer für den Host mit Public Key und `Allowed IPs` erstellen
- Rules für WAN und `WG_VPN` erstellen
- Verbindung mit `wg-quick` testen und `latest handshake` prüfen

---

## 25. Traffic Shaper (Limiters)

**Ziel:** Die Bandbreite eines Clients mit **Limiters** begrenzen.

**Praxisbezug:** Ein einzelner Client soll die Internetleitung nicht auslasten. Upload und Download werden getrennt begrenzt.

**Schritte:**
- Messung ohne Limit mit `speedtest-cli`
- Limiter `UBUNTU_UP` (10 Mbit/s) und `UBUNTU_DOWN` (20 Mbit/s) anlegen
- Rule mit **In/Out pipe** erstellen und Messung wiederholen
- Test-Rule wieder löschen

---

## 26. Suricata (IDS/IPS)

**Ziel:** **Suricata** auf dem LAN einsetzen, Alarme erzeugen und den Absender blockieren.

**Praxisbezug:** Firewall Rules prüfen Adressen und Ports. Suricata prüft zusätzlich den Inhalt des Verkehrs. Im IDS-Modus gibt es Alarme, im IPS-Modus (Legacy Mode) wird der Absender blockiert.

**Schritte:**
- Hardware-Offload (Checksum, TSO, LRO) deaktivieren
- Paket installieren, **Emerging Threats Open**-Regeln laden, LAN-Interface hinzufügen
- Eigene Regel `LAB Suspicious User-Agent` in `custom.rules` anlegen und Alarm erzeugen
- **Block Offenders** aktivieren, Blockierung testen und danach zurücksetzen

---

## 27. pfBlockerNG

**Ziel:** Bekannte schädliche IP-Adressen und Domains mit **pfBlockerNG** blockieren.

**Praxisbezug:** Listen mit schädlichen Adressen werden automatisch geladen. **DNSBL** blockiert Domains über den DNS Resolver. Bei einem False Positive hilft eine Whitelist.

**Schritte:**
- Paket `pfBlockerNG-devel` installieren, **DNSBL Virtual IP** auf `10.10.250.1` setzen
- IPv4-Liste (Emerging Threats) und DNSBL-Gruppe anlegen
- Listen aktualisieren und Blockierung mit `dig` und `ping` testen
- Test-Domain in die **DNSBL Whitelist** eintragen

---

## 28. Hardening und Security Audit

**Ziel:** pfSense absichern und die Konfiguration prüfen.

**Praxisbezug:** Nach dem Aufbau räume ich Test-Rules auf, schränke die Verwaltung ein und prüfe von außen, welche Ports offen sind.

**Schritte:**
- Test-Rules und ungenutzte Aliases löschen, Rules prüfen
- Verwaltung nur für `ADMIN_HOST` erlauben, LAN-Zugriff blockieren
- SSH nur mit Public Key, Konsolenmenü mit Passwort, IPv6 deaktivieren
- Offene Ports mit `sockstat` und `nmap` prüfen, Backup erstellen

---

## 29. Management Subnet und Jump Server

**Ziel:** Ein eigenes Management-Netz (MGMT) mit **Jump Server** einrichten.

**Praxisbezug:** Die Verwaltung läuft über ein getrenntes Netz. Server werden nur über den Jump Server per SSH erreicht. LAN und DMZ können das Management-Netz nicht erreichen.

**Schritte:**
- VMnet4 (`10.10.30.0/24`), Interface `MGMT` und `jump-server` (`10.10.30.10`) einrichten
- Aliases `JUMP_SERVER` und `MGMT_TARGETS` sowie fünf MGMT-Rules erstellen
- Rule `Block LAN to MGMT` auf dem LAN anlegen
- `ProxyJump` in `~/.ssh/config` einrichten und Zugriff sowie Isolation testen