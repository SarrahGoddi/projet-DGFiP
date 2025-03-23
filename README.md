# 📄 test DGFiP – Attribution de fiche pratique par similarité des questions

Ce projet vise à attribuer une **fiche pratique** à une question d’utilisateur, en identifiant une question consensus similaire à l’aide d’un modèle **Siamese BERT fine-tuné**.

---

## 📚 Objectif

L’idée est de détecter si deux questions sont similaires (`question_1` et `question_2`).  
Si c’est le cas, on transfère la **classe (fiche pratique)** de la `question_2` (consensus) à la `question_1`.

---

## 🧠 Modèle utilisé : Entrainement d'un modèle Siamese basé sur des représentations gelées de CamemBERT

Le modèle mis en place repose sur une architecture **Siamese Network** entraînée pour détecter la similarité entre deux questions en français.

Le backbone linguistique utilisé est le modèle pré-entraîné francophone :
> [`camembert/camembert-base-wikipedia-4gb`](https://huggingface.co/camembert/camembert-base-wikipedia-4gb)

CamemBERT est utilisé **en tant qu’extracteur de caractéristiques figé** (`trainable=False`) : ses poids ne sont pas modifiés durant l’entraînement. Le fine-tuning concerne uniquement les **couches supérieures** du modèle Siamese.

Cette approche permet d’exploiter la richesse sémantique de CamemBERT tout en entraînant un modèle léger et spécialisé sur la tâche de similarité.

### 🔧 Architecture du modèle :

- **Encodeur partagé** : le modèle utilise une architecture Siamese où deux questions passent indépendamment par le **même encoder CamemBERT** (poids partagés).
- **Encapsulation BERT** : le modèle est encapsulé dans une couche personnalisée (`BertLayer`).
- **Pooling** : sortie moyenne (`GlobalAveragePooling1D`) pour obtenir un vecteur de chaque question.
- **Comparaison** : les vecteurs sont comparés via la **distance L1** (couche personnalisée `L1Dist`).
- **Classification** :
  - Dense(512, relu)
  - Dense(1, sigmoid) → prédiction de similarité (0 ou 1)

### 🔒 BERT figé

Les poids de CamemBERT sont figés (`trainable=False`) pour accélérer l’entraînement.

### 📉 Entraînement

- **Fonction de perte** : `binary_crossentropy`
- **Optimiseur** : `Adam`, `lr=1e-5`
- **Callbacks** :
  - `EarlyStopping` sur la validation (`patience=5`)
  - `ReduceLROnPlateau` automatique
- **Epochs** : 18
- **Batch size** : 32

### 📊 Évaluation du modèle

Le modèle a été évalué sur un jeu de validation constitué de paires de questions annotées comme similaires ou non.

### ✅ Résultats obtenus :

| Métrique              | Score        |
|-----------------------|--------------|
| Accuracy              | 0.80         |


➡️ Le modèle atteint **80% de précision globale**  sur les données test.  
Il est utilisé pour **transférer la fiche pratique** d’une question consensus à une nouvelle question automatiquement.

### 🧪 Méthode d’évaluation

- Les prédictions sont comparées à la vérité terrain.
- Seules les paires de questions prédictes comme "similaires" (probabilité > 0.5) sont utilisées pour l’attribution de fiche


---

🎯 **Objectif** : prédire si deux questions sont similaires (`1`) ou non (`0`), afin de transférer la fiche pratique de la question consensus vers la nouvelle question.

--

## 📁 Contenu du dépôt

| Fichier / Dossier          | Description |
|----------------------------|-------------|
| `notebook_model.ipynb`     | Notebook d’entraînement du modèle Siamese BERT |
| `info_particulier_impot.csv`     | Données |
| `questions_fiches_fip.csv`     | Données |
| `data/`                    | jeu de paires de questions annotées (pour l'entrainement de Siamese BERT) |
| `README.md`                | Fichier de présentation |


---
