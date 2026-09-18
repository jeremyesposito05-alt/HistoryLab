# HistoryLab V7.1 · Blue Shell

Version GitHub Pages / smartphone de HistoryLab, avec architecture générique multi-cours.

## Ce qui change dans V7

- écran d’entrée HistoryLab avec choix FR / EN ;
- accueil global centré sur la progression de l’élève ;
- premier module : Régimes autoritaires · Hitler & Mao ;
- filtre transversal : Les deux / Hitler / Mao ;
- navigation fixe : Accueil · Fiches · Épreuve · Flash! ;
- Fiches et révision ciblée réunies ;
- Flash! : révision du jour + défi erreurs ;
- hub Épreuve : Paper 1 et Paper 3 visibles « En développement », Paper 2 actif ;
- mode renommé « Entraînement express — Plan Paper 2 » ;
- contenu pédagogique et progression existants conservés.

Le moteur d’interface dispose désormais d’une structure FR / EN. Le pack pédagogique Hitler & Mao reste volontairement dans sa version française auditée tant que sa traduction anglaise n’a pas été produite et auditée séparément.

## Publication avec GitHub Pages

1. Déposer tous les fichiers de ce dossier à la racine du dépôt GitHub.
2. GitHub → Settings → Pages.
3. Source : Deploy from a branch.
4. Branch : `main`, dossier : `/ (root)`.
5. Enregistrer et ouvrir l’URL GitHub Pages.

Si une ancienne version de HistoryLab était déjà installée sur smartphone, le nouveau `sw.js` change la clé de cache afin de forcer la mise à jour vers V7.

## V7.1
- correction du sélecteur Les deux / Hitler / Mao : progression et unités recalculées selon la cible ;
- carte du cours cliquable et cible visible sous le pourcentage ;
- correction du logo Beau Soleil sur l'écran d'entrée ;
- nouvelle coque bleue Beau Soleil autour des surfaces blanches sur mobile et desktop ;
- cache PWA incrémenté pour forcer la mise à jour GitHub Pages.
