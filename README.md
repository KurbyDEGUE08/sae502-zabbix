# SAÉ 5.02 - Déploiement automatisé et supervision multi-environnements avec Zabbix

## Description du Projet
Ce projet s'inscrit dans le cadre de la **SAÉ 5.02**. Il consiste à automatiser le déploiement, la configuration et la gestion d'une plateforme de supervision **Zabbix** conteneurisée via **Docker Compose**, et pilotée par des playbooks **Ansible**, sur une maquette de démonstration isolée.

## Architecture du Socle (Maquette à 3 composants)
L'infrastructure de test repose sur trois éléments clés :

1. **Le Serveur Zabbix (Le Cerveau)** :
   - Machine Virtuelle **Ubuntu 22.04 LTS** (hébergée sur l'environnement de test personnel).
   - Orchestration via **Docker & Docker Compose** sur un réseau bridge dédié (`zabbix_net` : `172.28.0.0/16`).
   - Conteneurs : `zabbix-db` (PostgreSQL 15-alpine) et `zabbix-server` (v6.4-alpine).

2. **Le Poste Cible Windows (Cible n°1)** :
   - Machine Virtuelle Windows créée et configurée manuellement pour simuler un poste ou un serveur d'entreprise.
   - Supervision active de la santé du système (CPU, RAM, stockage) via l'agent Zabbix.

3. **Le Switch Virtuel GNS3 (Cible n°2 / Réseau)** :
   - Équipement réseau simulé via **GNS3**.
   - Supervision des équipements réseaux (tests de connectivité et métriques via le protocole SNMP).

## Organisation du Dépôt et Playbooks Ansible
L'automatisation est structurée autour des playbooks suivants :
- `hosts` : Fichier d'inventaire des cibles.
- `playbook_init.yml` : Préparation de l'hôte et installation des prérequis (Docker & Docker Compose).
- `playbook_deploy_zabbix.yml` : Déploiement de la stack Zabbix et automatisation de la restauration de la base de données.
- `playbook_configure.yml` : Configuration des cibles (Windows et Switch), sécurisation HTTPS et mise en place des alertes mail.

## Lancement du Déploiement
Pour exécuter les playbooks d'automatisation sur l'environnement :
```bash
ansible-playbook -i hosts playbook_init.yml
ansible-playbook -i hosts playbook_deploy_zabbix.yml
ansible-playbook -i hosts playbook_configure.yml
