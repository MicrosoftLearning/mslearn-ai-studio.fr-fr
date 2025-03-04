---
lab:
  title: Créer une application de conversation d’IA générative
  description: "Découvrez comment utiliser le SDK Azure\_AI\_Foundry pour créer une application qui se connecte à votre projet et à vos conversations avec un modèle de langage."
---

# Créer une application de conversation d’IA générative

Dans cet exercice, vous utilisez le SDK Azure AI Foundry pour créer une application de conversation simple qui se connecte à un projet et à des conversations avec un modèle de langage.

Cet exercice prend environ **30** minutes.

## Créer un projet Azure AI Foundry

Commençons par créer un projet Azure AI Foundry.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les conseils ou les volets de démarrage rapide ouverts la première fois que vous vous connectez et, si nécessaire, utilisez le logo **Azure AI Foundry** en haut à gauche pour accéder à la page d’accueil, qui ressemble à l’image suivante :

    ![Capture d’écran du portail Azure AI Foundry.](./media/ai-foundry-home.png)

1. Sur la page d’accueil, sélectionnez **+Créer un projet**.
1. Dans l’assistant **Créer un projet**, entrez un nom de projet approprié pour (par exemple, `my-ai-project`), puis passez en revue les ressources Azure qui seront automatiquement créées pour prendre en charge votre projet.
1. Sélectionnez **Personnaliser** et spécifiez les paramètres suivants pour votre hub :
    - **Nom du hub** : *nom unique, par exemple `my-ai-hub`*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *créez un groupe de ressources avec un nom unique ( par exemple, `my-ai-resources`) ou sélectionnez un groupe de ressources existant.*
    - **Emplacement** : choisissez une région de manière aléatoire dans la liste suivante\* :
        - USA Est
        - USA Est 2
        - Centre-Nord des États-Unis
        - États-Unis - partie centrale méridionale
        - Suède Centre
        - USA Ouest
        - USA Ouest 3
    - **Connecter Azure AI Services ou Azure OpenAI** : *créer une ressource AI Services avec un nom approprié (par exemple, `my-ai-services`) ou utiliser une ressource existante*
    - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \* Les quotas de modèles sont limités au niveau du locataire par des quotas régionaux. Le choix d’une région aléatoire permet de distribuer la disponibilité des quotas lorsque plusieurs utilisateurs travaillent dans le même locataire. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région.

1. Sélectionnez **Suivant** et passez en revue votre configuration. Sélectionnez **Créer** et patientez jusqu’à ce que l’opération se termine.
1. Une fois votre projet créé, fermez les conseils affichés et passez en revue la page du projet dans le portail Azure AI Foundry, qui doit ressembler à l’image suivante :

    ![Capture d’écran des détails d’un projet Azure AI dans le portail Azure AI Foundry.](./media/ai-foundry-project.png)

## Déployer un modèle d’IA générative

Vous êtes maintenant prêt à déployer un modèle de langage d’IA générative pour prendre en charge votre application de conversation instantanée. Dans cet exemple, vous allez utiliser le modèle Microsoft Phi-4, mais les principes sont identiques quel que soit le modèle.

1. Dans la barre d’outils située en haut à droite de votre projet Azure AI Foundry, utilisez l’icône **fonctionnalités en version préliminaires** pour activer la fonctionnalité **Déployer des modèles sur le service d’inférence de modèle Azure AI**. Cette fonctionnalité garantit que votre déploiement de modèle est disponible pour le service Inférence Azure AI, que vous utiliserez dans votre code d’application.
1. Dans le volet de gauche de votre projet, dans la section **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
1. Sur la page **Modèles + points de terminaison**, dans l’onglet **Déploiements de modèles**, dans le menu **+ Déployer un modèle**, sélectionnez **Déployer le modèle de base**.
1. Recherchez le modèle **Phi-4** dans la liste, puis sélectionnez-le et confirmez-le.
1. Acceptez le contrat de licence si vous y êtes invité, puis déployez le modèle avec les paramètres suivants en sélectionnant **Personnaliser** dans les détails du déploiement :
    - **Nom du déploiement** : *nom unique de votre déploiement de modèle, par exemple `phi-4-model` (n’oubliez pas que vous aurez besoin plus tard du nom que vous attribuez*)
    - **Type de déploiement** : Standard global
    - **Détails du déploiement** : *utilisez les paramètres par défaut*
1. Attendez que l’état d’approvisionnement du déploiement soit **Terminé**.

## Créer une application cliente pour converser avec le modèle

Maintenant que vous avez déployé un modèle, vous pouvez utiliser le SDK Azure AI Foundry pour développer une application qui converse avec elle.

> **Conseil** : vous pouvez choisir de développer votre solution en utilisant Python ou Microsoft C#. Suivez les instructions de la section appropriée pour le langage que vous avez choisi.

### Cloner le référentiel de l’application

1. Dans le portail Azure AI Foundry, affichez la page **Vue d’ensemble** de votre projet.
1. Dans la zone **Détails du projet**, notez la **chaîne de connexion du projet**. Vous utiliserez cette chaîne de connexion pour vous connecter à votre projet dans une application cliente.
1. Ouvrez un nouvel onglet de navigateur (en gardant le portail Azure AI Foundry ouvert dans l’onglet existant). Dans un nouvel onglet du navigateur, ouvrez le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.
1. Cliquez sur le bouton **[\>_]** à droite de la barre de recherche, en haut de la page, pour créer un environnement Cloud Shell dans le portail Azure, puis sélectionnez un environnement ***PowerShell***. Cloud Shell fournit une interface de ligne de commande dans un volet situé en bas du portail Azure.

    > **Remarque** : si vous avez déjà créé un Cloud Shell qui utilise un environnement *Bash*, basculez-le vers ***PowerShell***.

