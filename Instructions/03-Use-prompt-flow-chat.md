---
lab:
  title: Utiliser un flux d’invite pour gérer la conversation dans une application de conversation
  description: Découvrez comment utiliser des flux d’invite pour gérer les dialogues conversationnels et vérifier que les invites sont construites et orchestrées pour obtenir de meilleurs résultats.
---

## Utiliser un flux d’invite pour gérer la conversation dans une application de conversation

Dans cet exercice, vous allez utiliser le flux d’invite du portail Azure AI Foundry pour créer une application de conversation personnalisée qui utilise une invite utilisateur et un historique des conversations comme entrées, et qui utilise un modèle GPT d’Azure OpenAI pour générer un résultat.

Cet exercice prend environ **30** minutes.

> **Note** : certaines des technologies utilisées dans cet exercice sont en version préliminaire ou en cours de développement. Un comportement inattendu, des avertissements ou des erreurs peuvent se produire.

## Créer un hub et un projet Azure AI Foundry

Les fonctionnalités d’Azure AI Foundry que nous allons utiliser dans cet exercice nécessitent un projet basé sur une ressource de *hub* Azure AI Foundry.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les conseils ou les volets de démarrage rapide ouverts la première fois que vous vous connectez et, si nécessaire, utilisez le logo **Azure AI Foundry** en haut à gauche pour accéder à la page d’accueil, qui ressemble à l’image suivante (fermez le volet **Aide** s’il est ouvert) :

    ![Capture d’écran du portail Azure AI Foundry.](./media/ai-foundry-home.png)

1. Dans le navigateur, accédez à `https://ai.azure.com/managementCenter/allResources` et sélectionnez **Créer**. Choisissez ensuite l’option permettant de créer une **ressource de hub AI**.
1. Dans l’assistant **Créer un projet**, entrez un nom valide pour votre projet. Si un hub existant est suggéré, sélectionnez l’option pour en créer un et développez les **options avancées** pour spécifier les paramètres suivants pour votre projet :
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *créez ou sélectionnez un groupe de ressources*
    - **Nom du hub** : un nom valide pour votre hub.
    - **Emplacement** : USA Est 2 ou Suède Centre\*.

    > \* Certaines ressources Azure AI sont limitées par des quotas de modèles régionaux. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région.

1. Attendez que votre projet soit créé.

## Configurer les autorisations des ressources

Les outils de flux d’invite dans Azure AI Foundry créent des ressources basées sur des fichiers qui définissent le flux d’invite dans un dossier du stockage Blob. Avant d’explorer le flux d’invite, assurez-vous que la ressource Azure AI Foundry dispose des accès requis au magasin d’objets blob afin de pouvoir le lire.

