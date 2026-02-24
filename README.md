
# 🌐 TP – Déploiement d’un serveur Apache sécurisé (HTTP → HTTPS)

## 📌 Objectif

Mettre en place un serveur web **Apache** sur une instance AWS, associer un **nom de domaine gratuit DuckDNS**, puis sécuriser le site avec un certificat SSL gratuit via **Let’s Encrypt**.

---

# 1️⃣ Prérequis

* Un compte AWS (Free Tier)
* Une instance EC2 Ubuntu (t2.micro ou t3.micro)
* Ports ouverts dans le Security Group :

  * 22 → SSH
  * 80 → HTTP
  * 443 → HTTPS
* Connexion SSH fonctionnelle (PuTTY ou terminal)
* Un compte sur DuckDNS.org

---

# 2️⃣ Installation d’Apache

Mise à jour du système :

```bash
sudo apt update
sudo apt upgrade -y
```

Installation d’Apache :

```bash
sudo apt install apache2 -y
```

Vérification :

```bash
sudo systemctl status apache2
```

Test via navigateur :

```
http://IP_PUBLIQUE
```

---

# 3️⃣ Configuration du nom de domaine DuckDNS

## Création du sous-domaine

* Connexion à DuckDNS
* Création du sous-domaine :

```
monserveur-tp.duckdns.org
```

* Récupération du **token**

---

## Script de mise à jour IP

Création du dossier :

```bash
mkdir -p ~/duckdns
cd ~/duckdns
```

Création du script :

```bash
nano duck.sh
```

Contenu :

```bash
echo url="https://www.duckdns.org/update?domains=monserveur-tp&token=VOTRE_TOKEN&ip=" | curl -k -o ~/duckdns/duck.log -K -
```

Rendre exécutable :

```bash
chmod +x duck.sh
./duck.sh
```

Vérification :

```bash
cat duck.log
```

Doit afficher :

```
OK
```

---

## Automatisation (important)

```bash
crontab -e
```

Ajouter :

```bash
*/5 * * * * ~/duckdns/duck.sh >/dev/null 2>&1
```

Cela permet de mettre à jour automatiquement l’IP si elle change.

---

# 4️⃣ Sécurisation HTTPS avec Let’s Encrypt

Installation de Certbot :

```bash
sudo apt install certbot python3-certbot-apache -y
```

Génération du certificat :

```bash
sudo certbot --apache
```

* Entrer l’adresse email
* Accepter les conditions
* Saisir le domaine :

```
monserveur-tp.duckdns.org
```

* Choisir la redirection HTTP → HTTPS

---

## Vérification finale

Navigateur :

```
https://monserveur-tp.duckdns.org
```

Le cadenas doit apparaître ✅

---

# 🔁 Gestion des changements

### Si l’IP publique change :

* Le script DuckDNS met automatiquement à jour le domaine (grâce au cron).

### Si le token DuckDNS change :

* Modifier le token dans `duck.sh`.

### Certificat SSL :

* Renouvellement automatique tous les 90 jours par Certbot.

---

# ✅ Résultat Final

* Serveur Apache opérationnel
* Domaine gratuit configuré
* HTTPS actif avec certificat SSL valide
* Mise à jour automatique de l’IP
* Infrastructure 100% fonctionnelle sur AWS Free Tier

---

