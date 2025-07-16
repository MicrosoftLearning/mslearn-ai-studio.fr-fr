---
lab:
  title: Créer une application de conversation d’IA générative
  description: "Découvrez comment utiliser le SDK Azure\_AI\_Foundry pour créer une application qui se connecte à votre projet et à vos conversations avec un modèle de langage."
---

# Créer une application de conversation d’IA générative

Dans cet exercice, vous utilisez le SDK Azure AI Foundry pour créer une application de conversation simple qui se connecte à un projet et à des conversations avec un modèle de langage.

Cet exercice prend environ **40** minutes.

> **Remarque** : cet exercice est basé sur des SDK en préversion, susceptibles d’être modifiés. Le cas échéant, nous avons utilisé des versions spécifiques de certains packages, qui ne correspondent pas forcément aux versions les plus récentes disponibles. Il se peut que vous rencontriez des comportements, inattendus, des avertissements ou des erreurs.

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
    - **Région** : *sélectionnez n’importe quel emplacement pris en charge par les services d’IA***\*

    > \* Certaines ressources Azure AI sont limitées par des quotas de modèles régionaux. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région.

1. Sélectionnez **Créer** et attendez que votre projet, y compris le déploiement du modèle gpt-4 que vous avez sélectionné, soit créé.
1. Une fois le projet créé, le terrain de jeu de conversation instantanée s’ouvrira automatiquement.
1. Dans le volet **Configuration**, notez le nom de votre modèle de déploiement ; il devrait s’agir de **gpt-4o**. Vous pouvez le confirmer en affichant le déploiement dans la page **Modèles et points de terminaison** (ouvrez simplement cette page dans le volet de navigation à gauche).
1. Dans le volet de navigation à gauche, sélectionnez **Vue d’ensemble** pour accéder à la page principale de votre projet ; elle se présente comme suit :

    > **Remarque** : si une erreur *Autorisations insuffisantes** s’affiche, utilisez le bouton **Corriger** pour la résoudre.

    ![Capture d’écran d’une page de présentation d’un projet Azure AI Foundry.](./media/ai-foundry-project.png)

## Créer une application cliente pour converser avec le modèle

Maintenant que vous avez déployé un modèle, vous pouvez utiliser les SDK Azure AI Foundry et Azure AI Model Inference pour développer une application capable d’interagir avec lui par chat.

> **Conseil** : vous pouvez choisir de développer votre solution à l’aide de Python ou de Microsoft C#. Suivez les instructions de la section appropriée pour votre langue choisie.

### Préparer la configuration de l’application

1. Dans le portail Azure AI Foundry, affichez la page **Vue d’ensemble** de votre projet.
1. Dans la zone **Détails du projet**, notez le **point de terminaison du projet Azure AI Foundry**. Vous utiliserez ce point de terminaison pour connecter votre projet à une application cliente.
1. Ouvrez un nouvel onglet de navigateur (en gardant le portail Azure AI Foundry ouvert dans l’onglet existant). Dans un nouvel onglet du navigateur, ouvrez le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.

    Fermez les notifications de bienvenue pour afficher la page d’accueil du portail Azure.

1. Utilisez le bouton **[\>_]** à droite de la barre de recherche, en haut de la page, pour créer un environnement Cloud Shell dans le portail Azure, puis sélectionnez un environnement ***PowerShell*** avec aucun stockage dans votre abonnement.

    Cloud Shell fournit une interface de ligne de commande via un volet situé en bas du portail Azure. Vous pouvez redimensionner ou agrandir ce volet pour faciliter le travail.

    > **Remarque** : si vous avez déjà créé un Cloud Shell qui utilise un environnement *Bash*, basculez-le vers ***PowerShell***.

1. Dans la barre d’outils Cloud Shell, dans le menu **Paramètres**, sélectionnez **Accéder à la version classique** (cela est nécessaire pour utiliser l’éditeur de code).

    **<font color="red">Assurez-vous d’avoir basculé vers la version classique du Cloud Shell avant de continuer.</font>**

1. Dans le volet Cloud Shell, saisissez les commandes suivantes pour cloner le référentiel GitHub contenant les fichiers de code pour cet exercice (saisissez la commande, ou copiez-la dans le presse-papiers puis effectuez un clic droit dans la ligne de commande pour la coller en texte brut) :

    ```
   rm -r mslearn-ai-foundry -f
   git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Conseil** : lorsque vous saisissez des commandes dans le Cloud Shell, la sortie peut occuper une grande partie de la mémoire tampon d’écran. Vous pouvez effacer le contenu de l’écran en saisissant la commande `cls` pour faciliter le focus sur chaque tâche.

1. Une fois le référentiel cloné, accédez au dossier contenant les fichiers de code de l’application de conversation :

    Utilisez la commande ci-dessous en fonction du langage de programmation choisi.

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/c-sharp
    ```

