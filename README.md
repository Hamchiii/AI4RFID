# AI4RFID — Détection de passage RFID par Machine Learning

## Contexte & Objectifs

Les portiques de sécurité équipés de lecteurs RFID souffrent d’un taux d’erreur d’environ 5 % lors de la détection d’étiquettes (« tags ») qui traversent un seuil. L’objectif de ce projet est de concevoir et d’évaluer une chaîne de traitement de données RFID capable de distinguer avec fiabilité les étiquettes **P** (passantes) des étiquettes **N** (non‑passantes), afin de réduire drastiquement les fausses alarmes et d’améliorer l’expérience client en point de vente. :contentReference[oaicite:0]{index=0}

---

## Description du dataset

Chaque fichier CSV correspond à un run RFID contenant :

| Colonne            | Description |
|--------------------|-------------|
| Timestamp          | Horodatage de la lecture |
| EPC                | Identifiant unique de l’étiquette |
| Antenna            | Position du capteur (`in` / `out`) |
| RSSI / RSSI_dBm    | Puissance du signal reçu |
| Actual             | Label binaire (P ou N) |
| slot_id, crossing_id, slot_id_norm | Séquençage temporel et normalisé |
| Sense              | Direction du passage (`IN>OUT` ou `OUT>IN`) |
| Keep_out           | Distance maximale parcourue par l’étiquette |

---

## Pipeline de traitement

1. **Prétraitement**  
   - Nettoyage et fusion des fichiers runs & actuals  
   - Extraction des features temporelles (`slot_id_norm`, `sense`, `keep_out`)  
   - Échantillonnage horizontal pour éviter les fuites de données entre train/test :contentReference[oaicite:1]{index=1}

2. **Exploration & Visualisation**  
   - Visualisation temporelle des trajectoires RFID  
   - Analyse des distributions RSSI et Sense

3. **Modélisation**  
   | Modèle | Description | Score (accuracy) |
   |--------|-------------|------------------|
   | Decision Tree | Arbre de décision simple | 0.9809 |
   | Random Forest | Ensemble d’arbres | 0.9871 |
   | Voting Classifier | Fusion de Random Forest | 0.9892 |
   | Random Forest + K‑Fold CV | Validation croisée | 0.9883 |
   | Random Forest + GridSearchCV | Hyperparameter tuning | 0.9906 |
   | Neural Network | Réseau de neurones dense | 0.9865 |
   | Neural Network + GridSearchCV | Optimisation hyperparamètres | 0.9907 |
   | Conv1D | Réseau de convolution 1D | 0.9956 |
   | Stacking | Modèle méta combinant RF, DT, NN | 0.9951 |
   | **Stacking + Bruit** | Ajout de données bruitées | **0.9996** |

4. **Améliorations avancées**  
   - Inclusion de la feature **Sense** (+0.5 % de gain)  
   - Injection de bruit sur échantillons mal classés pour généralisation  
   - Refactoring du preprocessing dans un module Python unique  

---

## Résultats clés

Le modèle de **Stacking avec injection de bruit** atteint une accuracy de **99.96 %**, réduisant le taux d’erreur de 5 % à 0.04 % sur 10 000 échantillons testés — soit une amélioration de ~98 %. :contentReference[oaicite:2]{index=2}

---
