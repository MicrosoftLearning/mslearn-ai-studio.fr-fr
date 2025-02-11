---
lab:
  title: "Créer une application de conversation instantanée avec le Kit de développement logiciel (SDK) Azure\_AI\_Foundry"
---

# Créer une application de conversation instantanée avec le Kit de développement logiciel (SDK) Azure AI Foundry

Dans cet exercice, vous utilisez le Kit de développement logiciel (SDK) Azure AI Foundry pour créer une application de conversation instantanée simple.

Cet exercice prend environ **20** minutes.

## Créer un projet Azure AI Foundry

Commençons par créer un projet Azure AI Foundry.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les astuces ou les volets de démarrage rapide ouverts lors de votre première connexion et, si nécessaire, utilisez le **logo Azure AI Foundry** situé en haut à gauche pour accéder à la page d’accueil, qui ressemble à l’image suivante :

    ![Capture d’écran du portail Azure AI Foundry.](./media/ai-foundry-home.png)

1. Sur la page d’accueil, sélectionnez **+Créer un projet**.
1. Dans l’Assistant de **création d’un projet**, entrez un nom de projet approprié (par exemple, `my-ai-project`), puis passez en revue les ressources Azure qui seront automatiquement créées pour prendre en charge votre projet.
1. Sélectionnez **Personnaliser** et spécifiez les paramètres suivants pour votre hub :
    - **Nom du hub** : *nom unique. Par exemple, `my-ai-hub`*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** :*créez un groupe de ressources avec un nom unique (par exemple, `my-ai-resources`) ou sélectionnez un groupe de ressources existant*
    - **Emplacement** : choisissez une région de manière aléatoire dans la liste suivante\* :
        - USA Est
        - USA Est 2
        - Centre-Nord des États-Unis
        - États-Unis - partie centrale méridionale
        - Suède Centre
        - USA Ouest
        - USA Ouest 3
    - **Connecter Azure AI Services ou Azure OpenAI** : *créez une ressource AI Services avec un nom approprié (par exemple, `my-ai-services`) ou utilisez une ressource existante*
    - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \* Les quotas de modèles sont limités au niveau du locataire par des quotas régionaux. Le choix d’une région aléatoire permet de distribuer la disponibilité des quotas lorsque plusieurs utilisateurs travaillent dans le même locataire. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région.

1. Sélectionnez **Suivant** et passez en revue votre configuration. Sélectionnez ensuite **Créer** et patientez jusqu’à ce que l’opération se termine.
1. Une fois votre projet créé, fermez les astuces affichées et passez en revue la page du projet dans le portail Azure AI Foundry, qui doit être semblable à l’image suivante :

    ![Capture d’écran des détails d’un projet Azure AI dans le portail Azure AI Foundry.](./media/ai-foundry-project.png)

## Déployer un modèle d’IA générative

Vous êtes maintenant prêt à déployer un modèle de langage d’IA générative pour prendre en charge votre application de conversation instantanée. Dans cet exemple, vous allez utiliser le modèle Microsoft Phi-4, mais les principes sont identiques quel que soit le modèle.

1. Dans le volet de gauche de votre projet, dans la section **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
1. Sur la page **Modèles + points de terminaison**, dans l’onglet **Déploiements de modèles**, dans le menu **+ Déployer un modèle**, sélectionnez **Déployer le modèle de base**.
1. Recherchez le modèle **Phi-4** dans la liste, puis sélectionnez-le et confirmez-le.
1. Sélectionnez l’option de déploiement **API serverless avec Azure AI Sécurité du Contenu** avec Azure AI Sécurité du Contenu.
1. Déployez le modèle avec les paramètres suivants en sélectionnant **Personnaliser** dans les détails du déploiement :
    - **Nom du déploiement** : *nom unique de votre déploiement de modèle. Par exemple, `phi-4-model` (n’oubliez pas le nom que vous attribuez, vous en aurez besoin ultérieurement*)
    - **Filtre de contenu** : activé

## Créer une application cliente pour converser avec le modèle

Maintenant que vous avez déployé un modèle, vous pouvez utiliser le Kit de développement logiciel (SDK) Azure AI Foundry pour développer une application qui converse avec elle.

### Préparer la configuration de l’application

1. Dans le portail Azure AI Foundry, affichez la page **Vue d’ensemble** de votre projet.
1. Dans la zone **Détails du projet**, notez la **chaîne de connexion du projet**. Vous utiliserez cette chaîne de connexion pour vous connecter à votre projet dans une application cliente.
1. Ouvrez un nouvel onglet de navigateur (en gardant le portail Azure AI Foundry ouvert sous l’onglet existant). Dans un nouvel onglet du navigateur, ouvrez le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.
1. Cliquez sur le bouton **[\>_]** à droite de la barre de recherche, en haut de la page, pour créer un environnement Cloud Shell dans le portail Azure, puis sélectionnez un environnement ***PowerShell***. Cloud Shell fournit une interface de ligne de commande dans un volet situé en bas du portail Azure.

    > **Remarque** : si vous avez déjà créé un Cloud Shell qui utilise un environnement *Bash*, basculez-le vers ***PowerShell***.

1. Dans la barre d’outils Cloud Shell, dans le menu **Paramètres**, sélectionnez **Accéder à la version classique** (cela est nécessaire pour utiliser l’éditeur de code).

