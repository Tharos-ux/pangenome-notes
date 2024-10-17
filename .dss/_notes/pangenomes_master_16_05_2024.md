---
title: Notes présentations master pangénomes PEPR
date: 16.05.2024
---
*Objectif annoncé : voir qui travaille sur quoi et éviter de se marcher sur les pieds*
## Adrien Lapoutge, MIAT, Identification taxonomique par métabarcode par graphe de variation  
Métabarcoding : utilisé pour inventaire sp dans un envnt.
Séquence = prélèvement d'ADN + méthode identification sp.
OTU, placement arbre phylo, kraken, et **méthode par graphe de variations**
- Euka
- Haplocart
- StrainFlair
Ces outils utilisent :
- vg
- graphaligner
- seqwish
- pggb
- bdsg
OBJECTIF : **Pipeline d'identification taxo sur graphe de variation**
Reads + Graphe de variation > Mapping > Filtres post-mapping > Assignation taxo
Objectif du stage : **Pipeline snakemake de benchmark**
Graphes construits avec vg
Mapping avec Graphaligner
Choix de vg pour la simplicité avec le reste du pipeline + graphes construits à partir d'alignements multiples
## Marouane Boumlik, MIAT, Mapping sur graphe de pangenome  
Mapping sur graphe de pg
Index du graphe > Charger les lectures > Extraction minimizers > hits > Seed clustering > alignmements
Utilisation de strobemers plutôt que minimizers pour gérer les indels
- midstrobe
- randstrobe
corrections de bugs sur lib strobemers
injection dans graphaligner
## Julien Guidihounme, MIAT, Détection de différentiel de motifs fonctionnels dans les GVs (e.g différentiel de score de matrices PSSM)  
Formule de fréquence relative, et normalisation de la matrice avec un log
Hypothèse de départ
On peut calculer un score par séquence
Travail sur sous-graphes :
- Noeud de bifurcation : noeud à partir duquel les séquences vont se ressembler
- Noeud intérieur : une fourche avant et après
- Fourche simple
Algo qui calcule des scores et qui détecte les noeuds de bifurcation
Se poser la question de l'évolution du nombre de bifurcations en fonction du nombre de génomes

## Fatima-Zahra Abani, DIADE, Skimming sur graphe et mapping  
Inférence de structure
Mieux comprendre la diversité des pop
Mapper sur pangénome
Utiliser résultats mapping pour faire assemblage sans avoir à tout assembler
Base minigraph-cactus, 8 génomes de riz
Quel graphe (full,clip,fliter) utiliser pour le mapping ?
Mapping avec vg giraffe
Mapping sur full et clip environ similaire, mais clip 10x plus rapide et 10x moins mémoire
Filter fait perdre beaucoup par contre
Comparaison avec résultats sur linaire
filtre sur sam/bam, gaftools n'a pas marché, vg tools n'a pas les mêmes métriques que samtools flagstats
Code pour parser GAM > JSON
- extraction stats pour comparaison facile alignement
- extraire infos pour enrichir les graphes
- à terme, mapping
## Thomas Biscop et Hugo Bellavoir, ISE-M, Recherche de variations structurales de type inversion dans un graphe de Pangénome appliquée au champignon pathogène P. destructans  
Champignon responsable d'une maladie chez les chauves-souris
évolution du génome ? Pathogénéicité ?
4 étapes portées par 4 personnes
Construction du graphe
- mg
- mgc
- pcactus
- pggb
séquences simulées avec variations structurales
génomes réels
détection d'une variation par recherche de noeuds d'ancrage par nombre de génomes traversant par un certain sens chaque noeud, en cherchant à maximiser pour trouver les ancrages
Observations : beaucoup plus d'inversions détectées par pggb (15 vs 1 pour les autres méthodes sur le même jeu de données. *question de conditions posées ou de graphe directement ?*)
## Bayram Boukhari, PHIM, Exploration des ressources génomiques de Xanthomonas oryzae par des approches de pan-génome graphe  
Review de l'état de l'art des pangénomes car pas d'avancées pour le moment
Clusteriser les gènes orthologues et regarder les présences/absences dans les génomes
Limites avec transfert horizontal : difficile de faire émerger des patterns
Matrices à partir du graphe, avec pggb / étude de l'overlap sur les noeuds pour les haplotypes
Review / Obtention de graphes / Benchmarking / Nouvelles features dans PanExplorer
## Samia Benrabia, CNRGV, construction et l’analyse d’un pangénome des portes greffes de la vigne pour l'adaptation à la sécheresse
Construction graphe PGGB et MGC
Comparaison ! (je vais être contacté)
Visualisation de régions spécifiques, utilisation minimap2 pour l'alignement
Pour le moment juste un graphe pggb construit
