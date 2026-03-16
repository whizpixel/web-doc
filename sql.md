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

## 📐 Structure de la base de données

> Ces tables sont créées automatiquement par Laravel via `php artisan migrate`. Ce schéma est fourni à titre indicatif.

```sql
-- Utilisateurs (commerciaux)
CREATE TABLE users (
    id              BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name            VARCHAR(255) NOT NULL,
    email           VARCHAR(255) NOT NULL UNIQUE,
    email_verified_at TIMESTAMP NULL,
    password        VARCHAR(255) NOT NULL,
    remember_token  VARCHAR(100) NULL,
    created_at      TIMESTAMP NULL,
    updated_at      TIMESTAMP NULL
);

-- Rendez-vous
CREATE TABLE appointments (
    id          BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id     BIGINT UNSIGNED NOT NULL,
    client_name VARCHAR(255) NOT NULL,
    starts_at   DATETIME NOT NULL,
    notes       TEXT NULL,
    created_at  TIMESTAMP NULL,
    updated_at  TIMESTAMP NULL,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

-- Produits
CREATE TABLE products (
    id          BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name        VARCHAR(255) NOT NULL,
    price       DECIMAL(10,2) DEFAULT 0,
    description TEXT NULL,
    created_at  TIMESTAMP NULL,
    updated_at  TIMESTAMP NULL
);

-- Clients
CREATE TABLE clients (
    id         BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name       VARCHAR(255) NOT NULL,
    email      VARCHAR(255) NULL,
    phone      VARCHAR(255) NULL,
    created_at TIMESTAMP NULL,
    updated_at TIMESTAMP NULL
);

-- Achats
CREATE TABLE purchases (
    id           BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id      BIGINT UNSIGNED NOT NULL,
    client_id    BIGINT UNSIGNED NOT NULL,
    product_id   BIGINT UNSIGNED NOT NULL,
    quantity     INT UNSIGNED DEFAULT 1,
    purchased_at DATETIME NOT NULL,
    notes        VARCHAR(255) NULL,
    created_at   TIMESTAMP NULL,
    updated_at   TIMESTAMP NULL,
    FOREIGN KEY (user_id)   REFERENCES users(id)    ON DELETE CASCADE,
    FOREIGN KEY (client_id) REFERENCES clients(id)  ON DELETE CASCADE,
    FOREIGN KEY (product_id)REFERENCES products(id) ON DELETE CASCADE,
    INDEX idx_user_client (user_id, client_id)
);

-- Logs d'audit
CREATE TABLE audit_logs (
    id          BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id     BIGINT UNSIGNED NULL,
    action      VARCHAR(255) NOT NULL,
    entity_type VARCHAR(255) NOT NULL,
    entity_id   BIGINT UNSIGNED NULL,
    meta        JSON NULL,
    created_at  TIMESTAMP NULL,
    updated_at  TIMESTAMP NULL,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_entity (entity_type, entity_id),
    INDEX idx_user_action (user_id, action)
);
```

### Schéma des relations

```
users ──────────────┬──< appointments
                    ├──< purchases >──── clients
                    └──< purchases >──── products
audit_logs (← liés aux purchases via entity_type/entity_id)
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
