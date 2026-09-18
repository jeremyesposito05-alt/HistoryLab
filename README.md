# HistoryLab · Hitler & Mao · Paper 2

Version GitHub Pages / smartphone de HistoryLab.

## Publication avec GitHub Pages

1. Créer un dépôt GitHub (par exemple `historylab-hitler-mao`).
2. Déposer **tous les fichiers de ce dossier à la racine** du dépôt.
3. Dans GitHub : **Settings → Pages**.
4. Dans **Build and deployment**, choisir **Deploy from a branch**.
5. Sélectionner la branche `main` et le dossier `/ (root)`, puis **Save**.
6. GitHub fournit ensuite l'adresse HTTPS publique de l'application.

## Smartphone

L'application est responsive sur iPhone et Android. La navigation principale passe en barre tactile en bas de l'écran.

### Installer comme une app

- **iPhone / iPad (Safari)** : Partager → Ajouter à l'écran d'accueil.
- **Android (Chrome)** : menu ⋮ → Ajouter à l'écran d'accueil / Installer l'application.

Après une première ouverture réussie, le service worker met les fichiers principaux en cache pour permettre un usage hors ligne.

## Fichier source

`index.html` contient l'application HistoryLab complète. Les autres fichiers ne changent pas son contenu pédagogique : ils servent uniquement à l'installation mobile/PWA et à GitHub Pages.
