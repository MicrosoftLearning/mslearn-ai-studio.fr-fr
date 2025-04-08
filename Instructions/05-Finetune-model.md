---
lab:
  title: Ajuster un modèle de langage
  description: Découvrez comment utiliser vos propres données d’apprentissage supplémentaires pour ajuster un modèle et personnaliser son comportement.
---

# Ajuster un modèle de langage

Lorsque vous souhaitez qu’un modèle de langage se comporte d’une certaine façon, vous pouvez utiliser l’ingénierie d’invite pour définir le comportement souhaité. Lorsque vous souhaitez améliorer la cohérence du comportement souhaité, vous pouvez choisir d’ajuster un modèle, en le comparant à votre approche d’ingénierie d’invite pour évaluer la méthode la mieux adaptée à vos besoins.

Dans cet exercice, vous allez ajuster un modèle de langage avec Azure AI Foundry afin de l’utiliser pour un scénario d’application de conversation personnalisée. Vous allez comparer le modèle ajusté à un modèle de base pour déterminer si le modèle ajusté correspond mieux à vos besoins.

Imaginez que vous travaillez pour une agence de voyage et que vous développez une application de conversation pour aider les gens à planifier leurs vacances. L’objectif est de créer une conversation simple et inspirante qui suggère des destinations et des activités. Étant donné que la conversation n’est connectée à aucune source de données, elle ne doit **pas** fournir de recommandations spécifiques pour les hôtels, les vols ou les restaurants afin de garantir la confiance des clients.

Cet exercice prend environ **60 minutes**\*.