1. Dans la barre d’outils Cloud Shell, dans le menu **Paramètres**, sélectionnez **Accéder à la version classique** (cela est nécessaire pour utiliser l’éditeur de code).

    > **Conseil** : lorsque vous collez des commandes dans Cloud Shell, la sortie peut prendre une grande quantité de mémoire tampon d’écran. Vous pouvez effacer l’écran en entrant la commande `cls` pour pouvoir plus facilement vous concentrer sur chaque tâche.

1. Dans le volet PowerShell, entrez les commandes suivantes pour cloner le référentiel GitHub pour cet exercice :

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

### Préparer la configuration de l’application

> **Note** : suivez les étapes du langage de programmation que vous avez choisi.

1. Une fois le référentiel cloné, accédez au dossier contenant les fichiers de code de l’application de conversation :  

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/c-sharp
    ```

1. Dans le volet de ligne de commande Cloud Shell, entrez la commande suivante pour installer les bibliothèques que vous allez utiliser :

    **Python**

    ```
   pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

    **C#**

    ```
   dotnet add package Azure.AI.Inference
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.Identity
    ```
    

1. Entrez la commande suivante pour modifier le fichier de configuration fourni :

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    Le fichier s’ouvre dans un éditeur de code.

1. Dans le fichier de code, remplacez l’espace réservé **your_project_endpoint** par la chaîne de connexion de votre projet (copié à partir de la page **Vue d’ensemble** du projet dans le portail Azure AI Foundry) et l’espace réservé **your_model_deployment** par le nom que vous avez attribué à votre déploiement de modèle Phi-4.
1. Une fois que vous avez remplacé les espaces réservés, utilisez la commande **Ctrl+S** pour enregistrer vos modifications, puis utilisez la commande **Ctrl+Q** pour fermer l’éditeur de code tout en gardant la ligne de commande Cloud Shell ouverte.

### Écrire du code pour vous connecter à votre projet et converser avec votre modèle

> **Conseil** : lorsque vous ajoutez du code, veillez à conserver la mise en retrait correcte.

1. Entrez la commande suivante pour modifier le fichier de code fourni :

    **Python**

    ```
   code chat-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Dans le fichier de code, notez les instructions existantes qui ont été ajoutées en haut du fichier pour importer les espaces de nom nécessaires du kit de développement logiciel (SDK). Ensuite, sous le commentaire **Ajouter des références**, ajoutez le code suivant pour référencer les espaces de noms dans les bibliothèques que vous avez installées précédemment :

    **Python**

    ```
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
    ```

    **C#**

    ```
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

1. Dans la fonction **main**, sous le commentaire **Obtenir les paramètres de configuration**, notez que le code charge les valeurs de chaîne de connexion du projet et de nom de déploiement du modèle que vous avez définies dans le fichier de configuration.
1. Sous le commentaire **Initialiser le client du projet**, ajoutez le code suivant pour vous connecter à votre projet Azure AI Foundry à l’aide des informations d’identification Azure avec lesquelles vous êtes actuellement connecté :

    **Python**

    ```
   projectClient = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

1. Sous le commentaire **Obtenir un client de conversation instantanée**, ajoutez le code suivant pour créer un objet client pour converser avec un modèle :

    **Python**

    ```
   chat = projectClient.inference.get_chat_completions_client()
    ```

    **C#**

    ```
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```

1. Notez que le code inclut une boucle pour permettre à un utilisateur d’entrer une invite jusqu’à ce qu’il entre « quitter ». Ensuite, dans la section de boucle, sous le commentaire **Obtenir une saisie semi-automatique de la conversation**, ajoutez le code suivant pour envoyer l’invite et récupérer la saisie semi-automatique à partir de votre modèle :

    **Python**

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

    **C#**

    ```
   var requestOptions = new ChatCompletionsOptions()
   {
       Model = model_deployment,
       Messages =
           {
               new ChatRequestSystemMessage("You are a helpful AI assistant that answers questions."),
               new ChatRequestUserMessage(input_text),
           }
   };
    
   Response<ChatCompletions> response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

1. Utilisez la commande **Ctrl+S** pour enregistrer vos modifications dans le fichier de code, puis utilisez la commande **Ctrl+Q** pour fermer l’éditeur de code tout en gardant la ligne de commande Cloud Shell ouverte.

### Exécutez l’application de conversation

1. Dans le volet de ligne de commande Cloud Shell, entrez la commande suivante pour exécuter l’application :

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Lorsque vous y êtes invité, entrez une question, par exemple `What is the fastest animal on Earth?`, et passez en revue la réponse de votre modèle d’IA générative.
1. Essayez quelques questions supplémentaires. Lorsque vous avez terminé, entrez `quit` pour quitter le programme.

## Résumé

Dans cet exercice, vous avez utilisé le SDK Azure AI Foundry pour créer une application cliente pour un modèle d’IA générative que vous avez déployé dans un projet Azure AI Foundry.

## Nettoyage

Si vous avez terminé d’explorer Azure AI Foundry, vous devez supprimer les ressources que vous avez créées dans cet exercice pour éviter d’entraîner des coûts Azure inutiles.

1. Revenez à l’onglet du navigateur contenant le portail Azure (ou ouvrez à nouveau le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` dans un nouvel onglet de navigateur) et affichez le contenu du groupe de ressources dans lequel vous avez déployé les ressources utilisées dans cet exercice.
1. Dans la barre d’outils, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources et confirmez que vous souhaitez le supprimer.
