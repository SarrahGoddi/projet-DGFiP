# 📄 test DGFiP – Attribution de fiche pratique par similarité de questions

Ce projet vise à attribuer une **fiche pratique** à une question d’utilisateur, en identifiant une question consensus similaire à l’aide d’un modèle **Siamese BERT fine-tuné**.

---

## 📚 Objectif

L’idée est de détecter si deux questions sont similaires (`question_1` et `question_2`).  
Si c’est le cas, on transfère la **classe (fiche pratique)** de la `question_2` (consensus) à la `question_1`.

---

## 🔧 Modèle utilisé

Le modèle utilisé est un **Siamese BERT** basé sur la version francophone pré-entraînée :

- **Checkpoint :** [`camembert/camembert-base-wikipedia-4gb`](https://huggingface.co/camembert/camembert-base-wikipedia-4gb)
- **Architecture Siamese** : deux entrées (question_1, question_2) passent dans un encodeur partagé (`CamemBERT`) suivi d'une couche dense pour évaluer leur similarité.
- **Fonction de perte :** Binary Cross-Entropy
- **Fine-tuning** effectué sur un jeu de paires de questions annotées comme similaires (1) ou non (0)
- **Accuracy sur validation :** ~0.80

Le choix de ce checkpoint vise à tirer parti des performances de CamemBERT entraîné sur 4GB de Wikipédia française.

--

## 📁 Contenu du dépôt

| Fichier / Dossier          | Description |
|----------------------------|-------------|
| `notebook_model.ipynb`     | Notebook d’entraînement du modèle Siamese BERT |
| `info_particulier_impot.csv`     | Données |
| `questions_fiches_fip.csv`     | Données |
| `data/`                    | jeu de paires de questions annotées (pour l'entrainement de Siamese BERT) |
| `README.md`                | Ce fichier de présentation |


---
