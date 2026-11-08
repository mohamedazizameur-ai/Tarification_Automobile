# Tarification automobile — projet d'actuariat

Tarification complète d'un portefeuille d'assurance automobile, de la fiabilisation des
données jusqu'à l'évaluation des modèles, à partir de deux bases : **Contrats** et
**Sinistres**.

> Auteurs : Mohamed Aziz Ameur 
> Enseignant : Lazher hedfi  — ESSAI, 2026-2027

## Démarche

Le projet suit les six étapes de l'énoncé :

| Étape | Contenu |
|---|---|
| A | Fiabilisation et préparation des données, création de l'exposition |
| B | Séparation des sinistres attritionnels et graves, choix du seuil |
| C | Approche Fréquence × Coût : `prime pure = fréquence × coût moyen` |
| D | Modélisation GLM de la fréquence et du coût |
| E | Séparation Train / Test : aléatoire (80 % / 20 %) et temporelle (N / N+1) |
| F | Comparaison des approches de pricing |

Deux méthodes sont menées en parallèle, une par notebook :

| Notebook | Méthode | Approche retenue |
|---|---|---|
| `01_tarification_avec_separation_graves.ipynb` | Les sinistres graves (plus de 10 000) sont séparés des sinistres attritionnels et tarifés à part | Attritionnels par GLM + graves tarifés par leur propre fréquence |
| `02_tarification_sans_separation.ipynb` | Tous les sinistres sont modélisés ensemble, sans seuil de gravité | Fréquence par GLM × coût moyen commun |

## Résultats principaux

- Après fiabilisation : 19 981 contrats, 11 532 années-police, 1 327 sinistres.
- Prime pure du portefeuille : **826** par année-police (fréquence 0,1151 × coût moyen 7 175).
- Les sinistres graves représentent 14,1 % du nombre et 89,0 % de la charge.
- Fréquence : loi de Poisson ; variables retenues : conducteur novice et zone géographique.
- Les deux méthodes donnent le même niveau de prime et des performances globales
  équivalentes en test. La séparation des graves rend le coût attritionnel stable et
  modélisable ; c'est la méthode recommandée.

La synthèse complète est dans `docs/Note_technique_Tarification_auto.docx`.

## Contenu du dépôt

```
.
├── 01_tarification_avec_separation_graves.ipynb   notebook, méthode avec séparation
├── 02_tarification_sans_separation.ipynb          notebook, méthode sans séparation
├── pricing_auto.py                                script Python de la méthode avec séparation
├── data/
│   ├── groupe1_base1_contrats.xlsx
│   └── groupe1_base2_sinistres.xlsx
├── docs/
│   ├── Note_technique_Tarification_auto.docx      note technique de 2 pages
│   └── syntheses_par_etape/                       synthèses détaillées, étape par étape
├── requirements.txt
└── README.md
```

## Exécution

```bash
pip install -r requirements.txt
jupyter notebook
```

Les notebooks lisent les données dans `data/` : ils doivent être lancés **depuis la
racine du dépôt**. Ils écrivent leurs tableaux et figures dans `outputs/` (notebook 01)
et `outputs_classique/` (notebook 02), dossiers non versionnés.

Le script équivalent au notebook 01 se lance avec :

```bash
python pricing_auto.py
```

## Choix techniques

- GLM estimés avec `statsmodels` (sous-modules `statsmodels.formula.api` et
  `statsmodels.genmod`), loi de Pareto généralisée avec `scipy`.
- Découpage Train / Test et métriques de validation codés explicitement, sans
  `scikit-learn`.
- Graine aléatoire fixée (2026) : les résultats sont reproductibles.
