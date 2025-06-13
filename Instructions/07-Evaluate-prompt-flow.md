---
lab:
  title: Évaluer les performances d’un modèle d’IA générative
  description: Découvrez comment évaluer les modèles et les invites pour optimiser les performances de votre application de conversation et sa capacité à répondre de manière appropriée.
---

# Évaluer les performances d’un modèle d’IA générative

Dans cet exercice, vous allez utiliser des évaluations manuelles et automatisées pour mesurer les performances d’un modèle dans le portail Azure AI Foundry.

Cet exercice prend environ **30** minutes.

> **Note** : certaines des technologies utilisées dans cet exercice sont en version préliminaire ou en cours de développement. Un comportement inattendu, des avertissements ou des erreurs peuvent se produire.

## Créer un hub et un projet Azure AI Foundry

Les fonctionnalités d’Azure AI Foundry que nous allons utiliser dans cet exercice nécessitent un projet basé sur une ressource de *hub* Azure AI Foundry.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les conseils ou les volets de démarrage rapide ouverts la première fois que vous vous connectez et, si nécessaire, utilisez le logo **Azure AI Foundry** en haut à gauche pour accéder à la page d’accueil, qui ressemble à l’image suivante (fermez le volet **Aide** s’il est ouvert) :

    ![Capture d’écran du portail Azure AI Foundry.](./media/ai-foundry-home.png)

1. Dans le navigateur, accédez à `https://ai.azure.com/managementCenter/allResources` et sélectionnez **Créer**. Choisissez ensuite l’option permettant de créer une **ressource de hub AI**.
1. Dans l’assistant **Créer un projet**, entrez un nom valide pour votre projet et utilisez le lien **Renommer le hub** pour spécifier un nom valide pour votre nouveau hub. Développez ensuite les **Options avancées** pour définir les paramètres suivants de votre projet :
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *créez ou sélectionnez un groupe de ressources*
    - **Région** : sélectionnez l’un des emplacements suivants (*si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région*) :
        - USA Est 2
        - France Centre
        - Sud du Royaume-Uni
        - Suède Centre

    > **Note** : si vous travaillez dans un abonnement Azure dans lequel les stratégies sont utilisées pour restreindre les noms de ressources autorisés, vous devrez peut-être utiliser le lien en bas de la boîte de dialogue **Créer un projet** pour créer le hub à l’aide du portail Azure.

    > **Conseil** : si le bouton **Créer** est toujours désactivé, veillez à renommer votre hub en une valeur alphanumérique unique.

1. Attendez que votre projet soit créé.

## Déployer des modèles

Dans cet exercice, vous allez évaluer les performances d’un modèle gpt-4o-mini. Vous utiliserez également un modèle gpt-4o pour générer des indicateurs d’évaluation assistée par IA.

1. Dans le volet de navigation à gauche de votre projet, dans la section **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
1. Sur la page **Modèles + points de terminaison**, dans l’onglet **Déploiements de modèles**, dans le menu **+ Déployer un modèle**, sélectionnez **Déployer le modèle de base**.
1. Recherchez le modèle **gpt-4o** dans la liste, puis sélectionnez-le et confirmez.
1. Déployez le modèle avec les paramètres suivants en sélectionnant **Personnaliser** dans les détails du déploiement :
    - **Nom du déploiement** : *nom valide pour votre modèle de déploiement*
    - **Type de déploiement** : standard global
    - **Mise à jour automatique de la version** : activée
    - **Version du modèle** : *sélectionnez la version la plus récente disponible.*
    - **Ressource IA connectée** : *sélectionnez votre connexion de ressources Azure OpenAI*
    - **Limite de jetons par minute (en milliers)**  : 50 *(ou le maximum disponible dans votre abonnement si inférieur à 50 000)*
    - **Filtre de contenu** : DefaultV2

    > **Remarque** : La réduction du nombre de jetons par minute permet d’éviter une surutilisation du quota disponible dans l’abonnement que vous utilisez. 50 000 jetons par minute sont suffisants pour les données utilisées dans cet exercice. Si votre quota disponible est inférieur à cette valeur, vous pourrez tout de même terminer l’exercice, mais vous pourriez rencontrer des erreurs en cas de dépassement de la limite.

1. Attendez la fin du déploiement.
1. Revenez à la page **Modèles + points de terminaison** et répétez les étapes précédentes pour déployer un modèle **gpt-4o-mini** avec les mêmes paramètres.

## Évaluer manuellement un modèle

Vous pouvez examiner manuellement les réponses de modèle en fonction des données de test. L’évaluation manuelle permet de tester différentes entrées afin de vérifier si le modèle se comporte comme prévu.

1. Dans un nouvel onglet de navigateur, téléchargez le fichier [travel_evaluation_data.jsonl](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.jsonl) depuis `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.jsonl`, puis enregistrez-le dans un dossier local sous le nom **travel_evaluation_data.jsonl** (assurez-vous de l’enregistrer en tant que fichier .jsonl, et non en tant que fichier .txt).
1. De retour sur l’onglet du Portail Azure AI Foundry, dans le volet de navigation, dans la section **Protéger et gérer**, sélectionnez **Évaluation**.
1. Si le volet **Créer une nouvelle évaluation** s’ouvre automatiquement, sélectionnez **Annuler** pour le fermer.
1. Sur la page **Évaluation**, ouvrez l’onglet **Évaluations manuelles**, puis sélectionnez **+ Nouvelle évaluation manuelle**.
1. Dans la section **Configurations**, dans la liste **Modèle**, sélectionnez votre modèle de déploiement **gpt-4o**.
1. Remplacez le **message système** par les instructions suivantes pour un assistant IA de voyages :

   ```
   Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.
   ```

