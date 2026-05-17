# 🇨🇬 Bridge-CG — Sondage National pour un Congo plus Digital

> Application web de sondage citoyen permettant aux Congolais de voter pour le projet numérique qui transformera le Congo-Brazzaville.

![Version](https://img.shields.io/badge/version-1.0.0-gold)
![Statut](https://img.shields.io/badge/statut-en%20production-brightgreen)
![Hébergement](https://img.shields.io/badge/hébergement-GitHub%20Pages-black)
![Backend](https://img.shields.io/badge/backend-Supabase-3ECF8E)

---

## 📋 Table des matières

1. [Présentation du projet](#-présentation-du-projet)
2. [Fonctionnement général](#-fonctionnement-général)
3. [Architecture du code](#-architecture-du-code)
4. [Technologies utilisées](#-technologies-utilisées)
5. [Design System](#-design-system)
6. [Base de données](#-base-de-données)
7. [Fonctionnalités détaillées](#-fonctionnalités-détaillées)
8. [Sécurité & anti-fraude](#-sécurité--anti-fraude)
9. [Temps réel (Realtime)](#-temps-réel-realtime)
10. [Déploiement](#-déploiement)
11. [Structure du projet](#-structure-du-projet)
12. [Contact](#-contact)

---

## 🎯 Présentation du projet

**Bridge-CG Sondage National** est une application web monopage (SPA) conçue pour consulter les citoyens congolais sur les projets numériques prioritaires pour le Congo-Brazzaville.

### Pourquoi ce projet ?

Le Congo-Brazzaville est en pleine transition numérique. Avant d'investir dans le développement d'une solution digitale, il est essentiel de **comprendre les besoins réels des citoyens**. Ce sondage permet de :

- 🗳️ **Recueillir des votes** de manière structurée et transparente
- 📊 **Afficher les résultats en temps réel** pour maintenir l'engagement
- 💬 **Collecter des commentaires** et suggestions libres
- 📈 **Prioriser** les projets selon la demande populaire

### Les 7 projets soumis au vote

| # | Projet | Problème résolu |
|---|--------|-----------------|
| 01 | 🏠 Plateforme de logement / immobilier | Arnaques des démarcheurs et manque d'infos fiables |
| 02 | 🎓 Système de gestion scolaire | Gestion papier opaque et rupture école-parents |
| 03 | 🛠️ Plateforme pour Artisans | Difficulté à trouver un bon artisan en urgence |
| 04 | 🚕 Application de transport local | Phénomène de demi-terrain et tarifs arbitraires |
| 05 | 🛒 Marketplace locale / vente en ligne | Manque de visibilité et achats à distance risqués |
| 06 | 🍽️ Gestion intelligente de restaurant | Lenteur du service et pertes de stocks |
| 07 | 🏥 Système de santé & Prise de RDV médical | Files d'attente interminables et médicaments introuvables |

---

## ⚙️ Fonctionnement général

L'application suit un **flux utilisateur simple en deux étapes** :

```
[Page de vote]
   ↓ L'utilisateur saisit son prénom + nom
   ↓ L'utilisateur choisit un projet parmi les 7
   ↓ (Optionnel) L'utilisateur laisse un commentaire
   ↓ Clic sur "Voter"
   ↓ INSERT en base de données Supabase
[Page de résultats]
   ↓ Affichage du classement en temps réel avec barres de progression
   ↓ Feed des 10 derniers votants
   ↓ Compteur total de votes mis à jour automatiquement
```

### Règle d'unicité du vote

Un utilisateur **ne peut voter qu'une seule fois** par navigateur. Ceci est géré via `localStorage` : après un vote réussi, deux clés sont stockées (`a_deja_vote = true`, `user_vote_id`). Au chargement de la page, si ces clés existent, l'utilisateur est directement redirigé vers les résultats.

> ⚠️ Cette protection est côté client uniquement. Elle peut être contournée en vidant le localStorage. Pour un sondage à haute sécurité, une authentification côté serveur serait nécessaire.

---

## 🏗️ Architecture du code

Le projet est une **Single Page Application (SPA) entièrement contenue dans un seul fichier `index.html`**, organisé en trois grandes zones :

```
index.html
├── <head>
│   ├── Métadonnées SEO (charset, viewport, title)
│   ├── Liens Google Fonts (Playfair Display + DM Sans)
│   ├── Script Supabase JS SDK (CDN)
│   └── <style> — Tout le CSS (Design System + composants)
│
├── <body>
│   ├── #loading-overlay      → Écran de chargement initial
│   ├── #toast                → Notification flottante (succès / erreur)
│   ├── .wrapper              → Conteneur principal centré (max 900px)
│   │   ├── <header>          → Titre + badge + sous-titre
│   │   ├── .counter-banner   → Compteur de votes live
│   │   ├── #vote-section     → Formulaire + grille de projets + bouton
│   │   ├── #results-section  → Résultats + barres + feed des votants
│   │   └── <footer>          → Copyright + coordonnées de contact
│   └── <script>              → Toute la logique JavaScript
│       ├── Configuration Supabase (URL + ANON_KEY)
│       ├── Noms des projets (objet PROJECT_NAMES)
│       ├── Gestion de l'état (selectedProjectId)
│       ├── Références DOM
│       ├── showToast()       → Affichage des notifications
│       ├── Sélection de carte projet (event listeners)
│       ├── fetchAndRenderStats() → Fetch + rendu des résultats
│       ├── submitBtn listener → Soumission + insertion du vote
│       ├── Canal Realtime Supabase
│       └── DOMContentLoaded → Initialisation
```

---

## 🛠️ Technologies utilisées

### Frontend

| Technologie | Version | Rôle |
|-------------|---------|------|
| **HTML5** | — | Structure sémantique de la page |
| **CSS3 Vanilla** | — | Design complet, animations, responsive |
| **JavaScript ES2020+** | — | Logique, interactions, appels API |
| **Google Fonts** | — | Typographies Playfair Display & DM Sans |

### Backend / Infrastructure

| Technologie | Version | Rôle |
|-------------|---------|------|
| **Supabase** | JS SDK v2 | Base de données PostgreSQL + API REST + Realtime |
| **Supabase Realtime** | — | WebSocket pour les mises à jour en direct |
| **GitHub Pages** | — | Hébergement statique gratuit |

### Pourquoi ces choix ?

- **Pas de framework JS (React, Vue…)** → Le projet est simple et rapide. Un fichier HTML suffit, ce qui facilite le déploiement et la maintenance.
- **Supabase plutôt que Firebase** → Open source, PostgreSQL (SQL standard), Realtime natif, généreux plan gratuit, CLI puissant.
- **CSS Vanilla plutôt que Tailwind** → Contrôle total sur le design, pas de dépendance de build, meilleure performance de chargement.
- **GitHub Pages** → Hébergement gratuit, déploiement automatique depuis le repository Git.

---

## 🎨 Design System

Tout le design est défini via des **variables CSS** (`:root`) pour garantir la cohérence visuelle :

```css
:root {
  /* Couleurs de fond (thème sombre) */
  --bg:        #0d0b08;   /* Fond principal très sombre */
  --bg2:       #161209;   /* Fond secondaire */
  --surface:   #1e1810;   /* Cartes / composants */
  --surface2:  #2a2015;   /* Cartes secondaires */

  /* Palette dorée (identité visuelle) */
  --gold:      #c9922a;   /* Or principal */
  --gold-lt:   #e8b84b;   /* Or clair (accents) */
  --amber:     #f0a500;   /* Ambre (dégradés) */
  --cream:     #f5ead6;   /* Texte principal */
  --muted:     #8a7a60;   /* Texte secondaire / muted */

  /* États */
  --danger:    #c0392b;   /* Erreurs */
  --success:   #27ae60;   /* Succès */

  /* Bordures */
  --border:    rgba(201,146,42,0.20);   /* Bordure subtile */
  --border-hl: rgba(201,146,42,0.60);   /* Bordure active */

  /* Typographies */
  --font-head: 'Playfair Display', Georgia, serif;   /* Titres élégants */
  --font-body: 'DM Sans', system-ui, sans-serif;     /* Corps de texte moderne */
}
```

### Effets visuels notables

- **Texture de fond** : Dégradés radiaux dorés subtils + pattern SVG en répétition (`body::before`)
- **Texte dégradé** : Les titres principaux utilisent `-webkit-background-clip: text` pour un effet gradient sur le texte
- **Animation fadeUp** : Les cartes apparaissent avec un effet de montée au chargement
- **Dot live** : Indicateur vert pulsant pour signaler l'activité temps réel
- **Hover sur les cartes** : Élévation (`translateY(-3px)`) + glow doré (`box-shadow`)

---

## 🗄️ Base de données

### Table `votes` (Supabase / PostgreSQL)

| Colonne | Type | Description |
|---------|------|-------------|
| `id` | `uuid` (PK) | Identifiant unique auto-généré |
| `nom_votant` | `text` | Prénom + Nom concatenés |
| `projet_vote` | `integer` | ID du projet choisi (1 à 7) |
| `message` | `text` (nullable) | Commentaire optionnel |
| `created_at` | `timestamptz` | Date/heure du vote (auto) |

### Requêtes utilisées

```javascript
// Lecture de tous les votes (pour les statistiques)
await sb.from('votes')
  .select('projet_vote, nom_votant, created_at')
  .order('created_at', { ascending: false });

// Insertion d'un nouveau vote
await sb.from('votes').insert([{
  nom_votant:  fullName,
  projet_vote: selectedProjectId,
  message:     message || null
}]);
```

### Configuration Supabase locale (CLI)

Le fichier `supabase/config.toml` configure l'environnement de développement local :
- **API** sur le port `54321`
- **Base de données** PostgreSQL sur le port `54322`
- **Studio** (interface admin) sur le port `54323`
- **Realtime** activé
- **Edge Functions** configurées (ex. `envoi-mail-vote`)

---

## ✨ Fonctionnalités détaillées

### 1. Écran de chargement
Un overlay plein écran avec un anneau de chargement animé s'affiche pendant l'initialisation. Il disparaît après 400ms une fois les données chargées (`overlay.classList.add('hidden')`).

### 2. Compteur de votes en direct
Une bannière affiche le nombre total de votes (`#total-votes`) mis à jour à chaque fetch. La valeur est calculée côté client en comptant les entrées retournées par Supabase.

### 3. Formulaire d'identité
- **Prénom** et **Nom** sont obligatoires pour soumettre un vote
- La validation est faite avant l'envoi : si un champ est vide, un toast d'erreur s'affiche
- Le nom complet est concaténé (`firstName + ' ' + lastName`) et stocké tel quel dans la base

### 4. Sélection de projet
- Chaque projet est une carte cliquable (`.project-card`)
- Au clic : toutes les autres cartes sont désélectionnées, la carte active reçoit la classe `.selected`
- Le bouton de soumission se déverrouille et affiche le nom du projet choisi
- La carte sélectionnée affiche une coche `✓` et un glow doré

### 5. Message optionnel
Un `<textarea>` permet d'ajouter un commentaire libre (suggestions, raisons du choix). Ce champ est facultatif et stocké dans la colonne `message` de la base de données.

### 6. Soumission du vote
1. Validation des champs (prénom + nom + projet sélectionné)
2. Désactivation du bouton + affichage du message de chargement
3. INSERT en base Supabase
4. En cas d'erreur : toast rouge + réactivation du bouton
5. En cas de succès : stockage dans `localStorage` + affichage des résultats

### 7. Page de résultats
- **Classement** des 7 projets triés par nombre de votes (décroissant)
- **Barres de progression** animées affichant le pourcentage
- **Vote de l'utilisateur** mis en évidence (bordure dorée + badge "Mon vote")
- **Comptage et pourcentage** calculés dynamiquement

### 8. Feed des derniers votants
Les 10 derniers votes sont affichés chronologiquement avec :
- **Avatar** généré depuis les initiales du votant (2 premières lettres)
- **Nom du votant** et projet choisi
- **Heure** du vote (format HH:MM)

### 9. Notifications Toast
Un système de notification flottante en bas de page affiche les messages :
- 🟡 **Info** : nouveaux votes reçus en temps réel
- 🟢 **Succès** : vote enregistré avec succès
- 🔴 **Erreur** : validation échouée ou erreur réseau

---

## 🔒 Sécurité & anti-fraude

| Mécanisme | Type | Description |
|-----------|------|-------------|
| `localStorage` | Côté client | Empêche le re-vote depuis le même navigateur |
| Clé anonyme Supabase | Côté serveur | La `ANON_KEY` est publique mais les RLS Policies Supabase peuvent restreindre les opérations |
| Validation JS | Côté client | Vérification des champs avant envoi |

> 💡 **Pour aller plus loin** : Implémenter des Row Level Security (RLS) sur Supabase et une vérification par numéro de téléphone ou email pour garantir un vote par personne.

---

## 📡 Temps réel (Realtime)

L'application utilise **Supabase Realtime** basé sur WebSocket pour écouter les nouveaux votes :

```javascript
sb.channel('votes-realtime')
  .on('postgres_changes', {
    event:  'INSERT',   // Déclenché à chaque nouvel INSERT
    schema: 'public',
    table:  'votes'
  }, () => {
    fetchAndRenderStats();  // Recharge et re-render les statistiques
    showToast('🔔 Un nouveau vote vient d\'être enregistré !', 'info');
  })
  .subscribe();
```

**Flux** : Dès qu'un utilisateur vote → INSERT en base → Supabase notifie tous les clients connectés via WebSocket → `fetchAndRenderStats()` est appelé → L'interface se met à jour sans rechargement de page.

---

## 🚀 Déploiement

### Hébergement : GitHub Pages

Le site est hébergé sur **GitHub Pages** depuis la branche `main`. Tout `git push` met automatiquement à jour le site en production.

```bash
# Déployer une mise à jour
git add .
git commit -m "Description des changements"
git push origin main
```

### Variables de configuration

Les identifiants Supabase sont déclarés en haut du script JavaScript :

```javascript
const SUPABASE_URL      = 'https://[projet].supabase.co';
const SUPABASE_ANON_KEY = 'sb_publishable_[clé]';
```

> ⚠️ La `ANON_KEY` est **publique par design** dans Supabase — elle est faite pour être exposée côté client. La sécurité repose sur les **Row Level Security (RLS)** configurées dans Supabase, pas sur la confidentialité de cette clé.

### Développement local avec Supabase CLI

```bash
# Démarrer l'environnement local Supabase
supabase start

# Accéder au Studio (interface admin locale)
# http://localhost:54323

# Arrêter l'environnement
supabase stop
```

---

## 📁 Structure du projet

```
site_sond_BCG/
│
├── index.html              ← Application entière (HTML + CSS + JS)
│
├── supabase/
│   ├── config.toml         ← Configuration de l'environnement Supabase local
│   ├── functions/
│   │   └── envoi-mail-vote/  ← Edge Function (envoi de mail de confirmation)
│   │       ├── index.ts
│   │       └── deno.json
│   └── .temp/              ← Cache temporaire Supabase CLI (ignoré par Git)
│
├── .vscode/
│   ├── extensions.json     ← Extensions VS Code recommandées
│   └── settings.json       ← Paramètres de l'éditeur pour ce projet
│
├── .git/                   ← Historique Git (GitHub Pages)
│
└── README.md               ← Ce fichier
```

---

## 📱 Responsive Design

L'application est **entièrement responsive** grâce aux breakpoints CSS :

| Breakpoint | Comportement |
|------------|-------------|
| `> 640px` | Grille 3+ colonnes pour les projets |
| `≤ 640px` | Grille 2 colonnes, padding réduit |
| `≤ 560px` | Champs prénom/nom en colonne unique |
| `≤ 380px` | Grille 1 colonne pour les projets |

---

## 📬 Contact

Projet développé par **Bridge-CG**

| Canal | Coordonnées |
|-------|-------------|
| ✉️ Email | [manuenrose.pro@gmail.com](mailto:manuenrose.pro@gmail.com) |
| 📞 Téléphone | [+242-05-056-81-56](tel:+242050568156) |

---

*© 2025 Bridge-CG — Sondage National pour un Congo plus Digital*
