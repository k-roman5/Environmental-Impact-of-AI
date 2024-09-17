Stage Karina
===

# Lectures

- [The growing energy footprint of artificial intelligence](https://asociace.ai/wp-content/uploads/2023/10/ai-spotreba.pdf)
    - revue par un économiste de la littérature sur la conso électrique de l'IA et les besoins en matériel

- [Sustainable AI: Environmental Implications, vChallenges and Opportunities](https://arxiv.org/pdf/2111.00364)
    - vue générale de l'empreinte carbone de l'IA - cas de facebook

- [Green Algorithms](https://www.green-algorithms.org/GAapp-overview/)
    - Outil d'estimation de l'empreinte carbone à l'entrainement de l'IA

    - Restrictions de l'algorithme :
        - restricted to GHGs emitted to power computers during a particular task
        - Can not perform a life cycle assessment and therefore, do not consider the full environmental and social impact of manufacturing, maintaining, and disposing of the hardware used, or the maintenance of the power plants
        - the TDP (Thermal Design Power) may substantially underestimate power draw in some situations
        - while the power consumption from storage is usually minimal at the scale of one computation, if central storage is constantly queried by the algorithm (for example, to avoid overloading memory), this can be an important factor in power draw
            - at the scale of the data center, storage represents a significant part of electricity usage
            - research projects relying on large databases should separately acknowledge the long-term carbon footprint of storage
        - the energy mix of a country varies by the hour
        - PUE (Power Usage Effectiveness : Indicateur d'efficacité énergétique) has some limitations as a measure of data centers energy usage
            - été vs hiver par exemple 
            - security or on-site power generation


- [Toward Sustainable HPC: Carbon Footprint Estimation and Environmental Implications of HPC Systems](https://ieeexplore.ieee.org/abstract/document/10485180)
    - Très technique sur la partie estimation, regarder plutot les simulations et résultats.

- [Sustainable energy transitions require enhanced resource governance](https://www.sciencedirect.com/science/article/pii/S0959652621019168?via%3Dihub)
    - Cas des voitures électriques, source d'inspiration sur la modélisation

- [Optimizing the Impact of Upgrading Computer Equipment](https://conf.researchr.org/getImage/ict4s-2024/orig/ICT4S2024_paper_29.pdf)

![](https://codimd.math.cnrs.fr/uploads/b75d44293093469c31f6b1638.png)

## Recherches supplementaire :

- [Carte mondiale de la production de l'éléctricité ](https://app.electricitymaps.com/zone/FR)

- GPU (Graphics Processing Unit - Processeur graphique) : un circuit électronique capable d'effectuer des calculs mathématiques à grande vitesse - [source](https://aws.amazon.com/fr/what-is/gpu/#:~:text=Un%20processeur%20graphique%20(GPU)%20est,un%20grand%20jeu%20de%20données)

- CPU : (Central Processing Unit - Processeur) constitue l'unité centrale de traitement d'un serveur - [source](https://aws.amazon.com/fr/what-is/cpu/#:~:text=Un%20processeur%20(CPU)%20est%20un,y%20effectuent%20des%20opérations%20mathématiques)

---

- Périmètre : 
    - Aujourd'hui : principalement 1 entrainement
    - Mais : plusieurs / inférence / ré-entrainement / données
- Amélioration de l'efficacité du matériel
    - quel renouvellement
    - quelle durée de vie
- Capacité à produire les équipements

---

# Algorithmes

Modèle dynamique 
- estimation tous les mois ou toutes les années

Deux variables 
- quantité de calcul (flop)
- fréquence de renouvellement → efficacité du matériel

## Modèles suivants

En entrée
- Tableau de l'efficatité du matériel en fonction du temps (watt, flops) en fonction du temps
- Quantité de calcul
- Distribution du matériel dans le centre de données

En sortie
- Distribution de GPU par année et donc une consommation par année

Autres évolutions
- frequence de renouvellement varibale
- Mettre des distributions pour chaque variable
- rajouter les cpus et la mémoire
- cout financier
- données de fabrication

Cas d'usage : IDRIS/Jean-Zay
- [Détails de l'infra](https://www.genci.fr/institut-du-developpement-et-des-ressources-en-informatique-scientifique-idris)

___

## Données en entrée de l'algorithme

**En entrée**
- Tableau de l'efficatité du matériel en fonction du temps (flops, watt) en fonction du temps
    - chercher sur le site Nvidia les modèles : V100, A100, H100
    - prendre la valeur du Tensor Performance pour chaque modèle 
        - [V100](https://images.nvidia.com/content/volta-architecture/pdf/volta-architecture-whitepaper.pdf) : 125 TFLOPS, 300 W, [26 300 € TTC](https://www.compufirst.com/nvidia-tesla-v100-processeur-de-calcul-tesla-v100-32-go/fiche_prod.do?prodId=2168593)
        - [A100](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/a100/pdf/nvidia-a100-datasheet-nvidia-us-2188504-web.pdf) : 500 TFLOPS, 400 W, [31 900 € TTC](https://www.compufirst.com/nvidia-tesla-a100-passive-processeur-de-calcul-nvidia-tesla-a100-80-go/fiche_prod.do?prodId=2754258)
        - [H100](https://resources.nvidia.com/en-us-tensor-core/nvidia-tensor-core-gpu-datasheet?_gl=1*f5wvy7*_gcl_au*MTQxMTM0MTU2OS4xNzE3MTU5MzMy) : 2000 TFLOPS, 700 W,  [29 500 € TTC](https://smicro.eu/nvidia-h100-80gb-pcie-5-0-x16-passive-cooling-900-21010-0000-000-1)

⟼ Modifications : garder la mesure FP32 si possible avec la mesure "Tension Core" (precision mixte = calcul en demi-précision)

→ Pour la colonne contenant la performance de chaque GPU, nous nous intéressons à la valeur de *Peak Tensor* en TFLOPS, qui mesure la performance du GPU pour les applications d'intelligence artificielle (IA)

→ Max Thermal Design Power (Puissance de Conception Thermique Maximale) : la quantité maximale de chaleur générée par le composant pendant son fonctionnement normal, mesurée en watts (W).

![](https://codimd.math.cnrs.fr/uploads/ccd4aabe7c44f0736e42fd029.png)


- Quantité de calcul (en TFLOPS)
- Le nombre du matériel dans le centre de données
- Variable fréquence de renouvellement (en année ou mois)

___

# Réunions hébdo

## Jeudi 13 juin

** Objectif : assurer la gestion d'un parc de GPU avec un seul type de GPU**

- Initialisation flexible : possibilité de commencer en 2019 ou 2020

- Types de GPU à installer: 
    - V100 : 2019-2021
    - A100 : 2021-2024
    - H100 : 2024 et après

- La puissance de calcul est davantage une sortie qu'une entrée !!

**Sortie :** consommation
- Pour un FLOP constant, il faut connaître :
    - Le nombre de GPU de chaque type ou âge 
    - La consommation 
    - Le budget nécessaire pour les renouvellements/achats
   
**Contrainte principale :**
- Maintenir un nombre constant de FLOP
- Faire varier ce nombre par période par la suite

**Gestion du nombre de FLOP :**
- Si le nombre de FLOP augmente, il faut :
    - Changer les GPU (renouvellement)
    - Anticiper les changements (simulation avec des durées de vie différentes)
    - Augmenter la quantité de GPU

**Indicateur principal :** Tension des coeurs FP32

**Autres indicateurs de performance :** 
 - Plusieurs indicateurs de performance doivent être pris en compte
- Choisir le type d'utilisation pour les GPU en fonction de ces indicateurs

[Top 500 liste des Data Centers au niveau mondial](https://top500.org/statistics/sublist/)

___

## Jeudi 20 juin

**Capacité des FLOPS vs demande des FLOPS**
  - Taux d'usage (%)
  - Consommation pour faire fonctionner le GPU (carte mère, etc.)

**Consommation statistique :** les GPU sont laissés disponibles en permanence (ce qui entraîne une consommation assez importante à prendre en compte dans le calcul de la performance)

**Graphiques :**
- **Nombre de GPU produits (ajoutés)** : Barplot (flux) - séparer par couleurs les GPU ajoutés pour répondre à la demande vs ceux ajoutés en raison de la durée de vie écoulée (nouveaux)
  
- **Consommation énergétique** : lineplot - coloré en fonction du type de GPU
  
- **FLOPS** : représenter la capacité stockée dans le parc et les GPU disponibles, classés par type
  
- **Somme cumulative des GPU achetés depuis le début (création du centre de données)** : lineplot - représente la puissance cumulée
  
- **FLOPS (multiplication par 2,5 tous les 2 ans)** : lineplot en escalier

**Exemple :**
- 1000 GPU en janvier 2019
- Capacité de calcul doublée tous les 2,5 ans

**Traduction en consommation électrique :**
- Taux d'utilisation très fluctuant, pas seulement au maximum
- Autres équipements : 8 GPU par machine + carte mère
  - 200-300 W en statique pour 8 GPU et l'équipement.
  - 25-30 W par GPU en statique
  
- **Consommation dynamique** : GPU - 80% en puissance maximale et 20% en statique

**Consommation par GPU :**

\begin{align*}
P_{GPU} &= 400W\\
P_{+/GPU} &= 66W \text{ ( } P_{+} \text{ = carte mere, cpu, etc)}\\
P_{s/GPU} &= 25W
\end{align*}

**Calcul de la consommation totale :**

\begin{align*}
Energie &= Temps * (P_{GPU} + P_{+/GPU} + P_{s})* \tau ) +(1- \tau) * P_{s}\\
&= Temps * [(P_{GPU} + P_{+}) * \tau + P_{s}]
\end{align*}

avec $\tau = 80%$

____

## Vendredi 28 juin 

- La quantité de calcul augmentera régulièrement, sans changements brusques
- Maintenir un taux de capacité constant à 0.80
- Supprimer les colonnes "...used" et "...not_used" dans le dataframe final

Voici le texte corrigé et amélioré :

Pour commencer, utiliser un datacenter fixe avec des GPU du même type installés simultanément !

- Variabilité du nombre maximal de FLOPS que l'on souhaite atteindre
- Estimation de l'augmentation du nombre de GPU lors de l'ajout de nouveaux GPU

Le nombre de GPU est bloqué par 8, donc un nombre multiple de 8 !

On questionne le gain d'efficacité : (cumulatif : ⭐︎)
- En entrée :
    - MW ⭐︎
    - € ⭐︎
    - Nombre de GPU ⭐︎

- Sortie :
    - GWK ⭐︎
    - CO2 - GES ⭐︎
    - m2

![](https://codimd.math.cnrs.fr/uploads/b75d44293093469c31f6b1637.png)

\
Estimation de la capacité utilisée maximale : $f_i = F(f_{i-1}, c_i, \tau_{i-1}, \tau)$

avec $\tau$ à 80% et $\tau_{i-1}$ initié avec $\tau$ et $c_i$ la capacité (TFLOP) en fonction du temps

Ainsi : $\tau_i = \frac{f_i}{c_i}$

On cherche à estimer : $\bar{f} =\frac{\tau}{c_i}$ et on ne peut pas augmenter $f_i$ de plus de 20% (par exemple).

**À prévoir pour plus tard :**
- Taille maximale disponible dans le centre de données
- Délai de renouvellement ?

___

## Jeudi 4 juillet
- Modifier le nom de la variable "Flops_total" en "Flops_max"
- Revoir la fonction "model" : résoudre les problèmes liés à l'emplacement des fonctions qui calculent la consommation et la quantité de FLOPS des GPU
- Permettre une transition sur plusieurs mois pour passer de la valeur initiale de FLOPS à la nouvelle valeur de FLOPS (colonne "Flops_max")
    - Changer le $\tau$ cible (partir de 80% et viser à nouveau 80%) - la valeur de $\tau$ varie
    - Mettre à jour le Data Center à la fin de l'exécution de la fonction $f_i$

___

## Lundi 8 juillet
**Modifications à effectuer sur le DataFrame:**

- On ne change pas tout le parc lorsqu'on doit ajouter des GPU. On garde ce qu'on avait avant et on le renouvelle (comme avant) !

- **Colonnes :**
    - **Flop_required** : pour le gestionnaire avec 100% d'utilisation, noté $r_i$ ⇾ donne la quantité de CPU, GPU, etc
    - **Capacité maximale** : notée $c_i$, avec $c_i \geq r_i$
    - **Flop_used** : notée $f_i = \tau_i \cdot c_i$

- **Approximation de $\tau$ :**

Quelques fonctions à tester :
- $f_i = (1 - \alpha) \cdot f_{i-1} + \alpha \cdot \bar{f} \quad \text{avec} \quad \alpha \in (0, 1]$

- $\begin{cases}
f_i = \min(\bar{f}, f_{i-1} + \Delta) \\
\Delta = f_{i-1} \cdot \beta \quad \text{avec} \quad \beta \in ]0, \infty[
\end{cases}$

- $\Delta = \bar{f} \cdot \beta$

Adapter la pente en regardant la valeur de $\frac{f_{i-1}}{\bar{f}}$ !
 
- **Autres éléments à inclure :**
    - une variable pour faire varier le temps
    - des graphiques pour tester l'influence des différents paramètres

___

## Jeudi 17 Juillet :

- Mettre la conso en kWh !

**Données supplanteraires :**

- Émissions de CO2 liée à la consommation - de 2012 à 2035 en kgCO2e/kWh
```
[0.061      0.061      0.061      0.061      0.057      0.056
 0.057      0.061      0.06       0.057      0.052      0.05061538
 0.04923077 0.04784615 0.04646154 0.04507692 0.04369231 0.04230769
 0.04092308 0.03953846 0.03815385 0.03676923 0.03538462 0.034     ]
 ```
- Émissions de CO2 liées à la production : [100, 150, 150] kg par unité (par GPU)

    - Il faut également prendre en compte les CPU et les autres composants du serveur : 1400 kg à diviser par 8 pour chaque GPU (pour l'instant c'est statique, mais il faudra ajouter un slider - widget).

- Consommation électrique : entre 0 et 1 (par défaut 0,2) → à convertir en CO2

- Impact de fabrication : on remplace aussi les serveurs, pas seulement les GPU

**Graphiques :**
- Pour comprendre : 
    - CO2 production : la courbe descend lors du remplacement
    - CO2 produits remplacés : un pic important lors du remplacement

- Calculs :
    - En cumulatif
    - Comparer plusieurs scénarios (méthode par "flux")
    - Vision par "stock" :
        - Par mois (on divise par la durée de vie du GPU) - on peut le faire en cumulatif aussi (mesure : tCO2/mois).

- Premier graphique par mois : ne montre que le stock (production et consommation sur le graphique)
- Deuxième graphique cumulé : version cumulée (flux et stock - avec un bouton pour choisir entre les deux) : consommation et production (mesure : tCO2e)
___

## Jeudi 18 Juillet
**Modifications à effectuer sur le code actuel**
- Supprimer le graphique créé avec la bibliothèque Matplotlib
- Ajouter une colonne dans le DataFrame final pour l'émission de CO2 liée à la consommation, exprimée en kgCO2e/kWh, en fonction de chaque année
- Créer un dictionnaire pour centraliser toutes les valeurs globales, cela permettra de modifier les valeurs plus facilement sans devoir toucher directement au code
- Modifier les valeurs dans la colonne `Performance_FP32` du DataFrame `df_data_GPU`, qui stocke les données sur l’efficacité du matériel, nous allons nous concentrer sur les valeurs de "Peak Tensor" :
     - **V100** : 125 TFLOPS
     - **A100** : 500 TFLOPS
     - **H100** : 2000 TFLOPS

**Amélioration du Graphique** :
- Permettre la modification interactive des valeurs suivantes :
     - durée de vie des GPU
     - facteur multiplicateur de la demande des FLOPS et l'intervalle entre les demandes
- Comparer différents scénarios à l'aide des graphiques améliorés.

**Modifications à prévoir**
- Finir l'ajout de toutes les fonctions pour le calcul de l'impact environnemental - émissions de CO2
- Réaliser les graphiques en utilisant les méthodes "Flux" et "Stock" pour les données mensuelles et cumulatives
- Améliorer le code pour le rendre plus flexible afin d'ajouter d'autres indicateurs (consommation d'eau, etc.) à l'avenir

À la fin du stage : rédiger un petit compte rendu et ajouter toute la documentation nécessaire sur le notebook pour chaque variable, fonction, DataFrame, etc., utilisés dans le projet

## Vendredi 26 Juillet
Modifications à apporter au code :
- Mettre le 0 sur l'axe des abscisses de tous les graphiques
- Changer la taille des graphiques en augmentant leur hauteur, en particulier pour les graphiques des émissions de CO2 et de la consommation énergétique
- Ajouter les émissions de CO2 pour la production des serveurs au calcul des émissions pour la production des GPU (un serveur pour 8 GPU)
- Limiter la partie flux aux émissions de CO2 dues à la production des GPU, sans inclure celles dues à l'utilisation
- Revoir le code des deux algorithmes pour réduire la répétition :
   - $f_i$ est calculé à deux endroits
   - conserver uniquement le calcul de `f_1` ou du taux d'usage, car les deux sont corrélés et l'un peut être dérivé facilement de l'autre
   - garder les deux valeurs en sortie du premier algorithme et n'en utiliser qu'une seule en entrée


## Jeudi 1er Août

- Ajouter un coût des GPU en sortie (en cumulatif aussi)
- Améliorer l'interface des widgets.$
- Ajouter une fonction qui calcule les m² nécessaires pour le data center en sortie :
  - on peut avoir 8 serveurs par étagère
  - 1500 kW par colonne
  - inclure les distances nécessaires autour de chaque rangée de GPU
- Changer les couleurs sur les graphiques cumulatifs

**Ajouter de nouveaux indicateurs (pour traduire la consommation électrique avec d'autres facteurs) :**
- [Abiotic Depletion](https://dataviz.boavizta.org) :
  - production du serveur : 0,16 kg/Seq
  - GPU :
    - V100 : 0,015 kg/Seq
    - A100 : 0,22 kg/Seq
    - H100 : 0,285 kg/Seq
  - usage : 4,86 * 10^-8 kg/Seq

- Eau :
  - production du serveur : [3500 m³](https://negaoctet.org)
  - GPU (hypothèse):
    - V100 : 50 m³
    - A100 : 50 * 1,5 m³ soit 75 m³
    - H100 : 50 * 2 m³ soit 100 m³
  - usage : 3,3 * 10^-3 m³/kWh (PEF water usage)

- Waste from Electrical and Electronic Equipment (WEEE)  - Déchets d'Équipements Électriques et Électroniques (Negaocte non public):
  - production du serveur : 10,7 kg
  - GPU :
    - V100 : 1,6 kg
    - A100 : 1,6 kg
    - H100 : 1,6 kg
  - usage : 0 kg


## Jeudi 22 Août

- Énergie primaire : ajouter cet indicateur (à comparer avec (✫) ), en mesurant l'utilisation des ressources fossiles en MJ
- Considérer également un indicateur similaire pour l'utilisation de l'eau
- Inclure un lien vers les données sur l'épuisement des ressources abiotiques
- Comparer l'épuisement des ressources abiotiques avec les limites planétaires pour mieux contextualiser les résultats (ex. : estimation du nombre de personnes concernées)
    - Article (✫) : [Sala, Crenna, Secchi, Sanyé-Mengual, "Environmental sustainability of European production and consumption assessed against planetary boundaries," Journal of Environmental Management, 2020](https://pdf.sciencedirectassets.com/272592/1-s2.0-S0301479720X00132/1-s2.0-S0301479720306186/mainext.pdf?X-Amz-Security-Token=IQoJb3JpZ2luX2VjEOX%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLWVhc3QtMSJGMEQCIHpOyRTNbH20jb3wAlTL4Rrlqy2jTOrM%2BZ3hhwVAoy9kAiBgaVgN4OqnQA9drVa3ehIZrj%2FI7az%2BdY0cQPgy3Lh2fiq7BQju%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F8BEAUaDDA1OTAwMzU0Njg2NSIMUCOaTXrRy11QM4DGKo8FPDIJPhPmwFkXJGpNaGedrDd4qlVMg%2BZxGqdQArUfAhQ5tGY76MeEnur4vEjnUuqhWVbwWX8salDkJ0JGKp5M6%2B6vyt2%2FPj9mwoBKPmsGxWeXwEL7g2c08G76Os75nMUDUmxX6EsLQQmhmZmpzlSZkfsKulYCt%2Fpgar1CuA7IzyR2axkvh19j1MgM1dEazAlP6Kp3q1jpp6dOYEtpt4jTnAbVG5THp7r1g%2B3F5a14ReHhgMblr9aspX%2BseKEuTu5wXZ%2F%2BukuILuNjCGh3hLO8Fl13ut7fdY9tyld5XQzpBcqda%2FPpCW8loqzrh2MyMkCpROFt1s0GMRiStfTU07q%2BoEmQl6vxjJPHnPAlovJ60Pr%2BhN0G%2FDAfg2krp9Iu93cT9icEF93AoTYVFZPvs512UU0ehP%2BrcLNjawRzsVUGRbi7TFyDblld37FAvehzhQZCFNlngU4Ai%2FnQDrp6V%2FszDYqSVoE2lnhSYMwzY%2BAfV0OoZyetE0o3XusOC1AOHBdqQmvJxKWbhz8hlgUC16Gmo0NZTExhT4JXqzGAzKI1VOlw%2F6SwYpX1lxS%2FFHIIf1SYJyh5NBmo54j658KdAARNdPyunVPNPHkGN56MsnJ4jF%2FMQ1eXersz9Y2l6c2ohE4eQqSv0tEYsmbcCntd4EnccmcSoncrgReKr%2FhjIugKctFJTC%2F%2B4NCyN1fblSW5cqS15u7HHOmwebG%2BTGNS7dlSMPAWy24DIUc0EuWFx2v2vroqdePGzAV39sroseX0cyQjEUhn90r%2BfrsKaoFny8H60tGBSWbie%2BVIXAK%2FN0n8qn%2FLpnX%2BSQ9FgH9BP50bZDjNlHxX9upCFMBXtu26x8xA7IQVHYB2GEBnIlwp6CLusDCh67G2BjqyATbOWfIpag15KMIB7Tn1ENsqCFgXr93cyrVGJ7q5dZCFJ8yQ9yta3%2B6xc26wuAV83E0MGeTmt49ztzzbjpqAWBEjQDWud1xay92AN9MpbY9WZAFC8EpMzdsaUVIt4iTf6PbovJRWUSpxDeMTop%2BqtwdAegmjRohTYw5WiKVEDbtdfagslJ%2BS9fpI72ceTpi8jYy%2BWe%2FJaZgnlBnyXzakSAPk2vU1m1CfmaWs9FgasQ%2FC8Kk%3D&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20240826T124602Z&X-Amz-SignedHeaders=host&X-Amz-Expires=300&X-Amz-Credential=ASIAQ3PHCVTY72BLOTZM%2F20240826%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Signature=c660c60d7c21b973d8bd9cb0382e6df3f694c9dd9ca57346a1023e5d80700d48&hash=36993f6e87fc776b8329e10c439ff551dcc45baad2e8d23a39b53a255c613d0b&host=68042c943591013ac2b2430a89b270f6af2c76d8dfd086a07176afe7c76c2c61&pii=S0301479720306186&tid=spdf-463c7dcb-f981-4d91-98c0-6cc723f2e094&sid=8cab97393324b44354-b951-b9502ddf119cgxrqb&type=client&tsoh=d3d3LnNjaWVuY2VkaXJlY3QuY29t&ua=00125f05595c03025102&rr=8b93ec1e7905414c&cc=fr) (page 7, colonne : PB et PB par personne) - dernière ligne
    - Adapter les calculs à une base annuelle pour chaque années et cumulatif

## Lundi 26 Août

Modifier le graphique avec le plan du data center :
- Le nombre de racks doit etre pair
- Couloir chaud à l'intérieur, froid à l'extérieur

Autres :
- Calculer la surface en m² (voir la photo)
- Convertir la consommation sur le graphique de MWh en MkWh
- Résoudre le problème concernant le GPU A100 pour l'année 2021
- Ajouter un graphique (barplot) montrant le nombre actuel de GPUs et leur type

Fonction pour calculer la surface utilisée :

$$
\text{Surface par rack en m}^2 = \frac{(\text{Profondeur du rack} + \text{Nombre de couloirs chaud/froid} \times \text{Largeur d'un couloir}) \times (\text{Longueur du rack} \times \text{Nombre de racks} + 2 \times \text{Largeur du couloir froid})}{\text{Nombre de racks dans la salle}}
$$
___

Quesques dimensions : https://www.fr.freelancer.com/contest/create-a-server-room-d-floorplan-1572153-byentry-30297462
![](https://codimd.math.cnrs.fr/uploads/6b4e205d4204cf272ac86d31a.png)

https://www.theseverngroup.com/data-center-hot-aisle-cold-aisle-layout/
![](https://codimd.math.cnrs.fr/uploads/upload_9741c539f291d8bfbd4e5f8b234d00f2.png)