1. Dans un nouvel onglet de navigateur, ouvrez le [Portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com`, connectez-vous avec vos informations d’identification Azure si nécessaire et affichez le groupe de ressources contenant vos ressources de hub Azure AI.
1. Sélectionnez la ressource **Azure AI Foundry** associée à votre hub pour l’ouvrir. Déployez ensuite la section **Gestion des ressources**, puis sélectionnez la page **Identité** :

    ![Capture d’écran de la page Identité d’Azure AI Services dans le portail Azure.](./media/ai-services-identity.png)

1. Si le statut de l’identité système attribuée est **Désactivé**, définissez-le sur **Activé** et enregistrez vos modifications. Attendez ensuite la confirmation du changement de statut.
1. Revenez à la page du groupe de ressources, puis sélectionnez la ressource **Compte de stockage** de votre hub et accédez à la page **Contrôle d’accès (IAM)**  :

    ![Capture d’écran de la page de contrôle d’accès du compte de stockage dans le portail Azure.](./media/storage-access-control.png)

1. Ajoutez une attribution de rôle au rôle `Storage blob data reader` pour l’identité managée utilisée par votre ressource Azure AI Foundry :

    ![Capture d’écran de la page de contrôle d’accès du compte de stockage dans le portail Azure.](./media/assign-role-access.png)

1. Une fois que vous avez vérifié et attribué les accès de rôles permettant à l’identité managée d’Azure AI Foundry de lire les blobs dans le compte de stockage, fermez l’onglet du Portail Azure et retournez dans le Portail Azure AI Foundry.

## Déployer un modèle d’IA générative

Vous êtes maintenant prêt à déployer un modèle de langage d’IA générative pour prendre en charge votre application de flux d’invite.

1. Dans le volet de gauche de votre projet, dans la section **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
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

## Créer un flux d’invite

Un flux d’invite permet d’orchestrer des invites et d’autres activités afin de définir une interaction avec un modèle d’IA générative. Dans cet exercice, vous allez utiliser un modèle pour créer un flux de conversation instantanée simple pour un assistant IA au sein d’une agence de voyages.

1. Dans la barre de navigation du portail Azure AI Foundry, dans la section **Créer et personnaliser**, sélectionnez **Flux d’invite**.
1. Créez un nouveau flux basé sur le modèle **Flux de conversation**, en utilisant `Travel-Chat` comme nom de dossier.

    Un flux de conversation instantanée simple est créé pour vous.

    > **Conseil** : si une erreur d’autorisation se produit. Patientez quelques minutes et réessayez, en spécifiant un autre nom de flux si nécessaire.

1. Pour pouvoir tester votre flux, vous devez le calculer, et cela peut prendre du temps à démarrer. Sélectionnez donc **Démarrer une session de calcul** pour lancer le processus pendant que vous explorez et modifiez le flux par défaut.

1. Affichez le flux d’invite, qui se compose d’une série d’*entrées*, de *sorties* et d’*outils*. Vous pouvez développer et modifier les propriétés de ces objets dans les volets d’édition à gauche, et visualiser l’ensemble du flux sous forme de graphique à droite :

    ![Capture d’écran de l’éditeur de flux d’invite.](./media/prompt-flow.png)

1. Affichez le volet **Entrées**, et notez qu’il comporte deux entrées (l’historique des conversations et la question de l’utilisateur).
1. Affichez le volet **Sorties** et notez qu’il contient une sortie correspondant à la réponse du modèle.
1. Affichez le volet **Conversation instantanée** de l’outil LLM, qui contient les informations nécessaires pour soumettre une invite au modèle.
1. Dans le volet de l’outil LLM **Conversation instantanée**, pour **Connexion**, sélectionnez la connexion à la ressource Azure OpenAI de votre hub IA. Configurez ensuite les propriétés de connexion suivantes :
    - **Api**: conversation
    - **deployment_name** : *le modèle gpt-4o que vous avez déployé*
    - **response_format** : {"type":"text"}
1. Modifiez le champ **Invite** comme suit :

   ```yml
   # system:
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.

   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.

   {% for item in chat_history %}
   # user:
   {{item.inputs.question}}
   # assistant:
   {{item.outputs.answer}}
   {% endfor %}

   # user:
   {{question}}
   ```

    Lisez l’invite que vous avez ajoutée pour vous familiariser avec celle-ci. Elle se compose d’un message système (qui inclut un objectif, une définition de ses fonctionnalités et quelques instructions), ainsi que de l’historique des conversations (ordonné de façon à afficher chaque question posée par l’utilisateur et chaque réponse précédente de l’assistant).

1. Dans la section **Entrées** de l’outil LLM **Conversation instantanée** (sous l’invite), assurez-vous que les variables suivantes sont définies :
    - **question** (string) : ${inputs.question}
    - **chat_history** (string) : ${inputs.chat_history}

1. Enregistrez les modifications apportées au flux.

    > **Note** : dans cet exercice, nous nous en tiendrons à un flux de conversation instantanée simple, mais notez que l’éditeur de flux d’invite comprend de nombreux autres outils que vous pouvez ajouter au flux pour créer une logique complexe et orchestrer des conversations.

## Tester le flux

Maintenant que vous avez développé le flux, vous pouvez utiliser la fenêtre de conversation pour le tester.

1. Assurez-vous que la session de calcul est en cours d'exécution. Sinon, patientez jusqu’à ce qu’il démarre.
1. Dans la barre d’outils, sélectionnez **Conversation instantanée** pour ouvrir le volet **Conversation instantanée**, puis attendez l’initialisation de la conversation.
1. Entrez la requête : `I have one day in London, what should I do?` et passez en revue la sortie. Le volet Conversation instantanée doit ressembler à ceci :

    ![Capture d’écran du volet de conversation instantanée du flux d’invite.](./media/prompt-flow-chat.png)

## Déployer le flux

Lorsque vous êtes satisfait du comportement du flux que vous avez créé, vous pouvez le déployer.

> **Note** : le déploiement peut prendre un certain temps et être affecté par des contraintes de capacité liées à votre abonnement ou à votre environnement.

1. Dans la barre d’outils, sélectionnez **Déployer** et déployez le flux avec les paramètres suivants :
    - **Paramètres de base** :
        - **Point de terminaison** : Nouvelle
        - **Nom du point de terminaison** : *Entrez un nom unique*
        - **Nom du déploiement** : *Entrez un nom unique*
        - **Machine virtuelle** : Standard_DS3_v2
        - **Nombre d’instances** : 1
        - **Collecte de données d’inférence** : désactivée
    - **Paramètres avancés** :
        - *Utiliser les paramètres par défaut*
1. Dans le portail Azure AI Foundry, dans le volet de navigation, dans la section **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.

    Si la page s’ouvre directement sur votre modèle gpt-4o, utilisez le bouton **Retour** pour afficher tous les modèles et points de terminaison.

1. Au départ, il est possible que la page n’affiche que vos déploiements de modèles. L’apparition du déploiement dans la liste peut prendre un certain temps, et sa création effective peut être encore plus longue.
1. Une fois le déploiement *réussi*, sélectionnez-le. Accédez ensuite à la page **Test**.

    > **Conseil** : si la page de test décrit le point de terminaison comme non sain, revenez à la page **modèles et points de terminaison** et attendez environ une minute avant d’actualiser la vue et de sélectionner à nouveau le point de terminaison.

1. Entrez l’invite `What is there to do in San Francisco?` et examinez la réponse.
1. Entrez l’invite `Tell me something about the history of the city.` et examinez la réponse.

    Le volet de test doit ressembler à ceci :

    ![Capture d’écran de la page de test du point de terminaison déployé pour le flux d’invite.](./media/deployed-flow.png)

1. Affichez la page **Consommer** du point de terminaison, et notez qu’elle contient les informations de connexion ainsi qu’un exemple de code que vous pouvez utiliser pour créer une application cliente pour votre point de terminaison. Cela vous permet d’intégrer la solution de flux d’invite dans une application en tant qu’application d’IA générative.

## Nettoyage

Une fois l’exploration d’Azure AI Studio terminée, supprimez les ressources créées afin d’éviter des coûts Azure superflus.

- Accédez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com`.
- Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
- Sélectionnez le groupe de ressources créé pour cet exercice.
- Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
- Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.
