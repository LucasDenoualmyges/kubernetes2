# 🚀 Projet Kubernetes – Déploiement des Applications de IC Group

## 🎯 Objectif

Ce projet vise à **conteneuriser**, **configurer** et **déployer** les applications du IC Group dans un cluster **Kubernetes**, tout en assurant la **persistance des données**.  
Applications concernées :
- **Odoo 13.0** : ERP open source
- **pgAdmin 4** : Interface web pour l'administration PostgreSQL
- **Application Web Flask** : Fournit une interface d'accès aux deux services ci-dessus

🔗 **Source du projet** : [GitHub Repository](https://github.com/OlivierKouokam/mini-projet-5esgi)

---

## ✅ Prérequis

- Cluster Kubernetes fonctionnel  
- Docker installé  
- Compte Docker Hub  
- Compte GitHub

---

## 🧱 Architecture des services

Déploiement de **trois conteneurs** dans le namespace `icgroup` :
- `PostgreSQL`
- `Odoo` (exposé sur le port **30090**)
- `pgAdmin` (exposé sur le port **30091**)

---

## 📥 Clonage du projet

```bash
git clone https://github.com/qahtan7/kubeprojet-esgi.git
cd ic-webapp
````

---

## 🐳 Création de l'application Flask avec Docker

### 📄 Dockerfile

```dockerfile
FROM python:3.6-alpine

WORKDIR /opt
COPY . .

RUN pip install flask==1.1.2

EXPOSE 8080

ENV ODOO_URL="https://www.odoo.com"
ENV PGADMIN_URL="https://www.pgadmin.org"

ENTRYPOINT ["python", "app.py"]
```

### ⚙️ Construction et test

```bash
docker build -t ic-webapp:1.0 .
docker image ls
docker run -d --name test-ic-webapp -p 8080:8080 \
  -e ODOO_URL="https://www.odoo.com" \
  -e PGADMIN_URL="https://www.pgadmin.org" \
  ic-webapp:1.0
docker ps -a
```

🔗 Accès à l’application :
[http://192.177.10.140:8080](http://192.177.10.140:8080)

---

## 📤 Publication sur Docker Hub

```bash
docker login
docker tag ic-webapp:1.0 aslimani94470/ic-webapp:1.0
docker push aslimani94470/ic-webapp:1.0
```

### 🧹 Nettoyage

```bash
docker stop <container_id>
docker rm <container_id>
```

---

## ☸️ Déploiement sur Kubernetes

### 📁 Création du namespace et déploiement de l’application web

```bash
kubectl create ns icgroup 
kubectl apply -f ic-webapp-deployment.yaml -n icgroup  
kubectl apply -f ic-webapp-service.yaml -n icgroup  
```

### 🗃️ Déploiement de PostgreSQL

```bash
kubectl apply -f postgresql.yaml -n icgroup  
```

Création de l’utilisateur `odoo` :

```bash
kubectl exec -it <pod-name> -n icgroup -- bash
psql -U postgres
CREATE USER odoo WITH PASSWORD 'odoo';
ALTER USER odoo CREATEDB;
```

### ⚙️ Déploiement de Odoo

```bash
kubectl apply -f odoo.yaml -n icgroup
```

### 🧰 Déploiement de pgAdmin

```bash
kubectl apply -f pgadmin.yaml -n icgroup
```

---

## 🔍 Vérifications

### 📦 État des ressources

```bash
kubectl get all -n icgroup
```

### 🛠️ ConfigMap

```bash
kubectl get cm -n icgroup
```

### 💾 Volumes persistants

```bash
kubectl get pvc -n icgroup
```

---

## 🌐 Interfaces Web

### Odoo

[http://192.168.10.42:30090](http://192.168.10.42:30090)
➡️ Interface Odoo fonctionnelle
➡️ Création de la base de données Odoo possible

### pgAdmin

[http://192.168.10.42:30091](http://192.168.10.42:30091)
➡️ Interface pgAdmin disponible
➡️ Connexion avec l'utilisateur `odoo` vérifiée

---

✅ **Déploiement complet et opérationnel !**

