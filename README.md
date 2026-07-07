# 🚀 Déploiement de mon Portfolio (CMS Apache + Nginx Proxy Manager)

Ce projet automatise le déploiement d'un CMS (Portfolio) sur un serveur distant. 

L'orchestration et la copie des fichiers sont gérées par **Ansible** depuis une **VM de contrôle sous Debian**. L'infrastructure applicative est quant à elle conteneurisée avec **Docker Compose** (Serveur Web Apache et Reverse Proxy).

---

## 🏗️ Architecture et Environnement de Travail

Le déploiement s'articule autour d'un dossier partagé et de deux conteneurs Docker isolés :

* **Dossier Partagé (`sharedFolders`)** : Le code source du CMS et les fichiers de configuration Ansible sont stockés dans un dossier partagé (`/mnt/sharedFolders/...`) accessible directement depuis la VM Debian de contrôle.
* **CMS (Apache2)** : Conteneur personnalisé qui récupère le code du portfolio et l'exécute.
* **Nginx Proxy Manager (NPM)** : Gère les certificats SSL (HTTPS) et redirige le trafic public de manière sécurisée vers le CMS.

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

## 🤖 2. L'Automatisation avec Ansible (VM Debian)

Ansible est exécuté depuis la VM Debian pour préparer le serveur distant et y pousser proprement les fichiers.

### L'Inventaire (`inventory/inventory.ini`)
Il liste les serveurs cibles et configure la connexion par mot de passe (nécessite l'outil `sshpass` sur la VM) :
```ini
[apache2]
127.0.0.1 ansible_user=ansible ansible_password=TON_MOT_DE_PASSE