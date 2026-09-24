# HistoryLab · PROJECT_STATUS

## Source technique de vérité

Dépôt : `jeremyesposito05-alt/HistoryLab`  
Branche : `main`  
Version applicative actuelle : **V7.13**  
Dernière base auditée avant V7.13 : **V7.12**

Le dépôt GitHub, et en particulier la branche `main`, est la source technique de vérité. Le README historique n'est pas suffisant pour déterminer l'état réel de l'application, car il est resté sur une description V7.1.

## Architecture

HistoryLab est actuellement une PWA statique déployable directement avec GitHub Pages.

Fichiers principaux :

- `index.html` : application principale. Interface, données pédagogiques, moteur de quiz, progression, répétition espacée, Paper 2, fiches, glossaire, diagnostic et réglages.
- `manifest.webmanifest` : identité PWA. Nom et nom court : `HistoryLab`.
- `sw.js` : service worker et cache hors ligne. Cache actuel : `historylab-v7.13-strict-spaced-review`.
- `icon-192.png` et `icon-512.png` : icônes de l'application.
- `README.md` : documentation historique partielle, actuellement ancienne.
- `PROJECT_STATUS.md` : présent document de continuité technique et pédagogique.

L'application stocke la progression dans le navigateur avec `localStorage`.

Clé actuelle : `ib_u12_rev_v3`.

Anciennes clés migrées automatiquement : `ib_u12_rev_v2` et `ib_u12_rev_v1`.

Le shell V7 prévoit une architecture générique multi-cours via `HISTORYLAB_CONFIG`, mais un seul cours est actuellement actif dans le contenu livré : Régimes autoritaires, Hitler et Mao, Paper 2.

## Fonctionnalités actuellement disponibles

### Accueil

- tableau de bord de progression ;
- choix transversal Les deux, Hitler ou Mao ;
- taux de maîtrise ;
- nombre de questions dues ;
- série de travail ;
- priorité pédagogique ;
- accès aux fiches et à Flash!.

### Construire ton profil

Un nouveau profil ne bascule plus vers une simple séance Flash.

Le bouton lance un diagnostic initial dédié de 16 questions.

Répartition du diagnostic :

- 8 questions sur l'émergence ;
- 8 questions sur le maintien ;
- 8 questions sur Hitler ;
- 8 questions sur Mao ;
- 2 questions pour chacun des 8 axes principaux :
  - idées ;
  - économie ;
  - conflits ;
  - société ;
  - droit ;
  - force ;
  - propagande ;
  - soutien.

Le diagnostic utilise le même moteur de réponse et de mémorisation que les autres quiz, mais il possède un type de séance `profile`.

Le profil n'est marqué comme construit que lorsque les 16 questions sont terminées.

À la fin du diagnostic, l'élève voit :

- son score ;
- ses axes solides ;
- ses axes à retravailler ;
- une priorité initiale ;
- les conséquences de ses réponses sur Flash! et le calendrier de révision.

### Fiches

Les fiches structurent le cours par thèmes et corpus.

Chaque fiche peut lancer un quiz ciblé avec `startFicheQuiz()`.

Le bouton « Tester cette fiche » utilise le moteur de quiz commun.

### Glossaire

Chaque entrée peut lancer « Réviser ce corpus ».

Le bouton prépare les filtres correspondants, puis lance le moteur de quiz commun.

### Flash!

Flash! conserve deux fonctions distinctes :

1. Révision du jour.
2. Défi erreurs.

Depuis V7.13, « Révision du jour » respecte strictement la logique de répétition espacée.

Il ne remplit plus artificiellement une séance avec des questions jamais vues lorsqu'aucune question n'est due.

Quand aucune question n'est due :

- le bouton reste indisponible ;
- l'interface explique pourquoi ;
- si le profil n'est pas encore construit, Flash! propose directement de lancer le diagnostic ;
- si le profil est construit, Flash! indique que le travail programmé est à jour ;
- une fiche reste accessible pour travailler librement.

« Défi erreurs » reste indisponible tant qu'aucune erreur ouverte n'existe.

L'interface explique alors que le défi s'activera dès qu'une réponse sera fausse.

### Épreuve

Paper 2 est actif.

Modes disponibles :

- Étape par étape ;
- Comme le jour J ;
- Entraînement express, Plan Paper 2.

Paper 1 et Paper 3 restent affichés comme fonctions en développement.

## Moteur de quiz

Tous les chemins prioritaires de lancement convergent vers `launchQuizSet()`.

Chemins vérifiés :

- Glossaire → Réviser ce corpus ;
- Flash! → Révision du jour ;
- Flash! → Défi erreurs ;
- Fiche → Tester cette fiche ;
- Accueil → diagnostic ou priorité.

La sélection manuelle utilise `launchQuizFromCurrentSelection()`, qui finit également dans `launchQuizSet()`.

## Répétition espacée

Constantes actuelles :

