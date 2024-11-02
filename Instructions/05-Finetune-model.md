---
lab:
  title: "Ajuster un modèle de langage pour la saisie semi-automatique de la conversation dans Azure\_AI\_Studio"
---

# Ajuster un modèle de langage pour la saisie semi-automatique de la conversation dans Azure AI Studio

Dans cet exercice, vous allez ajuster un modèle de langage avec Azure AI Studio afin de l’utiliser pour un scénario de copilote personnalisé.

Cet exercice prend environ **45** minutes.

## Créer un hub IA et un projet dans Azure AI Studio

Vous commencez par créer un projet Azure AI Studio au sein d’un hub Azure AI :

1. Dans un navigateur web, ouvrez [https://ai.azure.com](https://ai.azure.com) et connectez-vous à l’aide de vos informations d’identification Azure.
1. Sélectionnez la page **Accueil**, puis sélectionnez **+ Nouveau projet**.
1. Dans l’Assistant **Créer un projet**, créez ensuite un projet avec les paramètres suivants :
    - **Nom du projet** : *Un nom unique pour votre projet*
    - **Hub** : *Créer un hub avec les paramètres suivants :*
        - **Hub name** : *Un nom unique*
        - **Abonnement** : *votre abonnement Azure*
        - **Groupe de ressources** : *Un nouveau groupe de ressources*
        - **Emplacement** : *Choisir de manière **aléatoire** une région parmi les suivantes*\*
        - USA Est 2
        - Centre-Nord des États-Unis
        - Suède Centre
        - Suisse Nord
    - **Connecter Azure AI Services ou Azure OpenAI** : *Créer une connexion*
    - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \* Les ressources Azure OpenAI sont limitées au niveau du locataire par quotas régionaux. Les régions répertoriées incluent le quota par défaut pour les types de modèle utilisés dans cet exercice. Le choix aléatoire d’une région réduit le risque qu’une seule région atteigne sa limite de quota. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région. En savoir plus sur la [disponibilité du modèle par région](https://learn.microsoft.com/en-us/azure/ai-studio/concepts/fine-tuning-overview#azure-openai-models)

1. Examinez votre configuration et créez votre projet.
1. Attendez que votre projet soit créé.

## Ajuster un modèle GPT-3.5

Avant de pouvoir ajuster un modèle, vous avez besoin d’un jeu de données.

1. Enregistrez le jeu de données de démo localement en tant que fichier JSONL : https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-finetune.jsonl
1. Accédez à la page **Ajustement** sous la section **Outils**, à l’aide du menu de gauche.
1. Sélectionnez le bouton pour ajouter un nouveau modèle ajusté, sélectionnez le modèle `gpt-35-turbo`, puis sélectionnez **Confirmer**.
1. **Ajustez** le modèle à l’aide de la configuration suivante :
    - **Version du modèle** : *Sélectionnez la version par défaut*
    - **Suffixe de modèle** : `ft-travel`
    - **Connexion Azure OpenAI** : *sélectionnez la connexion qui a été créée lors de la création de votre hub*
    - **Données d’apprentissage** : charger des fichiers
    - **Charger fichier** : sélectionnez le fichier JSONL que vous avez téléchargé lors d’une étape précédente.

    > **Conseil** : vous n’avez pas besoin d’attendre que le traitement des données soit terminé pour passer à l’étape suivante.

    - **Données de validation** : aucune
    - **Paramètres de tâche** : *conserver les paramètres par défaut*
1. L’ajustement commence et peut prendre un certain temps.

> **Remarque** : l’ajustement et le déploiement peuvent prendre un certain temps. Vérifiez l’avancement régulièrement afin de pouvoir effectuer l’étape suivante.

## Déployer le modèle ajusté

Une fois l’ajustement terminé, vous pouvez déployer le modèle.

1. Sélectionnez le modèle ajusté. Sélectionnez l’onglet **Métriques** et explorez les métriques ajustées.
1. Déployez le modèle ajusté avec les paramètres suivants :
    - **Nom du déploiement** : *nom unique pour votre modèle ; vous pouvez utiliser la valeur par défaut*
    - **Type de déploiement** : Standard
    - **Limite de débit en jetons par minute (en milliers)** : 5 000
    - **Filtre de contenu** : valeur par défaut

## Tester le modèle ajusté

Maintenant que vous avez déployé votre modèle ajusté, vous pouvez le tester comme n’importe quel autre modèle déployé.

1. Une fois le déploiement terminé, accédez au modèle ajusté et sélectionnez **Ouvrir dans le terrain de jeu**.
1. Dans la fenêtre de conversation, entrez la requête `What can you do?` Notez que même si vous n’avez pas spécifié de message système indiquant à votre modèle de répondre aux questions liées aux voyages, le modèle sait déjà ce sur quoi il doit se concentrer.
1. Essayez avec une autre requête, comme `Where should I go on holiday for my 30th birthday?`

## Nettoyage

Lorsque vous avez terminé d’explorer Azure AI Studio, supprimez les ressources que vous avez créées afin d’éviter des coûts Azure inutiles.

- Accédez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com`.
- Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
- Sélectionnez le groupe de ressources créé pour cet exercice.
- Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
- Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.
