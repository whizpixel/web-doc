# 🔴 InfoTools — CRM Web

> Extranet CRM pour commerciaux · BTS SIO SLAM · Session 2024

![Laravel](https://img.shields.io/badge/Laravel-12.x-E5383B?style=flat-square&logo=laravel&logoColor=white)
![PHP](https://img.shields.io/badge/PHP-8.4-777BB4?style=flat-square&logo=php&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-8.0-4479A1?style=flat-square&logo=mysql&logoColor=white)
![Tailwind](https://img.shields.io/badge/Tailwind-3.x-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white)

---

## 📋 Contexte

Dans le cadre du projet **InfoTools** (AP 2ème année BTS SIO), ce dépôt contient la **Mission 2** : l'application CRM Web destinée aux commerciaux.

La société Info-Tools développe et vend différents logiciels. Cet extranet permet à ses commerciaux de gérer leurs rendez-vous, consulter le catalogue produits et suivre l'historique d'achats de leurs clients.

---

## 📚 Documentation
 
La documentation complète est disponible sur : **[whizpixel.github.io/web-doc](https://whizpixel.github.io/web-doc)**

---

## ✨ Fonctionnalités

- 🏠 **Dashboard** — KPIs personnalisés + prochains RDV du commercial connecté
- 📅 **Rendez-vous** — CRUD complet (création, modification, suppression)
- 📦 **Produits** — Catalogue InfoTools (nom, prix, description)
- 👥 **Clients & Achats** — Historique d'achats par client (filtré par commercial)
- 🔐 **API REST** — Endpoints sécurisés via Laravel Sanctum
- 📝 **Audit Logs** — Journalisation des actions sensibles en BDD

---

## 🛡️ Sécurité

| Mesure | Détail |
|--------|--------|
| **IDOR Protection** | Filtrage `user_id` sur tous les accès aux données sensibles |
| **Sanctum Auth** | Tokens personnels pour l'API, sessions CSRF pour le web |
| **Throttling** | Login API limité à 10 req/min |
| **Validation serveur** | FormRequests sur tous les endpoints API |
| **Audit Logs** | Table `audit_logs` avec user, action, entity, meta JSON |

---

## 🚀 Installation

### Prérequis

- PHP 8.4+
- Composer
- Node.js 18+
- MySQL 5.7+

### Étapes

```bash
# 1. Cloner le dépôt
git clone https://github.com/whizpixel/web-ap.git
cd web-ap

# 2. Dépendances
composer install
npm install

# 3. Environnement
cp .env.example .env
php artisan key:generate
# → Renseigner DB_DATABASE, DB_USERNAME, DB_PASSWORD dans .env

# 4. Base de données
php artisan migrate:fresh --seed

# 5. Lancer
php artisan serve    # Terminal 1
npm run dev          # Terminal 2
```

### Comptes de test

```bash
php artisan tinker
>>> App\Models\User::all(['id', 'name', 'email'])
```

> Mot de passe de tous les comptes seedés : `password`

---

## 🗂️ Structure

```
app/
├── Http/Controllers/
│   ├── AppointmentController.php   # CRUD rendez-vous (web)
│   ├── ClientController.php        # Liste clients + achats
│   ├── ProductController.php       # Catalogue produits
│   └── Api/
│       ├── ProductController.php   # API produits
│       └── PurchaseController.php  # API achats + audit log
├── Models/
│   ├── User.php
│   ├── Appointment.php
│   ├── Client.php
│   ├── Product.php
│   ├── Purchase.php
│   └── AuditLog.php
routes/
├── web.php       # Routes session (dashboard, rdv, produits, clients)
├── api.php       # Routes API Sanctum
└── auth.php      # Login / register / reset
resources/views/
├── layouts/app.blade.php
├── dashboard.blade.php
├── appointments/
├── products/
└── clients/
```

---

## 🔌 API

Authentification : `POST /api/login` → retourne un token Bearer

| Méthode | Route | Description |
|---------|-------|-------------|
| `GET` | `/api/products` | Liste des produits |
| `POST` | `/api/purchases` | Créer un achat |
| `GET` | `/api/clients/{id}/purchases` | Achats d'un client |
| `DELETE` | `/api/purchases/{id}` | Supprimer un achat |

---

## 🗃️ Base de données

```
users ──────────────┐
                    ├──< appointments
                    └──< purchases >──── clients
                                  └──── products
audit_logs (← purchases)
```

---

## 👤 Auteur

Projet réalisé dans le cadre du **BTS SIO option SLAM** — Lycée Mathias, Chalon-sur-Saône  
Session **2024**

---

*InfoTools CRM Web — Mission 2 / 4*
