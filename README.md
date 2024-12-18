# Rapport - DevOps Livecoding

## **Introduction**
Ce projet a pour objectif de mettre en place un environnement complet de DevOps en utilisant des outils modernes tels que **GitHub Actions**, **Docker**, **Ansible** et **SonarCloud**. L'objectif principal est d'automatiser les processus de **CI/CD**, de contrôler la qualité du code et de simplifier le **déploiement d'une application web** à l'aide d'outils de conteneurisation et d'automatisation.

Le dépôt du projet est accessible ici : [Dépôt GitHub](https://github.com/acbbiste17/devops-livecoding.git)

---

## **Table des Matières**
1. [**Prérequis**](#prérequis)
2. [**TP2 - GitHub Actions**](#tp2---github-actions)
3. [**TP3 - Ansible**](#tp3---ansible)
4. [**Structure du Projet**](#structure-du-projet)
5. [**Conclusion**](#conclusion)

---

## **Prérequis**
- **Git**
- **Docker et Docker Compose**
- **Ansible**
- **Accès à GitHub** (pour configurer les **Secrets GitHub** et exécuter **GitHub Actions**)

Assurez-vous d'avoir ces outils installés sur votre machine avant de commencer.

---

## **TP2 - GitHub Actions**

### **1. Introduction**
Dans ce TP, nous configurons un pipeline CI avec **GitHub Actions** pour tester et valider automatiquement chaque changement apporté au projet.

### **2. Configuration de GitHub Actions**

**Fichier de configuration** : `.github/workflows/main.yml`

**Déclencheurs** :
   - À chaque `push` ou `pull request` sur les branches `main` et `develop`.

**Structure des Jobs** :
   - **Job `test-backend`**
     - **Environnement** : `ubuntu-22.04`
     - **Étapes** :
       - Clonage du dépôt
       - Configuration de JDK 17
       - Construction et exécution des tests Maven.

### **3. Qu'est-ce que Testcontainers ?**
Les **Testcontainers** sont des bibliothèques Java qui permettent de lancer des conteneurs Docker lors des tests. Elles permettent de tester les interactions avec des bases de données réelles ou d'autres services.

### **4. Pourquoi pousser des images Docker ?**
Pousser des images Docker vers un registre permet de centraliser le stockage des images et de faciliter le déploiement cohérent de l'application dans divers environnements. Cela permet de s'assurer que la même version de l'application est utilisée en production, en développement et dans les environnements de test. Les images Docker permettent également de rendre le déploiement reproductible et de simplifier les mises à jour.

### **5. Documentation de la passerelle de qualité avec SonarCloud**
La configuration de la passerelle de qualité se fait via **SonarCloud** pour garantir la qualité du code. Les étapes de configuration incluent :
- Création d'une organisation et d'un projet sur SonarCloud.
- Ajout d'un secret `SONAR_TOKEN` dans les secrets GitHub.
- Intégration de SonarCloud dans le pipeline GitHub Actions.

---

## **TP3 - Ansible**

### **1. Introduction**
Dans ce TP, nous automatisons le déploiement de l'application à l'aide de **Ansible**. Ce processus comprend la configuration des serveurs, l'installation de Docker et le déploiement des conteneurs.

### **2. Création des rôles avec Ansible Galaxy**
Les rôles ont été initialisés avec la commande suivante :
```bash
ansible-galaxy init roles/<nom_du_role>
```
Cette commande a permis de créer une structure de répertoires propre pour chaque rôle, facilitant l'organisation et la maintenance des tâches Ansible.

### **3. Les rôles principaux**
Les rôles mis en place dans le projet sont conformes aux exigences de l'énoncé :

1. **install_docker** : Installe Docker sur le serveur distant.
2. **create_network** : Crée un réseau Docker pour permettre la communication entre les conteneurs.
3. **launch_database** : Lance le conteneur de la base de données PostgreSQL avec un volume persistant.
4. **launch_app** : Déploie l'application backend.
5. **launch_proxy** : Lance le serveur proxy HTTP (Apache ou NGINX) pour rediriger les requêtes vers l'application.

### **4. Playbook principal**
Le playbook **`ansible/playbook.yml`** orchestre ces rôles dans l'ordre requis :
```yaml
- hosts: prod
  become: true
  roles:
    - install_docker
    - create_network
    - launch_database
    - launch_app
    - launch_proxy
```

**Exécution du playbook** :
```bash
ansible-playbook -i ansible/inventories/setup.yml ansible/playbook.yml
```

---

## **Structure du Projet**
```
├── .github
│   └── workflows
│       ├── bonus.yml
│       └── main.yml
├── ansible
│   ├── inventories
│   ├── roles
│   │   ├── application
│   │   ├── database
│   │   ├── docker
│   │   ├── network
│   │   └── proxy
│   └── playbook.yml
├── database
├── http-server
├── simple-api
├── docker-compose.yml
└── README.md
```

**Explications des principaux répertoires :**
- **.github/workflows** : Contient les fichiers de configuration des **GitHub Actions**.
- **ansible** : Contient les fichiers Ansible, les inventaires et les rôles nécessaires au déploiement.
- **simple-api** : Contient le code source de l'API.
- **docker-compose.yml** : Fichier permettant de lancer les services avec Docker Compose.



---

## **Conclusion**
Ce projet DevOps a permis de mettre en place une solution complète pour l'automatisation des tests, la construction et le déploiement de l'application tout en assurant la qualité et la maintenance du code. Les outils **GitHub Actions**, **Ansible** et **Docker** ont été utilisés pour construire une infrastructure robuste et automatisée. 

La séparation des rôles avec **Ansible Galaxy** a permis de structurer efficacement les tâches de déploiement pour les différentes composantes : **Docker**, **réseau**, **base de données**, **application backend**, et **serveur proxy**. Grâce à cette architecture, l'application est prête à être déployée de manière automatisée et reproductible dans n'importe quel environnement.

