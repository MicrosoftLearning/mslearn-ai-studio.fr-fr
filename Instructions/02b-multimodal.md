---
lab:
  title: Développer une application d’IA générative mtltimodale
  description: "Découvrez comment utiliser Azure\_AI Foundry pour créer une application d’IA générative qui prend en charge les données audio, de texte et d’image."
---

# Développer une application d’IA générative mtltimodale

Dans cet exercice, vous utilisez le modèle d’IA générative *Phi-4-multimodal-instruct* pour générer des réponses aux invites qui incluent du texte, des images et des données audio. Vous allez développer une application qui aide l’IA à fournir des produits frais dans une épicerie grâce á Azure AI Foundry et au service d’inférence de modèle Azure AI.

Cet exercice prend environ **30** minutes.

> **Remarque** : cet exercice est basé sur des SDK en préversion, susceptibles d’être modifiés. Le cas échéant, nous avons utilisé des versions spécifiques de certains packages, qui ne correspondent pas forcément aux versions les plus récentes disponibles. Un comportement inattendu, des avertissements ou des erreurs peuvent se produire.

## Créer un projet Azure AI Foundry

Commençons par créer un projet Azure AI Foundry.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les conseils ou les volets de démarrage rapide ouverts la première fois que vous vous connectez et, si nécessaire, utilisez le logo **Azure AI Foundry** en haut à gauche pour accéder à la page d’accueil, qui ressemble à l’image suivante :

    ![Capture d’écran du portail Azure AI Foundry.](./media/ai-foundry-home.png)