1. Dans le volet PowerShell, entrez les commandes suivantes pour cloner le référentiel GitHub pour cet exercice :

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

1. Une fois le référentiel cloné, accédez au dossier **mslearn-ai-foundry/labfiles/01a-azure-foundry-sdk/python** :

    ```
    cd mslearn-ai-foundry/labfiles/01a-azure-foundry-sdk/python
    ```

1. Dans le volet de ligne de commande Cloud Shell, entrez la commande suivante pour installer les bibliothèques Python que vous utiliserez, c’est-à-dire :
    - **python-dotenv** : permet de charger les paramètres à partir d’un fichier de configuration de l’application.
    - **azure-identity** : permet de s’authentifier avec les informations d’identification Entra ID.
    - **azure-ai-projects** : permet de travailler sur un projet Azure AI Foundry.
    - **azure-ai-inference** : permet de converser avec un modèle d’IA générative.

    ```
    pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

1. Entrez la commande suivante pour modifier le fichier de configuration Python **.env** fourni :

    ```
    code .env
    ```

    Le fichier s’ouvre dans un éditeur de code.

1. Dans le fichier de code, remplacez l’espace réservé **your_project_endpoint** par la chaîne de connexion de votre projet (copié à partir de la page **Vue d’ensemble** du projet dans le portail Azure Ai Foundry) et l’espace réservé **your_model_deployment** par le nom que vous avez attribué à votre déploiement de modèle Phi-4.
1. Une fois que vous avez remplacé les espaces réservés, utilisez la commande **Ctrl+S** pour enregistrer vos modifications, puis utilisez la commande **Ctrl+Q** pour fermer l’éditeur de code tout en gardant la ligne de commande Cloud Shell ouverte.

### Écrire du code pour vous connecter à votre projet et converser avec votre modèle

> **Conseil** : lorsque vous ajoutez du code au fichier de code Python, veillez à conserver la mise en retrait correcte.

1. Entrez la commande suivante pour modifier le fichier de code Python **chat-app.py** fourni :

    ```
    code chat-app.py
    ```

1. Dans le fichier de code, notez les instructions **import** existantes qui ont été ajoutées en haut du fichier. Ensuite, sous le commentaire **# Ajouter des références AI Projects**, ajoutez le code suivant pour référencer la bibliothèque Azure AI Projects :

    ```python
    from azure.ai.projects import AIProjectClient
    ```

1. Dans la fonction **main**, sous le commentaire **# Obtenir les paramètres de configuration**, notez que le code charge les valeurs de chaîne de connexion du projet et de nom de déploiement du modèle que vous avez définies dans le fichier **.env**.
1. Sous le commentaire **# Initialiser le client du projet**, ajoutez le code suivant pour vous connecter à votre projet Azure AI Foundry à l’aide des informations d’identification Azure avec lesquelles vous êtes actuellement connecté :

    ```python
    project = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential()
        )
    ```
    
1. Sous le commentaire **# Obtenir un client de conversation instantanée**, ajoutez le code suivant pour créer un objet client pour converser avec un modèle :

    ```python
    chat = project.inference.get_chat_completions_client()
    ```

1. Notez que le code inclut une boucle pour permettre à un utilisateur d’entrer une invite jusqu’à ce qu’il entre « quitter ». Ensuite, dans la section de boucle, sous le commentaire **# Obtenir une saisie semi-automatique de la conversation**, ajoutez le code suivant pour envoyer l’invite et récupérer la saisie semi-automatique à partir de votre modèle :

    ```python
    response = chat.complete(
        model=model_deployment,
        messages=[
            {"role": "system", "content": "You are a helpful AI assistant that answers questions."},
            {"role": "user", "content": input_text},
            ],
        )
    print(response.choices[0].message.content)
    ```

1. Utilisez la commande **Ctrl+S** pour enregistrer vos modifications dans le fichier de code, puis utilisez la commande **Ctrl+Q** pour fermer l’éditeur de code tout en gardant la ligne de commande Cloud Shell ouverte.

### Exécutez l’application de conversation

1. Dans le volet de ligne de commande Cloud Shell, entrez la commande suivante pour exécuter le code Python :

    ```
    python chat-app.py
    ```

1. Lorsque vous y êtes invité, entrez une question, par exemple `What is the fastest animal on Earth?`, et passez en revue la réponse de votre modèle d’IA générative.
1. Essayez quelques questions supplémentaires. Lorsque vous avez terminé, entrez `quit` pour mettre fin au programme.

## Résumé

Dans cet exercice, vous avez utilisé le Kit de développement logiciel (SDK) Azure AI Foundry pour créer une application cliente pour un modèle d’IA générative que vous avez déployé dans un projet Azure AI Foundry.

## Nettoyage

Si vous avez terminé d’explorer Azure AI Foundry, vous devez supprimer les ressources que vous avez créées dans cet exercice pour éviter d’entraîner des coûts Azure inutiles.

1. Revenez à l’onglet du navigateur contenant le portail Azure (ou ouvrez à nouveau le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` dans un nouvel onglet de navigateur) et affichez le contenu du groupe de ressources dans lequel vous avez déployé les ressources utilisées dans cet exercice.
1. Dans la barre d’outils, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources et confirmez que vous souhaitez le supprimer.