1. Dans la section **Résultat de l’évaluation manuelle**, sélectionnez **Importer des données de test** et chargez le fichier **travel_evaluation_data.jsonl** que vous avez téléchargé précédemment, en associant les champs du jeu de données comme suit :
    - **Entrée** : Question
    - **Réponse attendue** : ExpectedResponse
1. Passez en revue les questions et les réponses attendues dans le fichier de test. Vous les utiliserez pour évaluer les réponses générées par le modèle.
1. Sélectionnez **Exécuter** dans la barre supérieure pour générer des sorties pour toutes les questions que vous avez ajoutées en tant qu’entrées. Après quelques minutes, les réponses du modèle devraient s’afficher dans une nouvelle colonne **Sortie**, comme ceci :

    ![Capture d’écran d’une page d’évaluation manuelle dans le portail Azure AI Foundry.](./media/manual-evaluation.png)

1. Analysez les sorties pour chaque question, comparez la réponse du modèle à la réponse attendue et « notez » les résultats en cliquant sur l’icône de pouce levé ou baissé en bas à droite de chaque réponse.
1. Une fois les réponses notées, examinez les vignettes récapitulatives situées au-dessus de la liste. Ensuite, dans la barre d’outils, sélectionnez **Enregistrer les résultats** et spécifiez un nom approprié. L’enregistrement des résultats vous permettra de retrouver ces derniers ultérieurement pour une nouvelle évaluation ou une comparaison avec un autre modèle.

## Utiliser l’évaluation automatisée

Comparer manuellement les réponses du modèle à vos attentes peut être utile pour évaluer ses performances, mais cette méthode devient fastidieuse lorsque vous vous attendez à une grande variété de questions et réponses et fournit peu d’indicateurs standardisés que vous pouvez utilisez pour comparer différentes combinaisons de modèles et invites.

L’évaluation automatisée vise à pallier ces limites en calculant des indicateurs et en s’appuyant sur l’IA pour évaluer la cohérence, la pertinence et d’autres aspects des réponses.

1. Utilisez la flèche de retour (**&larr;**) à côté du titre de la page **Évaluation manuelle** pour revenir à la page **Évaluation**.
1. Ouvrez l’onglet **Évaluations automatisées**.
1. Sélectionnez **Créer une nouvelle évaluation** et, lorsqu’on vous le demande, choisissez l’option **Évaluer un modèle**, puis sélectionnez **Suivant**.
1. Sur la page **Sélectionner une source de données**, sélectionnez **Utiliser votre jeu de données** et sélectionnez le jeu de données **travel_evaluation_data_jsonl_*xxxx...*** basé sur le fichier que vous avez chargé précédemment, puis sélectionnez **Suivant**.
1. Sur la page **Tester votre modèle**, sélectionnez le modèle **gpt-4o-mini** et remplacez le **message système** par les mêmes instructions que celles utilisées précédemment pour un assistant de voyage IA :

   ```
   Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.
   ```

1. Pour le champ **Requête**, sélectionnez **\{\{item.question\}\}**.
1. Cliquez sur **Suivant** pour passer à la page suivante.
1. Sur la page **Configurer les programmes d’évaluation**, utilisez le bouton **+Ajouter** pour ajouter les programmes d’évaluation suivants, en configurant chacun comme suit :
    - **Évaluateur de modèle** :
        - **Nom du critère** : Semantic_similarity
        - **Classer avec** : *sélectionnez votre modèle **gpt-4o***
        - Paramètres **utilisateur** (en bas) :


            Sortie : \{\{sample.output_text\}\}<br>
            Vérité de référence : \{\{item.ExpectedResponse\}\}<br>
            <br>
        
    - **Likert-scale evaluator** :
        - **Nom du critère** : Relevance
        - **Classer avec** : *sélectionnez votre modèle **gpt-4o***
        - **Requête** : \{\{item.question\}\}

    - **Similarité du texte** :
        - **Nom du critère** : F1_Score
        - **Vérité de référence** : \{\{item.ExpectedResponse\}\}

    - **Contenus haineux et injustes** :
        - **Nom du critère** : Hate_and_unfairness
        - **Requête** : \{\{item.question\}\}

1. Sélectionnez **Suivant** pour vérifier les paramètres d’évaluation. Vous devez avoir configuré l’évaluation de manière à utiliser le jeu de données d’évaluation de voyage pour évaluer la similarité sémantique, la pertinence, le score F1 et le langage haineux et injuste du modèle **gpt-4o-mini**.
1. Donnez un nom approprié à l’évaluation, **envoyez-la** pour démarrer le processus d’évaluation et attendez qu’il se termine. Cette opération peut prendre quelques minutes. Vous pouvez utiliser le bouton de barre d’outils **Actualiser** pour vérifier le statut.

1. Une fois l’évaluation terminée, faites défiler vers le bas si nécessaire pour examiner les résultats.

    ![Capture d’écran des mesures de l’évaluation.](./media/ai-quality-metrics.png)

1. En haut de la page, sélectionnez l’onglet **Données** pour afficher les données brutes issues de l’évaluation. Les données incluent les indicateurs associés à chaque entrée ainsi que les explications du raisonnement appliqué par le modèle gpt-4o lors de l’évaluation des réponses.

## Nettoyage

Une fois l’exploration d’Azure AI Foundry terminée, supprimez les ressources créées afin d’éviter des coûts Azure superflus.

- Accédez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com`.
- Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
- Sélectionnez le groupe de ressources créé pour cet exercice.
- Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
- Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.
