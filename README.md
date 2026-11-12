# pfSense Firewall Lab

Proof of Concept: Firewall-Umgebung mit pfSense in einer virtuellen Laborumgebung.

## Projektziel

In diesem Projekt habe ich eine komplette Firewall-Umgebung mit pfSense aufgebaut. Das Projekt zeigt typische Aufgaben eines Firewall-Administrators, von der Grundkonfiguration bis zu Segmentierung, VPN, IDS/IPS und Hardening. Jede Aufgabe habe ich praktisch umgesetzt und mit Tests geprüft.

Die Umgebung und die Netzwerk-Topologie sind in [NETZWERK-TOPOLOGIE.md](NETZWERK-TOPOLOGIE.md) beschrieben.

## Umgesetzte Themen

Das Projekt hat 29 Aufgaben. Die Nummern beziehen sich auf [AUFGABEN.md](AUFGABEN.md). Dort stehen zu jeder Aufgabe Ziel, Praxisbezug und die wichtigsten Schritte.

| Bereich | Umgesetzt | Aufgaben |
|---|---|---|
| Grundkonfiguration | VMware-Netzwerke, Setup Wizard, Interfaces, Web GUI und Admin-Sicherheit, Backup und Update | 1–4, 18 |
| Netzwerkdienste | DHCP Server, Static DHCP Mappings, DNS Resolver | 5–7 |
| Firewall Rules und NAT | Default Rules, Aliases, Outbound NAT, Port Forward, Schedules, Virtual IPs und 1:1 NAT | 8–11, 16, 22 |
| Segmentierung | DMZ mit eigenen Rules, VLANs, Inter-VLAN Policy, Management-Netz mit Jump Server | 14, 15, 19, 20, 29 |
| Ausfallsicherheit | Multi-WAN mit Gateway Groups und Failover | 21 |
| VPN | OpenVPN Remote Access, WireGuard | 23, 24 |
| Traffic und Schutz | Limiters, Suricata (IDS/IPS), pfBlockerNG | 25–27 |
| Betrieb und Sicherheit | Firewall Logs, Diagnostics, User Management, Hardening und Security Audit | 12, 13, 17, 28 |

## Dateien

| Datei | Inhalt |
|---|---|
| [README.md](README.md) | Überblick über das Projekt |
| [NETZWERK-TOPOLOGIE.md](NETZWERK-TOPOLOGIE.md) | Umgebung, Netzwerk-Topologie und virtuelle Maschinen |
| [AUFGABEN.md](AUFGABEN.md) | Alle Aufgaben mit Ziel, Praxisbezug und Schritten |

## Hinweise

- Das Projekt ist eine **Laborumgebung**. Einige Einstellungen sind nur für das Labor gewählt, zum Beispiel ist **Block private networks** am WAN ausgeschaltet, weil VMnet8 private IP-Adressen verwendet.
- Menüs, Buttons und Namen der Oberfläche sind auf Englisch. Der erklärende Text ist auf Deutsch.
- Im Repository liegen keine Passwörter, privaten Schlüssel (`*.key`, `*.pem`), VPN-Konfigurationen (`*.ovpn`) oder pfSense-Backups (`*.xml`).