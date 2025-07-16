---
lab:
  title: Ajuster un modèle de langage
  description: Découvrez comment utiliser vos propres données d’entraînement pour ajuster un modèle et personnaliser son comportement.
---

# Ajuster un modèle de langage

Lorsque vous souhaitez qu’un modèle de langage se comporte d’une certaine façon, vous pouvez utiliser l’ingénierie d’invite pour définir le comportement souhaité. Lorsque vous souhaitez améliorer la cohérence du comportement souhaité, vous pouvez choisir d’ajuster un modèle, en le comparant à votre approche d’ingénierie d’invite pour évaluer la méthode la mieux adaptée à vos besoins.

Dans cet exercice, vous allez ajuster un modèle de langage avec Azure AI Foundry afin de l’utiliser pour un scénario d’application de conversation personnalisée. Vous allez comparer le modèle ajusté à un modèle de base pour déterminer si le modèle ajusté correspond mieux à vos besoins.

Imaginez que vous travaillez pour une agence de voyage et que vous développez une application de conversation pour aider les gens à planifier leurs vacances. L’objectif est de créer une conversation simple et inspirante, qui suggère des destinations et des activités avec un ton conversationnel cohérent et chaleureux.

Cet exercice prend environ **60 minutes**\*.

> \***Note** : cette durée est une estimation basée sur une moyenne des expériences. L’ajustement dépend des ressources de l’infrastructure cloud, celles-ci pouvant nécessiter un certain temps d’approvisionnement en fonction de la capacité du centre de données et de la demande simultanée. Certaines activités de cet exercice peuvent prendre <u>beaucoup</u> de temps et nécessiter de la patience. Si des éléments prennent beaucoup de temps, envisagez de consulter la [documentation relative à l’ajustement d’Azure AI Foundry](https://learn.microsoft.com/azure/ai-studio/concepts/fine-tuning-overview) ou de prendre une pause. Il est possible que certains processus expirent ou semblent s’exécuter indéfiniment. Certaines des technologies utilisées dans cet exercice sont en version préliminaire ou en cours de développement. Il se peut que vous rencontriez des comportements, inattendus, des avertissements ou des erreurs.

## Déployer un modèle dans un projet Azure AI Foundry

Commençons par déployer un projet Azure AI Foundry.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les conseils ou les volets de démarrage rapide ouverts la première fois que vous vous connectez et, si nécessaire, utilisez le logo **Azure AI Foundry** en haut à gauche pour accéder à la page d’accueil, qui ressemble à l’image suivante (fermez le volet **Aide** s’il est ouvert) :

    ![Capture d’écran du portail Azure AI Foundry.](./media/ai-foundry-home.png)

1. Dans la page d’accueil, dans la section **Explorer les modèles et les fonctionnalités**, recherchez le modèle `gpt-4o` ; que nous utiliserons dans notre projet.
1. Dans les résultats de la recherche, sélectionnez le modèle **gpt-4o** pour afficher ses détails, puis en haut de la page du modèle, sélectionnez **Utiliser ce modèle**.
1. Lorsque vous êtes invité à créer un projet, entrez un nom valide pour votre projet et développez **les options avancées**.
1. Sélectionnez **Personnaliser** et spécifiez les paramètres suivants pour votre projet :
    - **Ressource Azure AI Foundry** : *un nom valide pour votre ressource Azure AI Foundry.*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *créez ou sélectionnez un groupe de ressources*
    - **Région** : *sélectionnez l’une des régions suivantes* :\*
        - USA Est 2
        - Centre-Nord des États-Unis
        - Suède Centre

    > \* À la date de rédaction, ces régions prennent en charge l’ajustement fin des modèles gpt-4o.

1. Sélectionnez **Créer** et attendez que votre projet, y compris le déploiement du modèle gpt-4 que vous avez sélectionné, soit créé.
1. Une fois le projet créé, le terrain de jeu de conversation instantanée s’ouvrira automatiquement.
1. Dans le volet **Configuration**, notez le nom de votre modèle de déploiement ; il devrait s’agir de **gpt-4o**. Vous pouvez le confirmer en affichant le déploiement dans la page **Modèles et points de terminaison** (ouvrez simplement cette page dans le volet de navigation à gauche).
1. Dans le volet de navigation à gauche, sélectionnez **Vue d’ensemble** pour accéder à la page principale de votre projet ; elle se présente comme suit :

    ![Capture d’écran d’une page de présentation d’un projet Azure AI Foundry.](./media/ai-foundry-project.png)

## Ajuster vos modèles

Comme l’ajustement fin d’un modèle prend du temps, commencez le travail d’ajustement dès maintenant, puis revenez-y après avoir exploré le modèle gpt-4o de base déjà déployé.

1. Téléchargez le [jeu de données de démo](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) à l’adresse `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl` et enregistrez-le en tant que fichier JSONL localement.

    > **Remarque** : votre appareil peut par défaut enregistrer le fichier en tant que fichier .txt. Sélectionnez tous les fichiers et supprimez le suffixe .txt pour vous assurer que vous enregistrez le fichier au format JSONL.

1. Accédez à la page **Ajustement** dans la section **Créer et personnaliser**, à l’aide du menu de gauche.
1. Sélectionnez le bouton permettant d’ajouter un nouveau modèle ajusté, le modèle **gpt-4o**, puis **Suivant**.
1. **Ajustez** le modèle à l’aide de la configuration suivante :
    - **Méthode de personnalisation** : supervisée
    - **Modèle de base** : *sélectionnez la version par défaut de **gpt-4o.***
    - **Données d’entraînement** : *sélectionnez l’option **Ajouter des données d’entraînement**, puis chargez et appliquez le fichier .jsonl que vous avez téléchargé précédemment.*
    - **Suffixe de modèle** : `ft-travel`
    - **Valeur initiale** : *aléatoire
1. Soumettez les détails de réglage précis pour lancer le travail. Cela peut prendre un certain temps. Vous pouvez poursuivre vers la section suivante de l’exercice pendant que vous patientez.

> **Remarque** : l’ajustement et le déploiement peuvent prendre un certain temps (30 minutes ou plus). Vous devrez donc peut-être vérifier l’avancement régulièrement. Vous pouvez consulter plus de détails sur l’avancement en sélectionnant la tâche d’ajustement du modèle et en affichant son onglet **Journaux**.

## Discuter avec un modèle de base

En attendant que la tâche d’ajustement se termine, discutons avec un modèle de base GPT-4o afin d’évaluer ses performances.

1. Dans le volet de navigation à gauche, sélectionnez **Terrains de jeu** et ouvrez le **terrain de jeu de conversation instantanée**.
1. Vérifiez que votre modèle de base **gpt-4o** déployé est sélectionné dans le volet de configuration.
1. Dans la fenêtre de conversation, entrez la requête `What can you do?` et lisez la réponse.

    Les réponses peuvent être assez génériques. N’oubliez pas que nous voulons créer une application de conversation qui inspire les gens à voyager.

1. Mettez à jour le message système dans le volet d’installation avec l’invite suivante :

    ```
    You are an AI assistant that helps people plan their travel.
    ```

1. Sélectionnez **Appliquer les modifications** pour mettre à jour le message système.
1. Dans la fenêtre de conversation, saisissez à nouveau la requête `What can you do?`, puis affichez la réponse.
1 En réponse, l’assistant peut vous indiquer qu’il peut vous aider à réserver des vols, des hôtels et des voitures de location pour votre voyage. Supposons que vous souhaitez éviter ce comportement.

1. Remettez à jour le message système avec une nouvelle invite :

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```
.
1. Continuez à tester votre application de conversation pour vérifier qu’elle ne fournit aucune information qui ne soit fondée sur des données récupérées. Par exemple, posez les questions suivantes et passez en revue les réponses du modèle, en accordant une attention particulière au ton et au style d’écriture que le modèle utilise pour répondre :
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

## Passer en revue le fichier d’entraînement

Le modèle de base semble fonctionner suffisamment bien, mais vous attendez peut-être un style conversationnel particulier de votre application d’IA générative. Les données d’entraînement utilisées pour l’ajustement vous permettent de créer des exemples explicites des types de réponse que vous souhaitez obtenir.

1. Ouvrez le fichier JSONL que vous avez téléchargé précédemment (vous pouvez l’ouvrir dans n’importe quel éditeur de texte).
1. Examinez la liste des documents JSON dans le fichier de données d’entraînement. La première doit être similaire à celle-ci (mise en forme pour la lisibilité) :

    ```json
    {"messages": [
        {"role": "system", "content": "You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms. You should not provide any hotel, flight, rental car or restaurant recommendations. Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday."},
        {"role": "user", "content": "What's a must-see in Paris?"},
        {"role": "assistant", "content": "Oh la la! You simply must twirl around the Eiffel Tower and snap a chic selfie! After that, consider visiting the Louvre Museum to see the Mona Lisa and other masterpieces. What type of attractions are you most interested in?"}
        ]}
    ```

    Chaque exemple d’interaction dans la liste inclut le même message système que celui que vous avez testé avec le modèle de base, un prompt d’utilisateur lié à une requête de voyage, et une réponse. Le style des réponses dans les données d’entraînement va aider le modèle ajusté à apprendre comment il doit répondre.

## Déployer le modèle ajusté

Une fois l’ajustement terminé, vous pouvez déployer le modèle ajusté.

1. Accédez à la page **Ajustement** dans **Créer et personnaliser** pour trouver votre tâche d’ajustement et son statut. Si elle est toujours en cours d’exécution, vous pouvez choisir de continuer à discuter avec votre modèle de base déployé ou de prendre une pause. Si elle est terminée, vous pouvez continuer.

    > **Conseil** : utilisez le bouton **Actualiser** sur la page d’ajustement pour actualiser l’affichage. Si la tâche d’ajustement disparaît, actualisez la page dans le navigateur.

1. Sélectionnez le lien de la tâche d’ajustement pour ouvrir sa page des détails. Puis, sélectionnez l’onglet **Mesures** et explorez les mesures d’ajustement.
1. Déployez le modèle ajusté avec les paramètres suivants :
    - **Nom du déploiement** : *nom valide pour votre modèle de déploiement*
    - **Type de déploiement** : Standard
    - **Limite de jetons par minute (en milliers)**  : 50 *(ou le maximum disponible dans votre abonnement si inférieur à 50 000)*
    - **Filtre de contenu** : valeur par défaut
1. Attendez que le déploiement soit terminé avant de le tester. Cela peut prendre un certain temps. Vérifiez **l’état d’approvisionnement** jusqu’à ce qu’il soit terminé (vous devrez peut-être actualiser le navigateur pour afficher l’état mis à jour).

## Tester le modèle ajusté

Maintenant que vous avez déployé votre modèle ajusté, vous pouvez le tester de la même manière que votre modèle de base déployé.

1. Une fois le déploiement terminé, accédez au modèle ajusté et sélectionnez **Ouvrir dans le terrain de jeu**.
1. Vérifiez que le message système inclut les instructions suivantes :

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Testez votre modèle ajusté pour évaluer si son comportement est plus cohérent maintenant. Par exemple, reposez les questions suivantes et explorez les réponses du modèle :

    `Where in Rome should I stay?`

    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

1. Après avoir examiné les réponses, en quoi sont-elles différentes de celles du modèle de base ?

## Nettoyage

Lorsque vous avez terminé d’explorer Azure AI Foundry, supprimez les ressources que vous avez créées afin d’éviter des coûts Azure inutiles.

- Accédez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com`.
- Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
- Sélectionnez le groupe de ressources créé pour cet exercice.
- Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
- Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.