```js
const BOXDAYS = [0, 1, 3, 7, 14, 30];
const MASTERY_BOX = 3;
```

La date est calculée en jours locaux via `localDay()`.

### Bonne réponse

Une bonne réponse :

- incrémente `seen` ;
- incrémente `ok` ;
- avance d'une boîte, avec plafond à la dernière boîte ;
- ferme toute erreur ouverte pour cette question ;
- programme la prochaine date selon la nouvelle boîte.

Exemples :

- première bonne réponse : boîte 1, révision dans 1 jour ;
- bonne réponse suivante : boîte 2, révision dans 3 jours ;
- bonne réponse suivante : boîte 3, révision dans 7 jours ;
- puis 14 jours ;
- puis 30 jours.

### Mauvaise réponse

Une mauvaise réponse :

- remet la boîte à 0 ;
- ajoute une erreur ouverte ;
- incrémente l'historique `wrongTotal` ;
- garde la question due le jour même.

### Maîtrise

Une question est considérée comme maîtrisée à partir de la boîte 3.

Une erreur ultérieure remet la question à la boîte 0 et lui fait perdre son statut de maîtrise jusqu'à une nouvelle progression.

### Défi erreurs

Le défi s'appuie sur les erreurs ouvertes.

Depuis V7.13, une bonne réponse ferme complètement l'erreur ouverte de la question. L'historique total des erreurs reste conservé séparément dans `wrongTotal`.

## Charte graphique et identité

Identité principale :

- Dark Navy Beau Soleil : `#002654` ;
- surfaces claires ;
- accents Beau Soleil ;
- interface responsive mobile et desktop ;
- identité HistoryLab distincte dans le shell de l'application.

Nom installé :

- manifeste : `HistoryLab` ;
- `short_name` : `HistoryLab` ;
- titre iOS : `HistoryLab`.

La V7.12 a installé deux fichiers d'icône actuellement présents sur `main`, mais cette proposition n'est pas validée et ne doit pas être considérée comme l'identité finale.

Spécification validée pour la prochaine icône :

- fond Dark Navy Beau Soleil ;
- montagnes alpines discrètes en arrière-plan, dans l'esprit de la première proposition ;
- grand H blanc ;
- aucune colonne ;
- aucun liseré doré autour du H ;
- aucun mot `HistoryLab` intégré dans l'image, puisque le nom apparaît sous l'icône sur iPhone.

Les fichiers `icon-192.png` et `icon-512.png` devront être remplacés uniquement après génération et validation de cette nouvelle icône.

## Bugs et incohérences corrigés

### V7.6 et V7.7

- correction des lancements de quiz depuis le glossaire et Flash! ;
- consolidation de la navigation vers le quiz.

### V7.8 à V7.12

- ajout et consolidation du diagnostic initial ;
- clarification progressive des états Flash! ;
- nom d'application HistoryLab ;
- identité iPhone et tentative d'icône, non validée comme version finale ;
- corrections successives du diagnostic et de sa navigation.

### V7.13

- retour à une « Révision du jour » strictement fondée sur les questions réellement dues ;
- suppression de l'injection automatique de questions nouvelles dans cette activité ;
- explication visuelle des états indisponibles de Flash! ;
- accès direct au diagnostic depuis Flash! pour un nouveau profil ;
- indication « à jour » quand aucune révision n'est due ;
- affichage de la prochaine échéance lorsqu'elle existe ;
- correction du traitement d'une erreur résolue par une bonne réponse ;
- priorité initiale plus explicite dans le bilan du diagnostic ;
- cache PWA incrémenté pour distribuer les corrections.

## Travaux restant à envisager

Les correctifs « profil initial + logique Flash de démarrage » sont désormais publiés sur `main` en V7.13.

Reste en priorité immédiate :

- générer puis valider la nouvelle icône iPhone selon la spécification ci-dessus ;
- seulement après validation, remplacer `icon-192.png` et `icon-512.png` et incrémenter de nouveau le cache PWA.

Restent ensuite des travaux de produit plus larges :

- mettre à jour `README.md`, encore basé sur V7.1 ;
- produire et auditer le pack pédagogique anglais, l'interface est bilingue mais le contenu Hitler et Mao reste principalement en français ;
- développer Paper 1 ;
- développer Paper 3 ;
- étendre réellement les données à plusieurs cours IB DP ;
- ajouter une suite automatisée de tests navigateur pour les parcours de quiz, la persistance locale et les transitions de version ;
- continuer les audits pédagogiques avant l'ajout de nouveaux corpus.

## Règle de continuité

Avant toute nouvelle modification :

1. vérifier le SHA actuel de `main` ;
2. lire le code réellement présent ;
3. vérifier les commits récents ;
4. ne pas reconstruire une fonction à partir d'une ancienne conversation ou d'une ancienne version ;
5. conserver le dépôt GitHub comme source technique de vérité.