1. Dans le volet de ligne de commande Cloud Shell, saisissez la commande suivante pour installer les bibliothèques que vous allez utiliser :

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.9
   dotnet add package Azure.AI.Inference --version 1.0.0-beta.5
    ```
    

1. Saisissez la commande suivante pour modifier le fichier de configuration fourni :

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    Le fichier s’ouvre dans un éditeur de code.

1. Dans le fichier de code, remplacez l’espace réservé **your_project_endpoint** par le point de terminaison de votre projet (copié depuis la page **Vue d’ensemble** du projet dans le portail Azure AI Foundry), et l’espace réservé **your_model_deployment** par le nom de votre déploiement du modèle gpt-4.
1. Une fois que vous avez remplacé les espaces réservés, utilisez la commande **CTRL+S** ou **Clic droit > Enregistrer** dans l’éditeur de code pour enregistrer vos modifications, puis utilisez la commande **CTRL+Q** ou **Clic droit > Quitter** pour fermer l’éditeur tout en gardant la ligne de commande du Cloud Shell ouverte.

### Écrire du code pour vous connecter à votre projet et converser avec votre modèle

> **Conseil** : lorsque vous ajoutez du code, veillez à conserver la mise en retrait correcte.

1. Saisissez la commande suivante pour modifier le fichier de code fourni :

    **Python**

    ```
   code chat-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Dans le fichier de code, notez les instructions existantes qui ont été ajoutées en haut du fichier pour importer les espaces de noms SDK nécessaires. Ensuite, repérez le commentaire **Ajouter des références**, puis ajoutez le code suivant pour référencer les espaces de noms des bibliothèques que vous avez installées précédemment :

    **Python**

    ```python
   # Add references
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from azure.ai.inference.models import SystemMessage, UserMessage, AssistantMessage
    ```

    **C#**

    ```csharp
   // Add references
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

1. Dans la fonction **main**, sous le commentaire **Obtenir les paramètres de configuration**, notez que le code charge les valeurs de chaîne de connexion du projet et de nom de déploiement du modèle que vous avez définies dans le fichier de configuration.
1. Recherchez le commentaire **Initialiser le client du projet**, puis ajoutez le code suivant pour vous connecter à votre projet Azure AI Foundry à l’aide des identifiants Azure que vous utilisez actuellement pour votre session :

    > **Conseil** : veillez à respecter le niveau d’indentation correct dans votre code.

    **Python**

    ```python
   # Initialize the project client
   projectClient = AIProjectClient(            
            credential=DefaultAzureCredential(
                exclude_environment_credential=True,
                exclude_managed_identity_credential=True
            ),
            endpoint=project_connection,
        )
    ```

    **C#**

    ```csharp
   // Initialize the project client
   DefaultAzureCredentialOptions options = new()
       { ExcludeEnvironmentCredential = true,
        ExcludeManagedIdentityCredential = true };
   var projectClient = new AIProjectClient(
        new Uri(project_connection),
        new DefaultAzureCredential(options));
    ```

1. Recherchez le commentaire **Obtenir un client de conversation instantanée**, puis ajoutez le code suivant pour créer un objet client permettant d’interagir avec un modèle :

    **Python**

    ```python
   # Get a chat client
   chat = projectClient.inference.get_chat_completions_client()
    ```

    **C#**

    ```csharp
   // Get a chat client
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```

    > **Note** : ce code utilise le client de projet Azure AI Foundry pour établir une connexion sécurisée avec le point de terminaison par défaut du service Azure AI Model Inference associé à votre projet. Vous pouvez également vous connecter *directement* au point de terminaison en utilisant le SDK Azure AI Model Inference, en spécifiant l’URI du point de terminaison affiché pour la connexion au service dans le portail Azure AI Foundry ou dans la page de la ressource correspondante Azure AI Services du portail Azure, et en utilisant une clé d’authentification ou un jeton d’identification Entra. Pour en savoir plus sur la connexion au service Azure AI Model Inference, consultez la page [API Azure AI Model Inference](https://learn.microsoft.com/azure/machine-learning/reference-model-inference-api).

1. Trouvez le commentaire : **Initialiser l’invite avec le message système**, puis ajoutez le code suivant pour initialiser une collection de messages avec une invite système.

    **Python**

    ```python
   # Initialize prompt with system message
   prompt=[
            SystemMessage("You are a helpful AI assistant that answers questions.")
        ]
    ```

    **C#**

    ```csharp
   // Initialize prompt with system message
   var prompt = new List<ChatRequestMessage>(){
                    new ChatRequestSystemMessage("You are a helpful AI assistant that answers questions.")
                };
    ```

1. Notez que le code inclut une boucle pour permettre à un utilisateur d’entrer une invite jusqu’à ce qu’il entre « quitter ». Ensuite, dans la section de la boucle, trouvez le commentaire : **Obtenir une complétion de conversation**, puis ajoutez le code suivant pour ajouter l’entrée de l’utilisateur à l’invite, récupérer la complétion générée par votre modèle, et ajouter cette complétion à l’invite (afin de conserver l’historique de la conversation pour les itérations suivantes) :

    **Python**

    ```python
   # Get a chat completion
   prompt.append(UserMessage(input_text))
   response = chat.complete(
        model=model_deployment,
        messages=prompt)
   completion = response.choices[0].message.content
   print(completion)
   prompt.append(AssistantMessage(completion))
    ```

    **C#**

    ```csharp
   // Get a chat completion
   prompt.Add(new ChatRequestUserMessage(input_text));
   var requestOptions = new ChatCompletionsOptions()
   {
       Model = model_deployment,
       Messages = prompt
   };

   Response<ChatCompletions> response = chat.Complete(requestOptions);
   var completion = response.Value.Content;
   Console.WriteLine(completion);
   prompt.Add(new ChatRequestAssistantMessage(completion));
    ```

1. Utilisez la commande **Ctrl+S** pour enregistrer les modifications que vous avez apportées au fichier de code.

### Se connecter à Azure et exécuter l’application

1. Dans le volet de la ligne de commande Cloud Shell, entrez la commande suivante pour vous connecter à Azure.

    ```
   az login
    ```

    **<font color="red">Vous devez vous connecter à Azure, même si la session Cloud Shell est déjà authentifiée.</font>**

    > **Remarque** :dans la plupart des scénarios, l’utilisation d’*az login* suffit. Toutefois, si vous avez des abonnements dans plusieurs locataires, vous devrez peut-être spécifier le locataire à l’aide du paramètre *--tenant*. Pour plus d’informations, consultez [Se connecter à Azure de manière interactive à l’aide d’Azure CLI](https://learn.microsoft.com/cli/azure/authenticate-azure-cli-interactively).
    
1. Lorsque l’invite apparaît, suivez les instructions pour ouvrir la page de connexion dans un nouvel onglet et entrez le code d’authentification fourni ainsi que vos informations d’identification Azure. Effectuez ensuite le processus de connexion dans la ligne de commande, en sélectionnant l’abonnement contenant votre hub Azure AI Foundry si nécessaire.
1. Une fois connecté, entrez la commande suivante pour exécuter l’application :

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Lorsque vous y êtes invité, entrez une question, par exemple `What is the fastest animal on Earth?`, et passez en revue la réponse de votre modèle d’IA générative.
1. Essayez de poser quelques questions de suivi, comme : « `Where can I see one?` » ou « `Are they endangered?` ». La conversation doit se poursuivre en utilisant l’historique des échanges comme contexte pour chaque itération.
1. Lorsque vous avez terminé, entrez `quit` pour quitter le programme.

> **Conseil** : si l’application échoue en raison du dépassement de la limite de débit, patientez quelques secondes, puis réessayez. Si le quota disponible dans votre abonnement est insuffisant, le modèle peut ne pas être en mesure de répondre.

## Résumé

Dans cet exercice, vous avez utilisé le SDK Azure AI Foundry pour créer une application cliente pour un modèle d’IA générative que vous avez déployé dans un projet Azure AI Foundry.

## Nettoyage

Si vous avez terminé d’explorer Azure AI Foundry, vous devez supprimer les ressources que vous avez créées dans cet exercice pour éviter d’entraîner des coûts Azure inutiles.

1. Ouvrez le [portail Azure](https://portal.azure.com) et affichez le contenu du groupe de ressources où vous avez déployé les ressources utilisées dans cet exercice.
1. Dans la barre d’outils, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources et confirmez que vous souhaitez le supprimer.
