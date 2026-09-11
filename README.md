# OSIRIS_Secure_architecture
Architecture réseau sécurisée avec pfSense et supervision Wazuh - Projet académique EFREI 2026
# OSIRIS — Plateforme SIEM/XDR open source

Projet académique — Mastercamp FG601, EFREI Paris Panthéon-Assas (Cycle Ingénieur, filière Réseaux & Cybersécurité), 2025-2026.

## Contexte

Les infrastructures d'entreprise reposent sur des systèmes de plus en plus hétérogènes (serveurs Linux, postes Windows, équipements réseau, services web), ce qui complique le maintien d'une visibilité globale sur leur sécurité. L'objectif du projet était de concevoir et déployer, en réponse à un cahier des charges, une plateforme SIEM/XDR capable d'assurer cette supervision en utilisant exclusivement des outils open source, à coût quasi nul.

## Architecture déployée

Infrastructure entièrement virtualisée sous VMware Workstation, avec 5 machines virtuelles réparties sur deux zones réseau (WAN/NAT et LAN/Host-Only), reliées par un pare-feu pfSense :

| Machine | Rôle | OS |
|---|---|---|
| Ubuntu Server | Serveur SIEM central (Wazuh + Suricata + Apache) | Ubuntu 24.04 LTS |
| pfSense | Pare-feu réseau (WAN/LAN), filtrage et export Syslog | pfSense CE 2.7.2 |
| Kali Linux | Machine attaquante (simulation) | Kali GNU/Linux 2026.1 |
| Windows 10 | Poste de travail supervisé | Windows 10 Pro |
| Metasploitable 2 | Serveur cible vulnérable | Ubuntu 8.04 (legacy) |

La collecte des journaux s'effectue en mode agent (Kali, Windows, Metasploitable → Wazuh via TCP/1514) et en mode agentless/Syslog pour pfSense (UDP/514). Suricata analyse le trafic réseau en temps réel et transmet ses alertes à Wazuh.

## Stack technique

- **Wazuh** (manager, indexer OpenSearch, dashboard) — collecte, corrélation et visualisation des événements de sécurité
- **pfSense** — pare-feu, segmentation réseau WAN/LAN, export Syslog
- **Suricata** — détection d'intrusion réseau (IDS)
- **Apache** — service web supervisé, cible des tests d'attaques applicatives
- **UFW** — pare-feu local complémentaire
- Cartographie des règles de détection sur le référentiel **MITRE ATT&CK** (T1110, T1046, T1078, T1595)

## Scénarios de test réalisés

5 types d'attaque simulés depuis Kali Linux et détectés avec succès par la plateforme :

1. **Reconnaissance réseau** — scan Nmap → 23 ports identifiés, alertes Suricata
2. **Accès FTP anonyme** — connexion journalisée dans Wazuh
3. **Brute force FTP** — attaque Hydra (1080 tentatives) → alerte déclenchée
4. **Scan de vulnérabilités web** — Nikto sur la cible et sur le serveur Ubuntu → alertes remontées
5. **Exploitation d'une backdoor** — shell root obtenu, connexion agent confirmée active

## Ma contribution

Dans le cadre de ce projet mené en équipe de 3 (sous la direction d'une cheffe de projet ayant piloté la conception de l'architecture), j'ai contribué à :
- la mise en œuvre technique des composants (déploiement et configuration Wazuh, Suricata, agents)
- la réalisation des tests de sécurité (scénarios d'attaque et validation de la détection)
- la rédaction et la structuration de la documentation du projet (ce rapport)

## Résultats et limites

Les Phases 1 (déploiement minimum) et 2 (règles de détection personnalisées) du cahier des charges sont validées. La Phase 3 (réponse automatique aux incidents, conteneurisation Docker, cartographie MITRE ATT&CK complète) est en cours de finalisation.

L'architecture actuelle est en mode *all-in-one*, ce qui limite sa scalabilité dans un contexte de forte volumétrie — un axe d'amélioration identifié pour la suite.

## Documentation complète

Le rapport final détaillé est disponible dans ce dépôt : [`Rapport_Final_OSIRIS.pdf`](./Rapport_Final_OSIRIS.pdf).
