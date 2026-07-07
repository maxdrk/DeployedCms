# 🚀 Déploiement de mon Portfolio (CMS Apache + Nginx Proxy Manager)

Ce projet automatise le déploiement d'un CMS (Portfolio) sur un serveur distant en utilisant **Ansible** pour l'orchestration et la copie des fichiers, et **Docker Compose** pour la gestion des conteneurs (Serveur Web Apache et Reverse Proxy).

---

## 🏗️ Architecture du Projet

Le projet est séparé en deux briques principales qui communiquent sur le même réseau virtuel Docker :
1. **CMS (Apache2)** : Conteneur personnalisé qui héberge le code source du portfolio. (le CMS choisit est CMS simple https://www.cmsimple.org/)
2. **Nginx Proxy Manager (NPM)** : Gère les certificats SSL (HTTPS) et redirige le trafic public vers le CMS.

---

## 📦 1. La Configuration Docker

### Le Dockerfile (`./cms/Dockerfile`)
Il construit l'image personnalisée pour le CMS à partir d'une base Ubuntu Apache.
* Installe les utilitaires de base (`less`, `nano`).
* Prépare le dossier `/var/www/cms` pour recevoir le code source.
* Expose le port interne `80`.

### Le Docker Compose (`./docker-compose.yml`)
Il orchestre les deux services sans avoir besoin de spécifier de version de syntaxe (norme moderne) :
* **`my-cms`** : Construit l'image locale via le Dockerfile et monte le dossier `./cms` dans le conteneur pour synchroniser le code.
* **`proxy-manager`** : Utilise l'image `jlesage/nginx-proxy-manager`. Il ouvre les ports publics `80` (HTTP), `443` (HTTPS) et `81` (Interface d'administration).

---

## 🤖 2. L'Automatisation avec Ansible

Ansible est utilisé pour préparer le serveur distant et y pousser proprement les fichiers sans action manuelle laborieuse.

### L'Inventaire (`inventory/inventory.ini`)
Il liste les serveurs cibles et configure la connexion par mot de passe (nécessite l'outil `sshpass` sur la machine de contrôle mais dans le futur l'utilisation de certficat se fera donc plus besoin de sshpass) :
```ini
[apache2]
127.0.0.1 "loopback utilisé uniquement pour le readme" ansible_user=ansible ansible_password=TON_MOT_DE_PASSE