# HistoryLab · PROJECT_STATUS

## Source technique de vérité

Dépôt : `jeremyesposito05-alt/HistoryLab`  
Branche : `main`  
Version applicative actuelle : **V7.16**  
Dernière base auditée : **V7.14** (audit complet du contenu et du code le 28 septembre 2026)

Le dépôt GitHub, et en particulier la branche `main`, est la source technique de vérité. Le README historique n'est pas suffisant pour déterminer l'état réel de l'application, car il est resté sur une description V7.1.

## Architecture

HistoryLab est actuellement une PWA statique déployable directement avec GitHub Pages.

Fichiers principaux :

- `index.html` : application principale. Interface, données pédagogiques, moteur de quiz, progression, répétition espacée, Paper 2, fiches, glossaire, diagnostic et réglages.
- `manifest.webmanifest` : identité PWA. Nom et nom court : `HistoryLab`.
- `sw.js` : service worker et cache hors ligne. Cache actuel : `historylab-v7.16-coach`. Les 14 illustrations du coach (`coach/coach_*.webp`) sont mises en cache dès l’installation.
- `icon-192.png` et `icon-512.png` : icônes de l'application.
- `coach/` : les 14 poses du coach, WebP transparents 750 × 900 (49 à 79 Ko).
- Sauvegardes : chaque version publiée précédente est étiquetée `sauvegarde-vX.Y` dans le dépôt.
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

- Dark Navy Beau Soleil : `#1A2047` (valeur des SVG officiels ; le `#002654` imprimé dans le PDF de la charte est faux, comme trois autres codes : Mid Blue `#14387F`, Gold `#F3BE31`, Warm Grey `#9C9895`) ;
- surfaces claires ;
- accents Beau Soleil ;
- interface responsive mobile et desktop ;
- identité HistoryLab distincte dans le shell de l'application.

Nom installé :

- manifeste : `HistoryLab` ;
- `short_name` : `HistoryLab` ;
- titre iOS : `HistoryLab`.

La V7.14 installe l'icône validée de HistoryLab sur `main`.

Identité de l'icône actuelle :

- fond Dark Navy Beau Soleil ;
- montagnes alpines en arrière-plan ;
- grand H blanc ;
- aucune colonne ;
- aucun liseré autour du H ;
- `History` en blanc et `Lab` en doré sous le H.

Les fichiers `icon-192.png` et `icon-512.png` correspondent désormais à cette identité validée.

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

### V7.14

- installation de l'icône HistoryLab validée ;
- génération des versions 192 × 192 et 512 × 512 ;
- identité visuelle : montagnes alpines, grand H blanc, `History` blanc et `Lab` doré ;
- incrément du cache PWA et de la référence Apple Touch Icon.

### V7.16

Refonte du coach, d'après l'audit (retour centré sur la tâche, apparitions limitées aux moments clés).

- **14 poses**, dont 6 nouvelles (fiches à la loupe, chrono, célébration, série, café, plan au tableau). Les 8 anciennes ont été retouchées à partir des originaux : bulles et textes incrustés retirés, logo de marque retiré de l'ordinateur. Images séparées du HTML : `index.html` passe de 1,85 Mo à 0,72 Mo.
- **Bulles écrites par l'application**, en français ou en anglais selon la langue choisie : 26 moments, 2 à 4 formulations chacun, sans répétition immédiate. Messages centrés sur la tâche et la stratégie ; humour sur le travail et sur le coach, jamais sur les régimes, leurs acteurs ou leurs victimes.
- **Apparitions dans le quiz limitées aux moments clés** : question maîtrisée (avec confettis), séries de 3, 5 et 10 bonnes réponses, première erreur de la séance, question qui piège une deuxième fois (renvoi au document du cours). Les autres réponses n'affichent que la correction.
- **Tableau de bord** : pose et bulle selon la situation (diagnostic à faire, retour après 3 jours ou plus avec la pose café, série de jours, révisions dues, planning à jour).
- **Épreuve** : pose et conseil différents selon le mode (étape par étape, jour J avec le chrono, plan express au tableau) ; bilan de copie selon l'auto-évaluation.
- Légère respiration du coach au repos, bulle animée, confettis ; tout est désactivé si l'appareil demande de réduire les animations. Texte alternatif de chaque pose en français et en anglais.

