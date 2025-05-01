# Nextcloud-Server-sur-un-serveur-Ubuntu

Voici un guide étape par étape pour installer **Nextcloud Server** sur un serveur **Ubuntu** (20.04 ou 22.04) :

---

## 🧰 **Pré-requis**

* Serveur Ubuntu à jour
* Accès root ou `sudo`
* Nom de domaine pointé vers votre serveur (ex : `cloud.exemple.com`) – **optionnel mais recommandé**
* Avoir un serveur LAMP ou LEMP installé

---

## 📦 Étape 1 : Mise à jour du système

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 🧱 Étape 2 : Installation des paquets nécessaires

```bash
sudo apt install apache2 mariadb-server libapache2-mod-php \
php php-gd php-mysql php-curl php-mbstring php-intl php-gmp \
php-bcmath php-xml php-zip php-cli php-common php-imagick \
unzip wget curl -y
```

---

## 🗃️ Étape 3 : Configuration de la base de données MariaDB

```bash
sudo mysql_secure_installation
```

Puis :

```bash
sudo mysql -u root -p
```

```sql
CREATE DATABASE nextcloud;
CREATE USER 'nextclouduser'@'localhost' IDENTIFIED BY 'motdepassefort';
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextclouduser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## ☁️ Étape 4 : Télécharger et déployer Nextcloud

```bash
cd /tmp
wget https://download.nextcloud.com/server/releases/latest.zip
unzip latest.zip
sudo mv nextcloud /var/www/html/
sudo chown -R www-data:www-data /var/www/html/nextcloud
sudo chmod -R 755 /var/www/html/nextcloud
```

---

## 🌐 Étape 5 : Configurer Apache

Créer un fichier de configuration :

```bash
sudo nano /etc/apache2/sites-available/nextcloud.conf
```

### Exemple de configuration :

```apache
<VirtualHost *:80>
    ServerAdmin admin@exemple.com
    DocumentRoot /var/www/html/nextcloud/
    ServerName cloud.exemple.com

    <Directory /var/www/html/nextcloud/>
        Options +FollowSymlinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/nextcloud_error.log
    CustomLog ${APACHE_LOG_DIR}/nextcloud_access.log combined
</VirtualHost>
```

Activer le site :

```bash
sudo a2ensite nextcloud.conf
sudo a2enmod rewrite headers env dir mime
sudo systemctl restart apache2
```

---

## 🔐 (Optionnel mais recommandé) Étape 6 : Activer HTTPS avec Let's Encrypt

```bash
sudo apt install certbot python3-certbot-apache -y
sudo certbot --apache -d cloud.exemple.com
```

---

## ✅ Étape 7 : Terminer l’installation via l’interface web

Accédez à :

```
http://cloud.exemple.com
```

ou :

```
http://IP_DU_SERVEUR/nextcloud
```

Configurez :

* Un nom d’utilisateur admin
* Le mot de passe admin
* Les infos de la base de données (nom : `nextcloud`, utilisateur : `nextclouduser`, mot de passe)