2. Sur la page d’accueil, sélectionnez **+Créer un projet**.
3. Dans l’assistant **Créer un projet**, saisissez un nom valide pour votre projet. Si un hub existant est suggéré, choisissez l’option permettant d’en créer un. Passez ensuite en revue les ressources Azure qui seront créées automatiquement pour prendre en charge votre hub et votre projet.
4. Sélectionnez **Personnaliser** et spécifiez les paramètres suivants pour votre hub :
    - **Nom du hub** : *nom valide pour votre hub*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *créez ou sélectionnez un groupe de ressources*
    - **Région** : Sélectionnez l’une des régions suivantes\* :
        - USA Est
        - USA Est 2
        - Centre-Nord des États-Unis
        - États-Unis - partie centrale méridionale
        - Suède Centre
        - USA Ouest
        - USA Ouest 3
    - **Connecter Azure AI Services ou Azure OpenAI** : *créer des ressources AI Services*
    - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \* Au moment de l’écriture, le modèle Microsoft *Phi-4-multimodal-instruct* que nous allons utiliser dans cet exercice est disponible dans ces régions. Vous pouvez consulter les dernières disponibilités régionales de certains modèles dans la [documentation Azure AI Foundry](https://learn.microsoft.com/azure/ai-foundry/how-to/deploy-models-serverless-availability#region-availability). Si une limite de quota régionale est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région.

5. Sélectionnez **Suivant** et passez en revue votre configuration. Sélectionnez **Créer** et patientez jusqu’à ce que l’opération se termine.
6. Une fois votre projet créé, fermez les conseils affichés et passez en revue la page du projet dans le portail Azure AI Foundry, qui doit ressembler à l’image suivante :

    ![Capture d’écran des détails d’un projet Azure AI dans le portail Azure AI Foundry.](./media/ai-foundry-project.png)

## Déployer un modèle

Vous êtes maintenant prêt à déployer un modèle *Phi-4-multimodal-instruct* pour prendre en charge les invites multimodales.

1. Dans la barre d’outils située en haut à droite de la page de votre projet Azure AI Foundry, utilisez l’icône **Fonctionnalités en version préliminaire** (**&#9215;**) pour vérifier que la fonctionnalité **Déployer des modèles sur le service d’inférence de modèle Azure AI** est activée. Cette fonctionnalité garantit que votre déploiement de modèle est disponible pour le service Inférence Azure AI, que vous utiliserez dans votre code d’application.
2. Dans le volet de gauche de votre projet, dans la section **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
3. Sur la page **Modèles + points de terminaison**, dans l’onglet **Déploiements de modèles**, dans le menu **+ Déployer un modèle**, sélectionnez **Déployer le modèle de base**.
4. Recherchez le modèle **Phi-4-multimodal-instruct** dans la liste, puis sélectionnez-le et confirmez-le.
5. Acceptez le contrat de licence si vous y êtes invité, puis déployez le modèle avec les paramètres suivants en sélectionnant **Personnaliser** dans les détails du déploiement :
    - **Nom du déploiement** : *nom valide pour votre modèle de déploiement*
    - **Type de déploiement** : standard global
    - **Détails du déploiement** : *utilisez les paramètres par défaut*
6. Attendez que l’état d’approvisionnement du déploiement soit **Terminé**.

## Créer une application cliente

Maintenant que vous avez déployé le modèle, vous pouvez utiliser le déploiement dans une application cliente.

> **Conseil** : vous pouvez choisir de développer votre solution à l’aide de Python ou de Microsoft C#. Suivez les instructions de la section appropriée pour votre langue choisie.

### Préparer la configuration de l’application

1. Dans le portail Azure AI Foundry, affichez la page **Vue d’ensemble** de votre projet.
2. Dans la zone **Détails du projet**, notez la **chaîne de connexion du projet**. Vous utiliserez cette chaîne de connexion pour vous connecter à votre projet dans une application cliente.
3. Ouvrez un nouvel onglet de navigateur (en gardant le portail Azure AI Foundry ouvert dans l’onglet existant). Dans un nouvel onglet du navigateur, ouvrez le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.

    Fermez les notifications de bienvenue pour afficher la page d’accueil du portail Azure.

1. Utilisez le bouton **[\>_]** à droite de la barre de recherche, en haut de la page, pour créer un environnement Cloud Shell dans le portail Azure, puis sélectionnez un environnement ***PowerShell*** avec aucun stockage dans votre abonnement.

    Cloud Shell fournit une interface de ligne de commande via un volet situé en bas du portail Azure. Vous pouvez redimensionner ou agrandir ce volet pour faciliter le travail.

    > **Remarque** : si vous avez déjà créé un Cloud Shell qui utilise un environnement *Bash*, basculez-le vers ***PowerShell***.

5. Dans la barre d’outils Cloud Shell, dans le menu **Paramètres**, sélectionnez **Accéder à la version classique** (cela est nécessaire pour utiliser l’éditeur de code).

    **<font color="red">Assurez-vous d’avoir basculé vers la version classique du Cloud Shell avant de continuer.</font>**

1. Dans le volet Cloud Shell, entrez les commandes suivantes pour cloner le référentiel GitHub contenant les fichiers de code de cet exercice (saisissez la commande, ou copiez-la vers le presse-papiers et cliquez avec le bouton droit dans la ligne de commande pour la coller sous forme de texte brut) :

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Conseil** : lorsque vous collez des commandes dans Cloud Shell, la sortie peut prendre une grande quantité de mémoire tampon d’écran. Vous pouvez effacer le contenu de l’écran en saisissant la commande `cls` pour faciliter le focus sur chaque tâche.

7. Une fois le référentiel cloné, accédez au dossier contenant les fichiers de code de l’application :  

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/multimodal/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/multimodal/c-sharp
    ```

8. Dans le volet de ligne de commande Cloud Shell, saisissez la commande suivante pour installer les bibliothèques que vous allez utiliser :

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

9. Saisissez la commande suivante pour modifier le fichier de configuration fourni :

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    Le fichier s’ouvre dans un éditeur de code.

10. Dans le fichier de code, remplacez l’espace réservé **your_project_connection_string** par la chaîne de connexion de votre projet (copiée depuis la page **Vue d'ensemble** du projet dans le portail Azure AI Foundry), et remplacez **your_model_deployment** par le nom que vous avez attribué à votre déploiement du modèle Phi-4-multimodal-instruct.
11. Une fois que vous avez remplacé les espaces réservés, dans l’éditeur de code, utilisez la commande **Ctrl+S** ou **Clic droit > Enregistrer** dans l’éditeur de code pour enregistrer vos modifications, puis utilisez la commande **Ctrl+Q** ou **Clic droit > Quitter** pour fermer l’éditeur tout en gardant la ligne de commande Cloud Shell ouverte.

### Écrire du code pour vous connecter à votre projet et obtenir un client de conversation instantanée pour votre modèle

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

2. Dans le fichier de code, notez les instructions existantes qui ont été ajoutées en haut du fichier pour importer les espaces de noms SDK nécessaires. Ensuite, sous le commentaire **Ajouter des références**, ajoutez le code suivant pour référencer les espaces de noms dans les bibliothèques que vous avez installées précédemment :

    **Python**

    ```
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from azure.ai.inference.models import (
       SystemMessage,
       UserMessage,
       TextContentItem,
       ImageContentItem,
       ImageUrl,
       AudioContentItem,
       InputAudio,
       AudioContentFormat,
   )
    ```

    **C#**

    ```
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

3. Dans la fonction **main**, sous le commentaire **Obtenir les paramètres de configuration**, notez que le code charge les valeurs de chaîne de connexion du projet et de nom de déploiement du modèle que vous avez définies dans le fichier de configuration.
4. Sous le commentaire **Initialiser le client du projet**, ajoutez le code suivant pour vous connecter à votre projet Azure AI Foundry à l’aide des informations d’identification Azure que vous utilisez actuellement :

    **Python**

    ```
   project_client = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

5. Sous le commentaire **Obtenir un client de conversation instantanée**, ajoutez le code suivant pour créer un objet client afin de converser avec votre modèle :

    **Python**

    ```
   chat_client = project_client.inference.get_chat_completions_client(model=model_deployment)
    ```

    **C#**

    ```
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```


### Écrire du code pour utiliser une invite textuelle

1. Notez que le code inclut une boucle pour permettre à un utilisateur d’entrer une invite jusqu’à ce qu’il entre « quitter ». Ensuite, dans la section de boucle, sous le commentaire **Obtenir une réponse à l‘entrée d’un texte**, ajoutez le code suivant pour envoyer une invite textuelle et récupérer la réponse à partir de votre modèle :

    **Python**

    ```python
   response = chat_client.complete(
       messages=[
           SystemMessage(system_message),
           UserMessage(content=[TextContentItem(text= prompt)])
       ])
   print(response.choices[0].message.content)
    ```

    **C#**

    ```
   var requestOptions = new ChatCompletionsOptions()
   {
   Model = model_deployment,
   Messages =
       {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage(prompt),
       }
   };

   Response<ChatCompletions> response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

2. Utilisez la commande **Ctrl+S** pour enregistrer les modifications que vous avez apportées au fichier de code. Laissez-le encore.

3. Dans le volet de ligne de commande Cloud Shell, sous l’éditeur de code, entrez la commande suivante pour exécuter l’application :

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Lorsque vous y êtes invité, entrez `1` pour utiliser une invite textuelle, puis entrez l’invite `I want to make an apple pie. What kind of apple should I use?`
5. Vérifiez la réponse. Ensuite, entrez `quit` pour quitter le programme.

### Écrire du code pour utiliser une invite basée sur une image

1. Dans l’éditeur de code du fichier **chat-app.py**, dans la section de boucle, sous le commentaire **Obtenir une réponse à l’entrée d’image**, ajoutez le code suivant pour envoyer une invite qui inclut l’image suivante :

    ![Photo d’une orange.](../labfiles/multimodal/orange.jpg)

    **Python**

    ```python
   image_url = "https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/orange.jpg"
   image_format = "jpeg"
   request = Request(image_url, headers={"User-Agent": "Mozilla/5.0"})
   image_data = base64.b64encode(urlopen(request).read()).decode("utf-8")
   data_url = f"data:image/{image_format};base64,{image_data}"

   response = chat_client.complete(
       messages=[
           SystemMessage(system_message),
           UserMessage(content=[
               TextContentItem(text=prompt),
               ImageContentItem(image_url=ImageUrl(url=data_url))
           ]),
       ]
   )
   print(response.choices[0].message.content)
    ```

    **C#**

    ```csharp
  string imageUrl = "https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/orange.jpg";
   ChatCompletionsOptions requestOptions = new ChatCompletionsOptions()
   {
       Messages = {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage([
               new ChatMessageTextContentItem(prompt),
               new ChatMessageImageContentItem(new Uri(imageUrl))
           ]),
       },
       Model = model_deployment
   };
   var response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

2. Utilisez la commande **Ctrl+S** pour enregistrer les modifications que vous avez apportées au fichier de code. Laissez-le encore.

3. Dans le volet de ligne de commande Cloud Shell, sous l’éditeur de code, entrez la commande suivante pour exécuter l’application :

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Lorsque vous y êtes invité, entrez `2` pour utiliser une invite basée sur une image, puis entrez l‘invite `I don't know what kind of fruit this is. Can you identify it, and tell me what kinds of food I could make with it?`
5. Vérifiez la réponse. Ensuite, entrez `quit` pour quitter le programme.

### Écrire du code pour utiliser une invite basée sur de l‘audio

1. Dans l’éditeur de code du fichier **chat-app.py**, dans la section de boucle, sous le commentaire **Obtenir une réponse à l’entrée de l’audio**, ajoutez le code suivant pour envoyer une invite qui inclut l’audio suivant :

    <video controls src="./media/manzanas.mp4" title="L’heure est 2:15." width="150"></video>

    **Python**

    ```python
   file_path="https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/manzanas.mp3"
   response = chat_client.complete(
           messages=[
               SystemMessage(system_message),
               UserMessage(
                   [
                       TextContentItem(text=prompt),
                       {
                           "type": "audio_url",
                           "audio_url": {"url": file_path}
                       }
                   ]
               )
           ]
       )
   print(response.choices[0].message.content)
    ```

    **C#**

    ```csharp
   string audioUrl="https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/manzanas.mp3";
   var requestOptions = new ChatCompletionsOptions()
   {
       Messages =
       {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage(
               new ChatMessageTextContentItem(prompt),
               new ChatMessageAudioContentItem(new Uri(audioUrl))),
       },
       Model = model_deployment
   };
   var response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```


2. Utilisez la commande **Ctrl+S** pour enregistrer les modifications que vous avez apportées au fichier de code. Vous pouvez également fermer l’éditeur de code (**Ctrl+Q**) si vous le souhaitez.

3. Dans le volet de ligne de commande Cloud Shell, sous l’éditeur de code, entrez la commande suivante pour exécuter l’application :

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Lorsque vous y êtes invité, entrez `3` pour utiliser une invite basée sur de l‘audio, puis entrez l’invite `What is this customer saying in English?`
5. Vérifiez la réponse.
6. Vous pouvez continuer à exécuter l‘application, en choisissant différents types d‘invites et en essayant différentes invites. Lorsque vous avez terminé, entrez `quit` pour quitter le programme.

    Si vous avez le temps, vous pouvez modifier le code pour utiliser une autre invite du système et vos propres fichiers audio et image accessibles sur Internet.

    > **Remarque** : dans cette application simple, nous n‘avons pas implémenté de logique pour conserver l‘historique des conversations. Par conséquent, le modèle traite chaque invite comme une nouvelle requête sans contexte de l‘invite précédente.

## Résumé

Dans cet exercice, vous avez utilisé Azure AI Foundry et le Kit de développement logiciel (SDK) Azure AI Inference pour créer une application cliente utilisant un modèle multimodal afin de générer des réponses au texte, aux images et à l‘audio.

## Nettoyage

.Si vous avez terminé d‘explorer Azure AI Foundry, vous devez supprimer les ressources que vous avez créées dans cet exercice pour éviter d‘entraîner des coûts Azure inutiles.

1. Revenez à l’onglet du navigateur contenant le portail Azure (ou ouvrez à nouveau le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` dans un nouvel onglet de navigateur) et affichez le contenu du groupe de ressources dans lequel vous avez déployé les ressources utilisées dans cet exercice.
1. Dans la barre d’outils, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources et confirmez que vous souhaitez le supprimer.