### V7.15

Audit complet du contenu contre le cours (unités 1.1 et 1.2) et du code.

Données pédagogiques :

- les 317 questions historiques repartent de la version auditée V6.5, dont les corrections n'avaient jamais été reportées dans la branche V7 (elle était partie des données V6.4) ;
- 257 questions retravaillées pour supprimer les indices qui trahissaient la bonne réponse : la bonne réponse était la plus longue dans 67,7 % des cas (hasard : 25 %), elle ne l'est plus que dans 23,7 % ; rangs de longueur 24 / 26 / 26 / 24 % ; plus aucun jeu d'options où seuls les distracteurs portent un mot absolu, ni où seule la bonne réponse est entre guillemets ;
- les 8 questions comparatives `CMP-*` rééquilibrées (la bonne réponse était la plus longue dans les 8) ;
- erreurs de fond corrigées : distracteurs qui étaient eux aussi justes (A2-05, A3-04, A4-01, C2-14, C3-34…), fausses citations (B1-06, C2-08), contresens (B2-13), commentaire tronqué (B1-08), question fondée sur une prémisse fausse (C2-04, Oranienburg), commentaire niant qu'une citation figure au dossier (C3-02), « trois fois » corrigé en « deux fois et demie » (C5-08), types de question erronés (C3-26, C4-29) ;
- les 71 citations nouvelles ou modifiées ont été vérifiées mot pour mot dans le texte du cours ;
- corrigé du sujet « Le recours à la force » : partie (a) reformulée pour rester sur la force ; glossaire : deux définitions contradictoires de « fanshen » harmonisées.

Code :

- **diagnostic initial jamais validé** : le total de fin de série comptait une question de trop (3 questions affichaient « 1 / 4 »), si bien que les 16 questions du diagnostic donnaient 17 et que `profileBuilt` ne passait jamais à vrai — Flash! restait bloqué « après le diagnostic ». Corrigé ;
- raccourcis clavier du quiz (1 à 4, Entrée, Espace) neutralisés hors de la révision et dans les zones de saisie : ils répondaient au quiz en arrière-plan et bloquaient la barre d'espace pendant la rédaction du Paper 2 ;
- témoin de sauvegarde honnête : un échec d'écriture (stockage plein, navigation privée) affiche un avertissement au lieu de « Sauvegardé » ; sauvegarde du brouillon différée de 0,8 s au lieu de chaque frappe ;
- l'écran de progression (statistiques par axe, rapport Teams, export, import, remise à zéro) n'était accessible par aucun bouton depuis V7 : il s'ouvre désormais depuis Réglages → « Ma progression et mes données » ;
- nouvel onglet de fiches « À savoir par cœur » : les 20 formules et jugements de `DATA.fiche`, jamais affichés jusque-là, avec un mode d'auto-test ;
- « Tout effacer » interrompt aussi l'épreuve et le quiz en cours, et conserve la langue ;
- navigation au clavier des onglets (flèches, `aria-controls`, `tabpanel`) ; texte des bulles du coach en alternative textuelle ;
- charte Beau Soleil : toutes les couleurs ramenées sur la palette officielle (261 codes), Calibri pour le texte, SangBleu OG Sans sans faux gras pour les titres, bouton gold remplacé par un bouton bleu à filet gold, manifeste en `#1A2047` ;
- code mort retiré (`charAsset`, `soloReaction`, `bars`) ; `esc()` échappe aussi l'apostrophe droite.

## Travaux restant à envisager

Les correctifs « profil initial + logique Flash de démarrage » sont publiés sur `main` depuis V7.13. L'icône iPhone validée est installée en V7.14.

Priorité produit suivante : faire évoluer « Construire ton profil » vers un onboarding d'apprentissage qui combine les habitudes et l'organisation déclarées par l'élève avec le diagnostic objectif de connaissances.

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
