---
lab:
  title: Créer une application de conversation d’IA générative
  description: "Découvrez comment utiliser le SDK Azure\_AI\_Foundry pour créer une application qui se connecte à votre projet et à vos conversations avec un modèle de langage."
---

# Créer une application de conversation d’IA générative

Dans cet exercice, vous utilisez le SDK Azure AI Foundry pour créer une application de conversation simple qui se connecte à un projet et à des conversations avec un modèle de langage.

Cet exercice prend environ **40** minutes.

> **Remarque** : cet exercice est basé sur des SDK en préversion, susceptibles d’être modifiés. Le cas échéant, nous avons utilisé des versions spécifiques de certains packages, qui ne correspondent pas forcément aux versions les plus récentes disponibles.

## Créer un projet Azure AI Foundry

Commençons par créer un projet Azure AI Foundry.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les conseils ou les volets de démarrage rapide ouverts la première fois que vous vous connectez et, si nécessaire, utilisez le logo **Azure AI Foundry** en haut à gauche pour accéder à la page d’accueil, qui ressemble à l’image suivante (fermez le volet **Aide** s’il est ouvert) :

    ![Capture d’écran du portail Azure AI Foundry.](./media/ai-foundry-home.png)

1. Sur la page d’accueil, sélectionnez **+Créer un projet**.
1. Dans l’assistant **Créer un projet**, saisissez un nom valide pour votre projet. Si un hub existant est suggéré, choisissez l’option permettant d’en créer un nouveau. Passez ensuite en revue les ressources Azure qui seront créées automatiquement pour prendre en charge votre hub et votre projet.
1. Sélectionnez **Personnaliser** et spécifiez les paramètres suivants pour votre hub :
    - **Nom du hub** : *un nom valide pour votre hub*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *créez ou sélectionnez un groupe de ressources*
    - **Emplacement** : sélectionnez **Aidez-moi à choisir**, puis sélectionnez **gpt-4o** dans la fenêtre d’aide à la sélection de l’emplacement et utilisez la région recommandée\*
    - **Connecter Azure AI Services ou Azure OpenAI** : *créer une nouvelle ressource AI Services*
    - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \*Les ressources Azure OpenAI sont soumises à des quotas de modèle par région. En cas de dépassement de quota au cours de l’exercice, vous devrez peut-être créer une autre ressource dans une région différente.

1. Sélectionnez **Suivant** et passez en revue votre configuration. Sélectionnez **Créer** et patientez jusqu’à ce que l’opération se termine.
1. Une fois votre projet créé, fermez les conseils affichés et passez en revue la page du projet dans le portail Azure AI Foundry, qui doit ressembler à l’image suivante :

    ![Capture d’écran des détails d’un projet Azure AI dans le portail Azure AI Foundry.](./media/ai-foundry-project.png)

## Déployer un modèle d’IA générative

Vous êtes maintenant prêt à déployer un modèle de langage d’IA générative pour prendre en charge votre application de conversation instantanée. Dans cet exemple, vous utiliserez le modèle gpt-4o d’OpenAI ; toutefois, les principes sont les mêmes pour tout modèle.