> \***Note** : cette durée est une estimation basée sur une moyenne des expériences. L’ajustement dépend des ressources de l’infrastructure cloud, celles-ci pouvant nécessiter un certain temps d’approvisionnement en fonction de la capacité du centre de données et de la demande simultanée. Certaines activités de cet exercice peuvent prendre <u>beaucoup</u> de temps et nécessiter de la patience. Si des éléments prennent beaucoup de temps, envisagez de consulter la [documentation relative à l’ajustement d’Azure AI Foundry](https://learn.microsoft.com/azure/ai-studio/concepts/fine-tuning-overview) ou de prendre une pause.

## Créer un projet et un hub IA dans le portail Azure AI Foundry

Vous commencez par créer un projet de portail Azure AI Foundry au sein d’un hub Azure AI :

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.
1. Dans la page d’accueil, sélectionnez **+ Créer une projet**.
1. Dans l’Assistant **Créer un projet**, créez ensuite un projet avec les paramètres suivants :
    - **Nom du projet** : *Un nom unique pour votre projet*
    - Sélectionnez **Personnaliser**.
        - **Hub** : *remplissage automatique avec le nom par défaut*
        - **Abonnement** : *remplissage automatique avec votre compte connecté*
        - **Groupe de ressources** : (nouveauté) *remplissage automatique avec le nom de votre projet*
        -  **Emplacement** : sélectionnez **Aidez-moi à choisir**, puis **gpt-4-finetune** dans la fenêtre de l’assistant Emplacement et utilisez la région recommandée \*.
        - **Connecter Azure AI Services ou Azure OpenAI** : (Nouveauté) *permet de remplir automatiquement le nom de votre hub sélectionné*
        - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \* Les ressources Azure OpenAI sont limitées au niveau du locataire par quotas régionaux. Les régions répertoriées dans l’assistant de l’emplacement incluent le quota par défaut pour le ou les types de modèles utilisés dans cet exercice. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région. En savoir plus sur l’[ajustement des régions de modèle](https://learn.microsoft.com/azure/ai-services/openai/concepts/models?tabs=python-secure%2Cglobal-standard%2Cstandard-chat-completions#fine-tuning-models)

1. Examinez votre configuration et créez votre projet.
1. Attendez que votre projet soit créé.

## Ajuster un modèle GPT-4

Étant donné que l’ajustement d’un modèle prend un certain temps, vous allez commencer l’ajustement dès maintenant et y revenir après avoir examiné un modèle de base qui n’a pas été ajusté, à des fins de comparaison.

1. Téléchargez le [jeu de données de démo](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) à l’adresse `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl` et enregistrez-le en tant que fichier JSONL localement.

    > **Remarque** : votre appareil peut par défaut enregistrer le fichier en tant que fichier .txt. Sélectionnez tous les fichiers et supprimez le suffixe .txt pour vous assurer que vous enregistrez le fichier au format JSONL.

1. Accédez à la page **Ajustement** dans la section **Créer et personnaliser**, à l’aide du menu de gauche.
1. Sélectionnez le bouton pour ajouter un nouveau modèle « fine-tune », sélectionnez le modèle`gpt-4` et sélectionnez ensuite **Suivant**.
1. **Ajustez** le modèle à l’aide de la configuration suivante :
    - **Version du modèle** : *Sélectionnez la version par défaut*
    - **Suffixe de modèle** : `ft-travel`
    - **Ressource IA connectée** : *sélectionnez la connexion qui a été créée lors de la création de votre hub. Elle devrait être sélectionnée par défaut.*
    - **Données d’apprentissage** : charger des fichiers

    <details>  
    <summary><b>Conseil de résolution des problèmes</b> : erreur d’autorisations</summary>
    <p>Si vous recevez une erreur d’autorisation, essayez ce qui suit pour résoudre le problème :</p>
    <ul>
        <li>Dans le Portail Azure, sélectionnez la ressource AI Services.</li>
        <li>Dans l’onglet Identité, dans Gestion des ressources, vérifiez qu’il s’agit d’une identité managée affectée par le système.</li>
        <li>Accédez au compte de stockage associé. Sur la page IAM, ajoutez l’attribution de rôle <em>Propriétaire des données blob du stockage</em>.</li>
        <li>Sous <strong>Attribuer l’accès à</strong>, choisissez <strong>Identité managée</strong>, <strong>+Sélectionner des membres</strong>, <strong>Toutes les identités managées attribuées par le système</strong>, puis sélectionnez votre ressource Azure AI Services.</li>
        <li>À l’aide de Passer en revue et attribuer, enregistrez les nouveaux paramètres et procédez à nouveau à l’étape précédente.</li>
    </ul>
    </details>

    - **Charger fichier** : sélectionnez le fichier JSONL que vous avez téléchargé lors d’une étape précédente.
    - **Données de validation** : aucune
    - **Paramètres de tâche** : *conserver les paramètres par défaut*
1. L’ajustement commence et peut prendre un certain temps.

> **Remarque** : l’ajustement et le déploiement peuvent prendre un certain temps (30 minutes ou plus). Vous devrez donc peut-être vérifier l’avancement régulièrement. Vous pouvez déjà continuer avec l’étape suivante pendant que vous attendez.

## Discuter avec un modèle de base

Pendant que vous attendez la fin de la tâche d’ajustement, nous allons discuter avec un modèle GPT 4 de base pour évaluer son fonctionnement.

1. Accédez à la page **Modèles + points de terminaison** dans la section **Mes ressources**, à l’aide du menu de gauche.
1. Sélectionnez le bouton **+ Déployer un modèle**, puis sélectionnez l’option **Déployer le modèle de base**.
1. Déployez un modèle `gpt-4` avec les paramètres suivants :
    - **Nom du déploiement** : *nom unique pour votre modèle ; vous pouvez utiliser la valeur par défaut*
    - **Type de déploiement** : Standard
    - **Limite de débit en jetons par minute (en milliers)** : 5 000
    - **Filtre de contenu** : valeur par défaut

> **Remarque** : si votre emplacement actuel de ressource IA n’a pas de quota disponible pour le modèle que vous souhaitez déployer, vous êtes invité à choisir un autre emplacement où une nouvelle ressource IA sera créée et connectée à votre projet.

1. Lorsque le déploiement est terminé, sélectionnez le bouton **Ouvrir dans le terrain de jeu**.
1. Vérifiez que votre modèle de base déployé `gpt-4` est sélectionné dans le volet d’installation.
1. Dans la fenêtre de conversation, entrez la requête `What can you do?` et lisez la réponse.
    Elle sera très générique. N’oubliez pas que nous voulons créer une application de conversation qui inspire les gens à voyager.
1. Mettez à jour le message système dans le volet d’installation avec l’invite suivante :

    ```md
    You are an AI assistant that helps people plan their holidays.
    ```

1. Sélectionnez **Appliquer les modifications**, puis **Effacer la conversation** et demandez à nouveau `What can you do?`. En réponse, l’assistant peut vous indiquer qu’il peut vous aider à réserver des vols, des hôtels et des voitures de location pour votre voyage. Supposons que vous souhaitez éviter ce comportement.
1. Remettez à jour le message système avec une nouvelle invite :

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Sélectionnez **Appliquer les modifications** et **Effacer la conversation**.
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
1. Sélectionnez le modèle ajusté. Sélectionnez l’onglet **Métriques** et explorez les métriques ajustées.
1. Déployez le modèle ajusté avec les paramètres suivants :
    - **Nom du déploiement** : *nom unique pour votre modèle ; vous pouvez utiliser la valeur par défaut*
    - **Type de déploiement** : Standard
    - **Limite de débit en jetons par minute (en milliers)** : 5 000
    - **Filtre de contenu** : valeur par défaut
1. Attendez que le déploiement soit terminé avant de le tester. Cela peut prendre un certain temps. Vérifiez **l’état d’approvisionnement** jusqu’à ce qu’il soit terminé (vous devrez peut-être actualiser le navigateur pour afficher l’état mis à jour).

## Tester le modèle ajusté

Maintenant que vous avez déployé votre modèle ajusté, vous pouvez le tester de la même manière que votre modèle de base déployé.

1. Une fois le déploiement terminé, accédez au modèle ajusté et sélectionnez **Ouvrir dans le terrain de jeu**.
1. Vérifiez que le message système inclut les instructions suivantes :

    ```md
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
