# SAÉ 5.02 - Déploiement automatisé d'une plateforme de supervision Zabbix

## Description du Projet
Ce projet consiste à automatiser le déploiement et la configuration d'une plateforme de supervision Zabbix conteneurisée via Docker Compose et administrée par Ansible, dans le cadre d'une maquette de démonstration isolée.

## Architecture du Socle (Maquette à 3 composants)
- **1. Serveur Zabbix (Le Cerveau)** : VM Ubuntu 22.04 LTS sous VirtualBox avec Docker et Docker Compose (Réseau bridge `zabbix_net` : 172.28.0.0/16).
  - Conteneurs : `zabbix-db` (PostgreSQL 15-alpine) et `zabbix-server` (v6.4-alpine).
- **2. Poste Cible Windows** : Une machine virtuelle Windows légère intégrée au périmètre et supervisée via l'agent Zabbix.
- **3. Switch Réseau (GNS3)** : Un équipement réseau simulé sous GNS3 pour valider la supervision d'infrastructure (tests de connectivité et métriques réseau).

## Organisation des Playbooks Ansible
1. `playbook_init.yml` : Préparation de l'hôte et installation de Docker / Docker Compose.
2. `playbook_deploy_zabbix.yml` : Déploiement de la stack Zabbix et automatisation de la restauration de la base de données.
3. `playbook_configure.yml` : Configuration des cibles (Linux, Windows, Switch), HTTPS et alertes mail.

## Lancement
```bash
ansible-playbook -i hosts playbook_init.yml
ansible-playbook -i hosts playbook_deploy_zabbix.yml
ansible-playbook -i hosts playbook_configure.yml