1. Dans la barre d’outils située en haut à droite de la page de votre projet Azure AI Foundry, utilisez l’icône **Fonctionnalités en préversion** (**&#9215;**) pour vérifier que la fonctionnalité **Déployer des modèles vers le service d’inférence de modèles Azure AI** est activée. Cette fonctionnalité garantit que votre déploiement de modèle est disponible pour le service Inférence Azure AI, que vous utiliserez dans votre code d’application.
1. Dans le volet de gauche de votre projet, dans la section **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
1. Sur la page **Modèles + points de terminaison**, dans l’onglet **Déploiements de modèles**, dans le menu **+ Déployer un modèle**, sélectionnez **Déployer le modèle de base**.
1. Recherchez le modèle **gpt-4** dans la liste, puis sélectionnez-le et confirmez votre choix.
1. Déployez le modèle avec les paramètres suivants en sélectionnant **Personnaliser** dans les détails du déploiement :
    - **Nom du déploiement** : *Un nom valide pour le modèle de déploiement*
    - **Type de déploiement** : standard global
    - **Mise à jour automatique de la version** : Activée
    - **Version du modèle** : *Sélectionnez la version la plus récente disponible*
    - **Ressource IA connectée** : *sélectionnez votre connexion de ressources Azure OpenAI*
    - **Limite de jetons par minute (en milliers)**  : 50K *(ou le maximum disponible dans votre abonnement si inférieur à 50K)*
    - **Filtre de contenu** : DefaultV2

    > **Remarque** : La réduction du nombre de jetons par minute permet d’éviter une surutilisation du quota disponible dans l’abonnement que vous utilisez. 50 000 TPM devraient suffire pour les données utilisées dans cet exercice. Si votre quota disponible est inférieur à cette valeur, vous pourrez tout de même terminer l’exercice, mais vous pourriez rencontrer des erreurs en cas de dépassement de la limite.

1. Attendez la fin du déploiement.

## Créer une application cliente pour converser avec le modèle

Maintenant que vous avez déployé un modèle, vous pouvez utiliser les SDK Azure AI Foundry et Azure AI Model Inference pour développer une application capable d’interagir avec lui par chat.

> **Conseil** : vous pouvez choisir de développer votre solution à l’aide de Python ou de Microsoft C#. Suivez les instructions de la section appropriée pour votre langue choisie.

### Préparer la configuration de l’application

1. Dans le portail Azure AI Foundry, affichez la page **Vue d’ensemble** de votre projet.
1. Dans la zone **Détails du projet**, notez la **chaîne de connexion du projet**. Vous utiliserez cette chaîne de connexion pour vous connecter à votre projet dans une application cliente.
1. Ouvrez un nouvel onglet de navigateur (en gardant le portail Azure AI Foundry ouvert dans l’onglet existant). Dans un nouvel onglet du navigateur, ouvrez le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.

    Fermez les notifications de bienvenue pour afficher la page d’accueil du portail Azure.

1. Utilisez le bouton **[\>_]** à droite de la barre de recherche, en haut de la page, pour créer un environnement Cloud Shell dans le portail Azure, puis sélectionnez un environnement ***PowerShell*** avec aucun stockage dans votre abonnement.

    Le Cloud Shell fournit une interface en ligne de commande dans un volet situé en bas du portail Azure. Vous pouvez redimensionner ou agrandir ce volet pour faciliter le travail.

    > **Remarque** : si vous avez déjà créé un Cloud Shell qui utilise un environnement *Bash*, basculez-le vers ***PowerShell***.

1. Dans la barre d’outils Cloud Shell, dans le menu **Paramètres**, sélectionnez **Accéder à la version classique** (cela est nécessaire pour utiliser l’éditeur de code).

    **<font color="red">Assurez-vous d’avoir basculé vers la version classique du Cloud Shell avant de continuer.</font>**

1. Dans le volet Cloud Shell, saisissez les commandes suivantes pour cloner le dépôt GitHub contenant les fichiers de code pour cet exercice (saisissez la commande, ou copiez-la dans le presse-papiers puis effectuez un clic droit dans la ligne de commande pour la coller en texte brut) :

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Conseil** : lorsque vous saisissez des commandes dans le Cloud Shell, la sortie peut occuper une grande partie du tampon d’affichage. Vous pouvez effacer le contenu de l’écran en saisissant la commande `cls` pour faciliter le focus sur chaque tâche.

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

1. Dans le volet en ligne de commande du Cloud Shell, saisissez la commande suivante pour installer les bibliothèques que vous utiliserez :

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.3
   dotnet add package Azure.AI.Inference --version 1.0.0-beta.3
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

1. Dans le fichier de code, remplacez l’espace réservé **your_project_connection_string** par la chaîne de connexion de votre projet (copiée depuis la page **Vue d'ensemble** du projet dans le portail Azure AI Foundry), et remplacez **your_model_deployment** par le nom que vous avez attribué à votre déploiement du modèle gpt-4.
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
1. Recherchez le commentaire **Initialiser le client du projet**, puis ajoutez le code suivant pour vous connecter à votre projet Azure AI Foundry à l’aide des identifiants Azure actuellement utilisés pour votre session :

    > **Conseil** : veillez à respecter le niveau d’indentation correct dans votre code.

    **Python**

    ```python
   # Initialize the project client
   projectClient = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```csharp
   // Initialize the project client
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
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

### Exécutez l’application de conversation

1. Dans le volet en ligne de commande du Cloud Shell, sous l’éditeur de code, saisissez la commande suivante pour exécuter l’application :

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

> **Conseil** : si l’application échoue en raison du dépassement de la limite de débit. Patientez quelques secondes, puis réessayez. Si le quota disponible dans votre abonnement est insuffisant, le modèle peut ne pas être en mesure de répondre.

## Utilisation du SDK Azure OpenAI

Votre application cliente est conçue à l’aide du SDK Azure AI Model Inference, ce qui signifie qu’elle peut être utilisée avec n’importe quel modèle déployé sur le service Azure AI Model Inference. Le modèle que vous avez déployé est un modèle GPT d’OpenAI, que vous pouvez également exploiter à l’aide du SDK OpenAI.

Apportons quelques modifications au code pour voir comment implémenter une application de conversation instantanée en utilisant le SDK OpenAI.

1. Dans le terminal Cloud Shell, dans le dossier contenant votre code (*python* ou *c-sharp*), saisissez la commande suivante pour installer le package requis :

    **Python**

    ```
   pip install openai
    ```

    **C#**

    ```
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.6
   dotnet add package Azure.AI.OpenAI --prerelease
    ```

> **Remarque** : une version différente préliminaire du package Azure.AI.Projects est requise à titre de solution provisoire pour pallier certaines incompatibilités avec le SDK Azure AI Model Inference.

1. Si votre fichier de code (*chat-app.py* ou *Program.cs*) n’est pas déjà ouvert, saisissez la commande suivante pour l’ouvrir dans l’éditeur de code :

    **Python**

    ```
   code chat-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. En haut du fichier de code, ajoutez la ou les références suivantes :

    **Python**

    ```python
   import openai
    ```

    **C#**

    ```csharp
   using OpenAI.Chat;
   using Azure.AI.OpenAI;
    ```

1. Trouvez le commentaire « **Obtenir un client de conversation instantanée** » et modifiez le code de création de l’objet client comme suit :

    **Python**

    ```python
   # Get a chat client 
   openai_client = projectClient.inference.get_azure_openai_client(api_version="2024-10-21")
    ```

    **C#**

    ```csharp
   // Get a chat client
   ChatClient openaiClient = projectClient.GetAzureOpenAIChatClient(model_deployment);
    ```

    > **Remarque** : ce code utilise le client de projet Azure AI Foundry pour établir une connexion sécurisée avec le point de terminaison par défaut du service Azure OpenAI associé à votre projet. Vous pouvez également vous connecter *directement* au point de terminaison en utilisant le SDK Azure OpenAI, en spécifiant l’URI du point de terminaison affiché pour la connexion au service dans le portail Azure AI Foundry ou sur la page de la ressource correspondante Azure OpenAI ou Azure AI Services dans le portail Azure, et en utilisant une clé d’authentification ou un jeton d’identification Entra. Pour en savoir plus sur la connexion au service Azure OpenAI, consultez la page [Azure OpenAI - Langages de programmation pris en charge](https://learn.microsoft.com/azure/ai-services/openai/supported-languages).

1. Trouvez le commentaire « **Initialiser l’invite avec le message système** », puis modifiez le code pour initialiser une collection de messages avec une invite système comme suit :

    **Python**

    ```python
   # Initialize prompt with system message
   prompt=[
        {"role": "system", "content": "You are a helpful AI assistant that answers questions."}
    ]
    ```

    **C#**

    ```csharp
   // Initialize prompt with system message
    var prompt = new List<ChatMessage>(){
        new SystemChatMessage("You are a helpful AI assistant that answers questions.")
    };
    ```

1. Recherchez le commentaire « **Obtenir une réponse de conversation** », puis modifiez le code afin d’ajouter l’entrée de l’utilisateur à l’invite, d’obtenir la réponse du modèle, et d’ajouter cette réponse à l’historique de conversation comme suit :

    **Python**

    ```python
   # Get a chat completion
   prompt.append({"role": "user", "content": input_text})
   response = openai_client.chat.completions.create(
        model=model_deployment,
        messages=prompt)
   completion = response.choices[0].message.content
   print(completion)
   prompt.append({"role": "assistant", "content": completion})
    ```

    **C#**

    ```csharp
   // Get a chat completion
   prompt.Add(new UserChatMessage(input_text));
   ChatCompletion completion = openaiClient.CompleteChat(prompt);
   var completionText = completion.Content[0].Text;
   Console.WriteLine(completionText);
   prompt.Add(new AssistantChatMessage(completionText));
    ```

1. Utilisez la commande **Ctrl+S** pour enregistrer les modifications que vous avez apportées au fichier de code.

1. Dans le volet en ligne de commande du Cloud Shell, sous l’éditeur de code, saisissez la commande suivante pour exécuter l’application :

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Testez l’application en envoyant des questions comme avant. Lorsque vous avez terminé, entrez `quit` pour quitter le programme.

    > **Remarque** : le SDK Azure AI Model Inference et les SDK OpenAI utilisent des classes et des structures de code similaires, ce qui permet de modifier le code avec un minimum de changements. Vous pouvez utiliser le SDK Azure AI Model Inference avec *n’importe quel* modèle déployé sur un point de terminaison du service Azure AI Model Inference. Le SDK OpenAI fonctionne uniquement avec les modèles OpenAI, mais vous pouvez l’utiliser pour des modèles déployés soit sur un point de terminaison du service Azure AI Model Inference, soit sur un point de terminaison Azure OpenAI.  

## Résumé

Dans cet exercice, vous avez utilisé Azure AI Foundry, Azure AI Model Inference et les SDK Azure OpenAI pour créer une application cliente destinée à un modèle d’IA générative que vous avez déployé dans un projet Azure AI Foundry.

## Nettoyage

Si vous avez terminé d’explorer Azure AI Foundry, vous devez supprimer les ressources que vous avez créées dans cet exercice pour éviter d’entraîner des coûts Azure inutiles.

1. Revenez à l’onglet du navigateur contenant le portail Azure (ou ouvrez à nouveau le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` dans un nouvel onglet de navigateur) et affichez le contenu du groupe de ressources dans lequel vous avez déployé les ressources utilisées dans cet exercice.
1. Dans la barre d’outils, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources et confirmez que vous souhaitez le supprimer.
