# Manuel d'Utilisation - SmartBimPeï

Ce document détaille l'utilisation des 5 modules de la suite SmartBimPeï.

---

## 1. Détection de Conflits (Clash Detector)

Ce module est le cœur de SmartBimPeï. Il permet d'analyser deux modèles (ou un modèle contre lui-même) pour détecter les intersections physiques (clashs) entre les éléments.

### Description de l'interface

| Élément | Description et Fonctionnement |
| :--- | :--- |
| **Modèle A / Modèle B** | Sélectionnez le fichier cible (maquette courante ou lien Revit) pour chaque modèle. C'est entre ces deux modèles que la détection sera lancée. |
| **Catégories d'éléments** | Cochez les catégories Revit à analyser pour chaque modèle (ex: Murs, Dalles, Canalisations, Gaines). |
| **Niveau (Filtre)** | Permet de restreindre l'analyse à un niveau spécifique du bâtiment pour gagner du temps. |
| **Tolérance (mm)** | Marge de chevauchement tolérée. Si deux objets se touchent sur une profondeur inférieure à cette tolérance, le clash est ignoré. |
| **Taille min. (Modèle A & B)** | Exclut de l'analyse les éléments trop petits (ex: ignorer les tuyaux d'un diamètre inférieur à 32mm). |
| **Marge Auto-Réservation** | Ajoute une marge de sécurité (en mm) autour du clash lors de la création d'une boîte de réservation. |
| **Filtre par Paramètre** | Permet de cibler précisément certains éléments (ex: uniquement les murs dont le paramètre 'Fonction' = 'Structure'). |
| **Filtre d'orientation** | (Optionnel) Ciblez uniquement les réseaux Horizontaux ou Verticaux dans le Modèle B. |
| **Bouton : Lancer la détection** | Démarre le scan. Les résultats s'afficheront dans la grille avec des options pour générer les réservations ou isoler la vue. |

### Flux de données et Partage (Important)
À chaque détection, SmartBimPeï génère automatiquement un fichier **.xml** invisible dans le même dossier que votre fichier Revit (.rvt). 
Pour partager votre analyse avec d'autres intervenants (ex: BET Structure), **envoyez-leur toujours le .rvt ET le .xml dans le même dossier**. Ils verront ainsi vos résultats directement dans leur Dashboard.

---

## 2. Analyse de la Qualité des Données (Data Quality)

Ce module vérifie si les éléments de votre maquette sont correctement renseignés.

| Élément | Description et Fonctionnement |
| :--- | :--- |
| **Sélection de la Catégorie** | Choisissez la famille d'objets à inspecter (ex: Portes, Fenêtres). |
| **Paramètre à vérifier** | Saisissez le nom exact du paramètre Revit (ex: 'Résistance au feu', 'Marque'). |
| **Règle de validation** | Définissez ce qui est attendu (ex: 'Doit contenir du texte', 'Doit être supérieur à 0'). |
| **Bouton : Lancer l'analyse** | Scanne le projet et liste tous les éléments ne respectant pas votre règle (paramètres vides ou erronés). |
| **Bouton : Corriger (Batch)** | Permet de remplir ou corriger rapidement une valeur pour plusieurs éléments sélectionnés d'un seul clic. |

---

## 3. Nettoyage des Doublons (Duplicate Cleaner)

Ce module détecte et supprime les éléments modélisés deux fois au même endroit.

| Élément | Description et Fonctionnement |
| :--- | :--- |
| **Sensibilité (Tolérance X,Y,Z)** | Règle la distance (en mm) en dessous de laquelle deux éléments sont considérés comme "superposés". |
| **Catégories à scanner** | Cochez les catégories où vous suspectez des doublons (fréquent sur les Imports DWG/IFC ou les modèles collaboratifs). |
| **Bouton : Trouver les doublons** | Affiche la liste des éléments superposés avec leur ID. |
| **Bouton : Supprimer les copies** | Efface automatiquement un des deux éléments pour nettoyer la maquette et alléger le poids du fichier. |

---

## 4. Extraction de Métrés (Quantification)

Générez rapidement des quantitatifs fiables pour vos chiffrages.

| Élément | Description et Fonctionnement |
| :--- | :--- |
| **Choix du type de métré** | Longueur (Réseaux, Murs), Surface (Dalles, Peinture), ou Volume (Béton). |
| **Regroupement** | Option pour regrouper les résultats par Niveau ou par Type (ex: Total linéaire de Tuyau PVC Ø100 au RDC). |
| **Bouton : Calculer** | Lance le calcul des quantités réelles extraites de la géométrie 3D. |
| **Bouton : Exporter vers Excel** | Sauvegarde le tableau de résultats dans un fichier .xlsx formaté. |

---

## 5. Dashboard (Tableau de bord Global)

Supervisez l'état de santé de vos projets et l'avancement des synthèses.

| Élément | Description et Fonctionnement |
| :--- | :--- |
| **Graphique d'avancement (Camembert)** | Affiche le ratio de clashs "Résolus" vs "Actifs" (lu depuis le fichier XML). |
| **Indicateur de Santé (Score)** | Note globale de la maquette basée sur la qualité des données et le nombre de doublons. |
| **Bouton : Rafraîchir les données** | Force la relecture du fichier XML et du modèle pour mettre à jour les graphiques en temps réel. |

