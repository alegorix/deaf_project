# Installation d'Apache, PHP, MySQL et phpMyAdmin sur un Raspberry Pi

## 1. Mettre à jour le système
Avant toute installation, mets à jour les paquets disponibles :

```bash
sudo apt update && sudo apt upgrade -y
```

## 2. Installer Apache
Installe le serveur web Apache :

```bash
sudo apt install apache2 -y
```

Vérifie si Apache fonctionne :

```bash
sudo systemctl status apache2
```

Si nécessaire, démarre-le avec :

```bash
sudo systemctl start apache2
sudo systemctl enable apache2
```

### Tester Apache
Ouvre un navigateur et saisis l’adresse IP du Raspberry Pi :
```
http://[adresse-ip-du-raspberry]
```
Si la page par défaut d’Apache s’affiche, tout fonctionne.

## 3. Installer PHP

Apache peut traiter du PHP, mais il faut installer le module correspondant :  

```bash
sudo apt install php libapache2-mod-php -y
```

Vérifie que PHP est bien installé avec :

```bash
php -v
```

Installe des extensions utiles :

```bash
sudo apt install php-mysql php-curl php-xml php-mbstring php-zip php-gd -y
```

Redémarre Apache :

```bash
sudo systemctl restart apache2
```

### Tester PHP
Crée un fichier de test :

```bash
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
```

Puis visite :
```
http://[adresse-ip-du-raspberry]/info.php
```

## 4. Installer MySQL (MariaDB)

Installe MariaDB :

```bash
sudo apt install mariadb-server mariadb-client -y
```

Sécurise l’installation :

```bash
sudo mysql_secure_installation
```

Crée une base de données et un utilisateur :

```bash
sudo mysql -u root -p
```
```sql
CREATE DATABASE mon_site;
CREATE USER 'user'@'localhost' IDENTIFIED BY 'motdepasse';
GRANT ALL PRIVILEGES ON mon_site.* TO 'user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### Tester la connexion PHP/MySQL

Crée un fichier PHP de test :

```bash
echo "<?php
$pdo = new PDO('mysql:host=localhost;dbname=mon_site', 'user', 'motdepasse');
echo 'Connexion réussie à MySQL!';
?>" | sudo tee /var/www/html/test_db.php
```

Puis visite :
```
http://[adresse-ip-du-raspberry]/test_db.php
```

## 5. Installer phpMyAdmin

Installe phpMyAdmin :

```bash
sudo apt install phpmyadmin -y
```

Sélectionne **Apache2** et choisis **"Non"** pour `dbconfig-common`.

### Ajouter phpMyAdmin à Apache

```bash
sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin
sudo systemctl restart apache2
```

### Configurer MySQL pour phpMyAdmin

```bash
sudo mysql -u root -p
```

```sql
CREATE USER 'adminphp'@'localhost' IDENTIFIED BY 'motdepasse';
GRANT ALL PRIVILEGES ON *.* TO 'adminphp'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;
```

### Tester phpMyAdmin

Ouvre :
```
http://[adresse-ip-du-raspberry]/phpmyadmin
```

Connecte-toi avec **adminphp** et le mot de passe défini.

### Sécuriser phpMyAdmin (optionnel)

Restreindre l’accès :

```bash
sudo nano /etc/apache2/conf-available/phpmyadmin.conf
```
Ajoute sous `<Directory /usr/share/phpmyadmin>` :
```
AllowOverride All
```

Puis protège avec un mot de passe :

```bash
sudo apt install apache2-utils -y
sudo htpasswd -c /etc/phpmyadmin/.htpasswd admin
```
Ajoute cette protection :
```
<Directory /usr/share/phpmyadmin>
    AuthType Basic
    AuthName "Accès Restreint"
    AuthUserFile /etc/phpmyadmin/.htpasswd
    Require valid-user
</Directory>
```

Redémarre Apache :
```bash
sudo systemctl restart apache2
```



