# SAÉ 5.02 - Déploiement automatisé et supervision multi-environnements avec Zabbix

## Description du Projet
Ce projet s'inscrit dans le cadre de la **SAÉ 5.02**. Il consiste à automatiser le déploiement, la configuration et la gestion d'une plateforme de supervision **Zabbix** conteneurisée via **Docker Compose**, et pilotée par des playbooks **Ansible**, sur une maquette de démonstration isolée sous **Oracle VirtualBox** (réseau interne `intnet` : `192.168.56.0/24`).

## Architecture du Socle (Maquette à 3 composants)
L'infrastructure de test repose sur trois éléments clés interconnectés sur le réseau interne `192.168.56.0/24` :

1. **Le Serveur Zabbix & Nœud Ansible (`marsdb1-l1` — `192.168.56.10`)** :
   - Machine Virtuelle **Debian** avec adresse IP statique persistante sur `enp0s8`.
   - Orchestration via **Docker & Docker Compose** sur un réseau bridge dédié (`zabbix_net` : `172.28.0.0/16`).
   - Conteneurs : `zabbix-db` (PostgreSQL 15-alpine), `zabbix-server` (v6.4-alpine, port `10051`) et `zabbix-web` (Nginx/PHP v6.4-alpine, port `8080`).

2. **Le Poste Cible Windows (`win-target-01` — `192.168.56.102`)** :
   - Machine Virtuelle **Windows Server** configurée avec l'IP statique `192.168.56.102/24`, **WinRM** (HTTP port `5985`) et ouverture du port TCP `10050`.
   - Déploiement automatisé de l'agent Zabbix via Ansible et supervision active de la santé du système (CPU, RAM, stockage).

3. **La Cible SNMP Linux (`sw-snmp-01` — `192.168.56.254`)** :
   - Machine Virtuelle **Debian** configurée avec l'IP statique `192.168.56.254/24` (en substitution d'un switch virtuel GNS3 dans cet environnement VirtualBox).
   - Supervision des métriques et de la connectivité via le daemon `snmpd` (`udp:161`, communauté `public`).

## Organisation du Dépôt et Playbooks Ansible
L'automatisation est structurée autour des fichiers et playbooks suivants :
- `hosts.example` : Modèle du fichier d'inventaire des cibles (à copier vers `hosts`, exclu de Git pour la sécurité).
- `.env.example` : Modèle des variables d'environnement Docker (à copier vers `.env`, exclu de Git).
- `docker-compose.yml` : Déploiement de la stack Zabbix (base PostgreSQL, serveur et interface web).
- `playbook_init.yml` : Préparation de l'hôte Debian et installation des prérequis (Docker & Docker Compose).
- `deploy_windows_agent.yml` : Copie de l'installateur MSI, installation silencieuse et démarrage automatique du service Zabbix Agent sur `win-target-01`.

## Lancement du Déploiement
Pour préparer l'environnement, exécuter les playbooks d'automatisation et démarrer Zabbix :
```bash
cp .env.example .env
cp hosts.example hosts
ansible-playbook -i hosts playbook_init.yml
ansible-playbook -i hosts deploy_windows_agent.yml
docker compose up -d
