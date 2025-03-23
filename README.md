# 📄 test DGFiP – Attribution de fiche pratique par similarité de questions

Ce projet vise à attribuer une **fiche pratique** à une question d’utilisateur, en identifiant une question consensus similaire à l’aide d’un modèle **Siamese BERT fine-tuné**.

---

## 📚 Objectif

L’idée est de détecter si deux questions sont similaires (`question_1` et `question_2`).  
Si c’est le cas, on transfère la **classe (fiche pratique)** de la `question_2` (consensus) à la `question_1`.

---

## 🧠 Modèle utilisé : Siamese BERT avec CamemBERT

Le cœur du projet repose sur un **modèle Siamese BERT** construit à partir du checkpoint francophone :

> [`camembert/camembert-base-wikipedia-4gb`](https://huggingface.co/camembert/camembert-base-wikipedia-4gb)

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
