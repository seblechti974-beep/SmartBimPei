# SmartBimPeï - Fiche Technique & Architecture

**SmartBimPeï** (anciennement SmartClashDetector) est un plugin professionnel développé sur-mesure pour Autodesk Revit®. Initialement conçu pour révolutionner la synthèse géométrique (Clash Detection), le plugin a évolué en une suite complète de 5 modules distincts couvrant l'ensemble du spectre du BIM Management : Synthèse géométrique, Audit de la donnée (Data Quality), Nettoyage de maquette, Tableau de bord décisionnel, et Métré.

> [!TIP]
> **Objectif Technique :** Offrir une précision "chirurgicale" sur les géométries complexes (réseaux courbes, dalles), garantir une fluidité totale de Revit lors du traitement de milliers de données, et assurer un contrôle de conformité infaillible (GMAO / DOE).

---

## 1. Architecture Logicielle & Performances

La création de SmartBimPeï a nécessité de résoudre de nombreux défis propres à l'API Revit :

*   **Architecture Asynchrone (Thread-Safe)** : Utilisation de threads séparés (Tâches asynchrones) pour les calculs géométriques et la vérification des paramètres. La communication vers l'interface WPF se fait via des `ExternalEvent` (`IExternalEventHandler`), empêchant ainsi tout "freezing" (gel) de Revit, même lors de l'analyse de milliers de clashs ou de propriétés.
*   **Stabilité & Sécurité (Anti-Crash)** : Mise en place de `TransactionGroup` et de `RollBack` lors des visualisations. Les actions modifiant virtuellement la maquette (pour isoler un objet ou appliquer de la transparence) sont proprement gérées ou annulées en fin de script. La maquette n'est jamais modifiée accidentellement.
*   **Code Professionnel (MVVM)** : Implémentation du pattern MVVM (Model-View-ViewModel) partiel et séparation stricte des `Views` (Fichiers `.xaml`), des `Models` (Données) et des `Handlers` (Logique métier de l'API Revit).

---

## 2. Module 1 : Smart Clash Detector (Synthèse Géométrique)

Le cœur historique du plugin. Il remplace avantageusement le détecteur natif de Revit en apportant des options de filtrage avancées et une création automatisée de réservations.

### Fonctionnalités Techniques :
*   **Analyse Multicritères** : Croisement entre une sélection A et B (Gaines, Canalisations, Chemins de Câbles, Murs, Dalles...).
*   **Calcul Géométrique Avancé (BoundingBoxes)** : Détermination précise des `BoundingBoxes` complexes (incluant les modèles liés avec leurs transformations de coordonnées `Transform`) pour un ciblage parfait des conflits dans l'espace tridimensionnel.
*   **Tolérance Géométrique & Sliver Tolerance** : Filtrage des micro-conflits (ex: le contact d'un isolant ou les tolérances chantier normales) selon un seuil millimétrique paramétrable.
*   **Auto-Réservation Intelligente** : Insertion automatisée de familles de "vides" (Vides rectangulaires ou circulaires) à l'emplacement exact de l'intersection, avec gestion d'une "Marge de Réservation" paramétrable (ex: +50mm) pour anticiper l'isolation.
*   **Rendu Visuel "Chirurgical"** : Basculement automatique de la vue 3D avec un gabarit transparent gris pour le contexte, et couleurs opaques à haut contraste pour les deux éléments en conflit.

---

## 3. Module 2 : BIM Health Dashboard (Tableau de Bord)

Un outil décisionnel pour les chefs d'entreprise et directeurs de projets.

### Fonctionnalités Techniques :
*   **Score Global de Santé (KPI)** : Algorithme lisant les résultats de l'ensemble des autres modules (clashs non résolus, doublons MEP, données non conformes) pour calculer un score sur 100 de la santé du modèle BIM.
*   **Indicateurs Financiers** : Croisement des détections (doublons, quantités) avec une base de coûts estimative pour chiffrer l'impact financier des erreurs de modélisation.
*   **Rapport de Synthèse** : Génération d'exports structurés (PDF, Excel) récapitulant les KPIs pour les revues de direction.

---

## 4. Module 3 : Duplicate Detector (Nettoyeur de Maquette)

Un outil radical pour lutter contre les surcoûts matériels liés à la superposition d'éléments MEP (doubles commandes).

### Fonctionnalités Techniques :
*   **Vérification de Superposition Stricte** : L'algorithme ne se contente pas de trouver des intersections partielles, il vérifie si deux éléments partagent exactement la même géométrie (même courbe centrale, mêmes sommets) et les mêmes dimensions.
*   **Purge Automatisée** : Suppression en un clic des éléments redondants identifiés, avec rafraîchissement dynamique de la liste.

---

## 5. Module 4 : BIM Data Audit & Compliance (Contrôle Qualité)

Dernière grande évolution majeure de SmartBimPeï, ce module est le "gendarme de la donnée". Il assure la validité de la maquette pour les phases d'Exploitation-Maintenance (Facility Management / GMAO) et de Dossier d'Ouvrage Exécuté (DOE).

> [!IMPORTANT]
> **Innovation "Cerveau Central" (Anti-Faux Positif)**
> L'algorithme de vérification (`HasValidParameter`) a été conçu pour reproduire l'intelligence d'un BIM Manager humain. Lorsqu'il cherche un paramètre (ex: *Résistance au Feu* ou *Matériau Structurel*), **il inspecte d'abord l'occurrence (Instance)**. S'il ne le trouve pas ou s'il est vide, **il va chercher automatiquement cette même information dans les propriétés du Type (`ElementType`)**. Cela garantit l'absence de faux positifs et évite les crashs de requêtes nulles.

### Contrôles par Corps d'État (Disciplines) :

1. **Architecture (Second-Œuvre)** *(Portes, Fenêtres, Plafonds, Murs)*
   - Vérification du paramètre natif **Identifiant (Marque)** pour tous.
   - Vérification de la **Résistance au Feu** (Compartimentage) sur les Murs et Portes.
   - Vérification du **Fabricant** et du **Modèle** pour les Portes, Fenêtres et Plafonds (GMAO).

2. **Structure (Gros-Œuvre)** *(Murs, Dalles, Poutres, Poteaux)*
   - Vérification de l'**Identifiant (Marque)**.
   - Vérification du **Matériau Structurel** (Crucial pour le bilan carbone et les calculs de charge).
   - Vérification de la **Résistance au Feu** (Réglementation).

3. **CVC (Gaines & Équipements)**
   - Vérification **Système de Gaine** (Classification d'Air).
   - Vérification **Fabricant** et **Modèle** sur les équipements (CTA, Terminaux).
   - **Localisation Spatiale** (Pièce / Espace) obligatoire pour les terminaux afin de certifier les rapports COBie.

4. **Plomberie (Canalisations & Équipements)**
   - Vérification du **Système de Canalisation** (ex: EU, EV, ECS).
   - Vérification de la mention *Par défaut* dans le paramètre Famille & Type.
   - Vérification **Fabricant**, **Modèle** et **Localisation Spatiale** (Pièce/Espace) pour les équipements et sanitaires.

5. **Électricité (CFO/CFA & Chemins)**
   - Vérification **Panneau/Tableau** d'alimentation (pour les luminaires et équipements).
   - Vérification de l'**Identifiant (Marque)** des TGBT et équipements CFA (SSI, VDI).
   - Vérification **Fabricant**, **Modèle** et **Localisation Spatiale** stricte.

---

## 6. Module 5 : Métré Express (Quantities & Pricing)

Un pont direct entre la géométrie 3D et l'économie de la construction.

### Fonctionnalités Techniques :
*   **Extraction Paramétrique** : Récupération des valeurs géométriques réelles (Longueur, Surface, Volume, Diamètre) dynamiquement depuis l'API, indépendamment des nomenclatures natives de Revit.
*   **Valorisation Financière** : Croisement des quantitatifs extraits avec une bibliothèque de prix unitaire intégrée pour obtenir une estimation totale instantanée.
*   **Interopérabilité** : Fonction d'import/export robuste (format CSV/Excel) pour l'échange de données avec des logiciels d'économie de la construction tiers.

---

*Ce document certifie les capacités techniques de pointe de la suite SmartBimPeï, démontrant une maîtrise experte de l'API Revit (C#), du développement d'interfaces (WPF) et des enjeux complexes de la coordination OpenBIM et du Facility Management.*
