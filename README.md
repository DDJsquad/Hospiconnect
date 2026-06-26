# HospiConnect — Clinique du Chatelet
## Guide d'installation & Déploiement

---

## Prérequis
- **XAMPP** (PHP 8.x + MySQL 8.0 + Apache)
- Navigateur moderne (Chrome, Firefox, Edge)
- Connexion SMTP valide pour les emails

---

## Installation (XAMPP)

### 1. Copier les fichiers
```
C:\xampp\htdocs\hospiconnect\
```

### 2. Importer la base de données
1. Démarrer XAMPP → Apache + MySQL
2. Ouvrir **phpMyAdmin** : http://localhost/phpmyadmin
3. Créer une base `hospiconnect` (ou laisser le script le faire)
4. Aller dans **Importer** → sélectionner `sql/hospiconnect.sql`
5. Cliquer **Exécuter**

### 3. Configurer l'application
Ouvrir `includes/config.php` et ajuster :

```php
// Base de données
define('DB_HOST', 'localhost');
define('DB_NAME', 'hospiconnect');
define('DB_USER', 'root');
define('DB_PASS', '');          // Vide par défaut sous XAMPP

// URL de base
define('APP_URL', 'http://localhost/hospiconnect');

// SMTP Email (exemple Gmail)
define('SMTP_HOST', 'smtp.gmail.com');
define('SMTP_PORT', 587);
define('SMTP_USER', 'votre.email@gmail.com');
define('SMTP_PASS', 'votre_mot_de_passe_app');  // Mot de passe d'application Gmail
define('SMTP_FROM', 'noreply@clinique-chatelet.fr');
```

### 4. Accéder à l'application
```
http://localhost/hospiconnect/
```

---

## Comptes par défaut

| Profil      | Login         | Mot de passe | Accès                          |
|-------------|---------------|--------------|-------------------------------|
| Superadmin  | `admin`       | `admin`      | Administration complète        |
| Secrétaire  | `secretaire1` | `password`   | Secrétariat (service: Consult.)|
| Médecin     | `dr.martin`   | `password`   | File d'attente                 |

>  **IMPORTANT** : Changer tous les mots de passe par défaut avant mise en production !

---

## Structure des fichiers

```
hospiconnect/
├── index.php                    ← Page de connexion
├── includes/
│   ├── config.php               ← Configuration centrale
│   ├── db.php                   ← Connexion PDO
│   ├── functions.php            ← Fonctions utilitaires
│   ├── header.php               ← Header HTML commun
│   └── footer.php               ← Footer HTML commun
├── assets/
│   ├── css/hospiconnect.css     ← Styles principaux
│   ├── js/hospiconnect.js       ← JavaScript principal
│   └── img/logo_clinique.png    ← Logo de la clinique
├── modules/
│   ├── auth/
│   │   ├── logout.php
│   │   ├── forgot_password.php
│   │   └── reset_password.php
│   ├── secretariat/
│   │   ├── dashboard.php        ← Tableau de bord secrétaire
│   │   ├── patient_new.php      ← Enregistrement + ticket
│   │   ├── patient_search.php   ← Recherche patient
│   │   ├── patient_edit.php     ← Édition fiche patient
│   │   └── examens.php          ← Suivi examens
│   ├── medecin/
│   │   └── dashboard.php        ← File d'attente médecin
│   ├── admin/
│   │   ├── dashboard.php        ← Tableau de bord admin
│   │   ├── users.php            ← Gestion utilisateurs
│   │   ├── services.php         ← Gestion services
│   │   ├── stats.php            ← Statistiques
│   │   └── logs.php             ← Journal d'audit (superadmin)
│   └── affichage/
│       └── salle.php            ← Kiosque salle d'attente
├── api/
│   ├── queue.php                ← File d'attente (AJAX polling)
│   ├── ticket_action.php        ← Actions sur tickets
│   ├── patient_search.php       ← Autocomplétion patient
│   ├── kiosque.php              ← Données écran salle
│   └── services_list.php        ← Liste services
└── sql/
    └── hospiconnect.sql         ← Script initialisation BDD
```

---

## Fonctionnalités clés

### Authentification
- Login page unique → redirection automatique selon rôle
- Blocage temporaire après 5 tentatives échouées (15 min)
- Session expiration : 30 minutes d'inactivité
- Réinitialisation MDP par email (lien valable 1h)

### Secrétariat
- Recherche patient en temps réel (auto-complétion dès 3 caractères)
- Création fiche patient + ticket en une étape
- Email automatique envoyé au patient à la création du ticket
- Gestion des niveaux de priorité (Standard / Prioritaire / Urgence)
- Suivi et notification des résultats d'examens

### Médecin
- File d'attente en temps réel (AJAX polling toutes les 15 secondes)
- Tri automatique : Urgence > Prioritaire > Standard
- Appel ticket → affiché sur écran salle d'attente
- Transfert inter-services sans ressaisie
- Notes de consultation

### Écran salle d'attente
- Interface kiosque plein écran
- Ticket actuel affiché en grand
- Signal sonore + synthèse vocale à chaque appel
- Historique des derniers tickets appelés
- Rafraîchissement toutes les 5 secondes

### Administration
- Gestion complète du personnel (CRUD comptes)
- Configuration services et salles
- Statistiques : temps d'attente moyen, affluence par heure
- Alertes : attente > 45 min, file pleine > 20 patients
- Export CSV des statistiques
- Journal d'audit complet (superadmin uniquement)

---

## Configuration email (Gmail)
1. Activer la **validation en 2 étapes** sur votre compte Google
2. Générer un **mot de passe d'application** :
   - Compte Google → Sécurité → Mots de passe des applications
3. Renseigner ce mot de passe dans `config.php` → `SMTP_PASS`

---

## Sécurité — Checklist production
- [ ] Changer les mots de passe par défaut (admin/admin, password)
- [ ] Configurer SMTP avec un vrai compte
- [ ] Activer HTTPS (certificat SSL)
- [ ] Restreindre l'accès à `/admin/logs.php` au réseau interne
- [ ] Sauvegardes automatiques quotidiennes de la base de données
- [ ] Mettre à jour `APP_URL` avec le vrai domaine

---

## Crédits
**HospiConnect v1.0** — Clinique du Chatelet  
Développé par **Junior DJOUAKEP**  
Contact : [wa.me/+237671384110] (https://wa.me/+237671384110)
