# 🗄️ SISR — Configuration MySQL pour InfoTools CRM

> Projet InfoTools · BTS SIO 2024

---

## 📦 Installer MySQL

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install mysql-server -y
sudo systemctl enable mysql
sudo systemctl start mysql
```

---

## 🔒 Sécuriser l'installation

```bash
sudo mysql_secure_installation
```

- **Remove anonymous users** → `Oui`
- **Disallow root login remotely** → `Oui`
- **Remove test database** → `Oui`
- **Reload privilege tables** → `Oui`

---

## 🗃️ Créer la base et l'utilisateur

```bash
sudo mysql -u root -p
```

```sql
-- Base de données
CREATE DATABASE infotools_crm
    CHARACTER SET utf8mb4
    COLLATE utf8mb4_unicode_ci;

-- Utilisateur applicatif
CREATE USER 'infotools'@'%' IDENTIFIED BY 'MotDePasseForte2024!';

-- Droits
GRANT ALL PRIVILEGES ON infotools_crm.* TO 'infotools'@'%';
FLUSH PRIVILEGES;

-- Vérification
SHOW DATABASES;
EXIT;
```

---

## 🌐 Autoriser les connexions distantes

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Modifier :

```ini
bind-address = 0.0.0.0
```

```bash
sudo systemctl restart mysql
```

---

## ⚙️ Configuration .env côté SLAM

Transmettre ces informations à l'équipe SLAM pour qu'ils renseignent leur `.env` :

```env
DB_CONNECTION=mysql
DB_HOST=<IP_DU_SERVEUR_BDD>
DB_PORT=3306
DB_DATABASE=infotools_crm
DB_USERNAME=infotools
DB_PASSWORD=MotDePasseForte2024!
```

Une fois le `.env` configuré, l'équipe SLAM lancera :

```bash
php artisan migrate:fresh --seed
```

---

> ⚠️ Penser à noter le mot de passe dans le fichier de mots de passe du dossier projet SISR.
