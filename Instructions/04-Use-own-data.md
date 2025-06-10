---
lab:
  title: Créer une application d’IA générative qui utilise vos propres données
  description: Découvrez comment utiliser le modèle RAG (génération augmentée de récupération) pour créer une application de conversation qui déclenche des invites à l’aide de vos propres données.
---

# Créer une application d’IA générative qui utilise vos propres données

La génération augmentée e récupération (RAG, Retrieval Augmented Generation) est une technique utilisée pour créer des applications qui intègrent des données provenant de sources de données personnalisées dans une invite pour un modèle d’IA générative. La RAG est un modèle couramment utilisé pour développer des applications d’IA générative, qui sont des applications basées sur la conversation instantanée utilisant un modèle de langage pour interpréter les entrées et générer des réponses appropriées.

Dans cet exercice, vous allez utiliser le portail Azure AI Foundry pour intégrer des données personnalisées dans une solution d’IA générative.

Cet exercice prend environ **45** minutes.

> **Remarque** : cet exercice est basé sur des services en préversion, susceptibles d’être modifiés.

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

## Déployer des modèles

Vous avez besoin de deux modèles pour implémenter votre solution :

- Un modèle *d’incorporation* pour vectoriser les données texte pour une indexation et un traitement efficaces.
- Un modèle qui peut générer des réponses en langage naturel aux questions en fonction de vos données.

1. Dans le portail Azure AI Foundry, dans votre projet, dans le volet de navigation de gauche, dans **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
1. Créez un déploiement du modèle **text-embedding-ada-002** avec les paramètres suivants en sélectionnant **Personnaliser** dans l’assistant Déployer le modèle :

    - **Nom du déploiement** : *nom valide pour votre modèle de déploiement*
    - **Type de déploiement** : standard global
    - **Version du modèle** : *Sélectionnez la version par défaut*
    - **Ressource IA connectée** : *sélectionnez la ressource créée précédemment*
    - **Limite de jetons par minute (en milliers)**  : 50 *(ou le maximum disponible dans votre abonnement si inférieur à 50 000)*
    - **Filtre de contenu** : DefaultV2

    > **Remarque** : si votre emplacement actuel de ressource IA n’a pas de quota disponible pour le modèle que vous souhaitez déployer, vous êtes invité à choisir un autre emplacement où une nouvelle ressource IA sera créée et connectée à votre projet.

1. Revenez à la page **Modèles + points de terminaison** et répétez les étapes précédentes pour déployer un modèle **gpt-4o** à l’aide d’un déploiement **Standard global** de la version la plus récente, avec une limite de **50 000 TPM** (ou le maximum disponible dans votre abonnement si inférieur à 50 000).

    > **Remarque** : La réduction du nombre de jetons par minute (TPM) permet d’éviter une surutilisation du quota disponible dans l’abonnement que vous utilisez. 50 000 TPM suffisent pour les données utilisées dans cet exercice.

## Ajouter des données dans votre projet

Les données de votre application se composent d’un ensemble de brochures de voyage au format PDF issues de l’agence de voyages fictive *Margie’s Travel*. Ajoutons-les au projet.

1. Dans un nouvel onglet du navigateur, téléchargez l’[archive compressée des brochures](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) depuis `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` et décompressez-la dans un dossier nommé **brochures** sur votre système de fichiers local.
1. Dans le portail Azure AI Foundry, dans votre projet, dans le volet de navigation de gauche, dans **Mes ressources**, sélectionnez la page **Données + index**.
1. Sélectionnez **+ Nouvelles données**.
1. Dans l’Assistant **Ajouter vos données**, développez le menu déroulant pour sélectionner **Charger des fichiers/dossiers**.
1. Sélectionnez **Charger un dossier**, puis chargez le dossier **brochures**. Attendez que tous les fichiers du dossier soient répertoriés.
1. Sélectionnez **Suivant** et définissez le nom des données sur `brochures`.
1. Attendez que le dossier soit chargé et notez qu’il contient plusieurs fichiers .pdf.

## Créer un index pour vos données

Maintenant que vous avez ajouté une source de données à votre projet, vous pouvez l’utiliser pour créer un index dans votre ressource Recherche Azure AI.

1. Dans le portail Azure AI Foundry, dans votre projet, dans le volet de navigation de gauche, dans **Mes ressources**, sélectionnez la page **Données + index**.
1. Dans l’onglet **Index**, ajoutez un nouvel index avec les paramètres suivants :
    - **Emplacement source** :
        - **Source de données** : données dans Azure AI Foundry
            - *Sélectionnez la source de données **brochures***
    - **Configuration de l’index** :
        - **Sélectionnez le service Recherche Azure AI** : *créez une ressource Recherche Azure AI avec les paramètres suivants* :
            - **Abonnement** : *votre abonnement Azure*
            - **Groupe de ressources** : *le même groupe de ressources que pour votre Hub IA*
            - **Nom du service** : *un nom valide pour votre ressource de recherche IA*
            - **Emplacement** : *le même emplacement que votre Hub IA*
            - **Niveau tarifaire** : De base
            
            Attendez que la ressource Recherche IA soit créée. Revenez ensuite dans Azure AI Foundry et terminez la configuration de l’index en sélectionnant **Connecter une autre ressource Recherche Azure AI**, puis en ajoutant une connexion à la ressource Recherche IA que vous venez de créer.
 
        - **Index vectoriel** : `brochures-index`
        - **Machine virtuelle** : Sélection automatique
    - **Paramètres de recherche** :
        - **Paramètres de vecteur** : Ajouter la recherche vectorielle à cette ressource de recherche
        - **Connexion Azure OpenAI** : *Sélectionner la ressource Azure OpenAI par défaut pour votre hub*
        - **Modèle d’incorporation** : text-embedding-ada-002
        - **Déploiement du modèle d’incorporation** : *votre déploiement du* *modèle* text-embedding-ada-002

1. Créez l’index vectoriel et attendez que le processus d‘indexation soit terminé, ce qui peut prendre un certain temps en fonction des ressources de calcul disponibles dans votre abonnement.

    L’opération de création d’index est constituée des travaux suivants :

    - Déchiffrez, segmentez et incorporez des jetons de texte dans les données de vos brochures.
    - Créez l’index Recherche Azure AI.
    - Inscrivez la ressource d’index.

    > **Conseil** : pendant que vous attendez que l’index soit créé, pourquoi ne pas examiner les brochures que vous avez téléchargées pour vous familiariser avec leur contenu ?

## Tester l‘index dans le terrain de jeu

Avant d’utiliser votre index dans un flux d’invite basé sur RAG, nous allons vérifier qu’il peut être utilisé pour affecter les réponses de l’IA générative.

1. Dans le volet de navigation de gauche, sélectionnez la page **Terrains de jeu**, puis ouvrez le terrain de jeu **Conversation**.
1. Dans la page du terrain de jeu Conversation, dans le volet Configuration, vérifiez si votre modèle de déploiement **gpt-4o** est sélectionné. Ensuite, dans le panneau de session de conversation instantanée, soumettez l’invite `Where can I stay in New York?`
1. Examinez la réponse, qui doit être une réponse générique du modèle sans aucune donnée de l’index.
1. Dans le volet Configuration, développez le champ **Ajouter vos données**, puis ajoutez l’index du projet **brochures-index**, puis sélectionnez le type de recherche **hybride (vecteur + mot clé)**.

   > **Conseil** : dans certains cas, les index nouvellement créés peuvent ne pas être disponibles de suite. Actualiser le navigateur permet généralement de résoudre le problème, mais si cela ne suffit pas, vous devrez peut-être attendre que l’index soit reconnu.

1. Une fois l’index ajouté et la session de conversation redémarrée, soumettez à nouveau l’invite `Where can I stay in New York?`
1. Examinez la réponse, qui doit être basée sur les données de l’index.

<!-- DEPRECATED STEPS

## Create a RAG client app with the Azure AI Foundry and Azure OpenAI SDKs

Now that you have a working index, you can use the Azure AI Foundry and Azure OpenAI SDKs to implement the RAG pattern in a client application. Let's explore the code to accomplish this in a simple example.

> **Tip**: You can choose to develop your RAG solution using Python or Microsoft C#. Follow the instructions in the appropriate section for your chosen language.

### Prepare the application configuration

1. In the Azure AI Foundry portal, view the **Overview** page for your project.
1. In the **Project details** area, note the **Project connection string**. You'll use this connection string to connect to your project in a client application.
1. Return to the browser tab containing the Azure portal (keeping the Azure AI Foundry portal open in the existing tab).
1. Use the **[\>_]** button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a ***PowerShell*** environment with no storage in your subscription.

    The cloud shell provides a command-line interface in a pane at the bottom of the Azure portal. You can resize or maximize this pane to make it easier to work in.

    > **Note**: If you have previously created a cloud shell that uses a *Bash* environment, switch it to ***PowerShell***.

1. In the cloud shell toolbar, in the **Settings** menu, select **Go to Classic version** (this is required to use the code editor).

    **<font color="red">Ensure you've switched to the classic version of the cloud shell before continuing.</font>**

1. In the cloud shell pane, enter the following commands to clone the GitHub repo containing the code files for this exercise (type the command, or copy it to the clipboard and then right-click in the command line and paste as plain text):

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Tip**: As you paste commands into the cloudshell, the output may take up a large amount of the screen buffer. You can clear the screen by entering the `cls` command to make it easier to focus on each task.

1. After the repo has been cloned, navigate to the folder containing the chat application code files:

    > **Note**: Follow the steps for your chosen programming language.

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/c-sharp
    ```

1. In the cloud shell command-line pane, enter the following command to install the libraries you'll use:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-ai-projects azure-identity openai
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI --prerelease
    ```
    

1. Enter the following command to edit the configuration file that has been provided:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    The file is opened in a code editor.

1. In the code file, replace the following placeholders: 
    - **your_project_connection_string**: Replace with the connection string for your project (copied from the project **Overview** page in the Azure AI Foundry portal).
    - **your_gpt_model_deployment** Replace with the name you assigned to your **gpt-4o** model deployment.
    - **your_embedding_model_deployment**: Replace with the name you assigned to your **text-embedding-ada-002** model deployment.
    - **your_index**: Replace with your index name (which should be `brochures-index`).
1. After you've replaced the placeholders, in the code editor, use the **CTRL+S** command or **Right-click > Save** to save your changes and then use the **CTRL+Q** command or **Right-click > Quit** to close the code editor while keeping the cloud shell command line open.

### Explore code to implement the RAG pattern

1. Enter the following command to edit the code file that has been provided:

    **Python**

    ```
   code rag-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Review the code in the file, noting that it:
    - Uses the Azure AI Foundry SDK to connect to your project (using the project connection string)
    - Creates an authenticated Azure OpenAI client from your project connection.
    - Retrieves the default Azure AI Search connection from your project so it can determine the endpoint and key for your Azure AI Search service.
    - Creates a suitable system message.
    - Submits a prompt (including the system and a user message based on the user input) to the Azure OpenAI client, adding:
        - Connection details for the Azure AI Search index to be queried.
        - Details of the embedding model to be used to vectorize the query\*.
    - Displays the response from the grounded prompt.
    - Adds the response to the chat history.

    \* *The query for the search index is based on the prompt, and is used to find relevant text in the indexed documents. You can use a keyword-based search that submits the query as text, but using a vector-based search can be more efficient - hence the use of an embedding model to vectorize the query text before submitting it.*

1. Use the **CTRL+Q** command to close the code editor without saving any changes, while keeping the cloud shell command line open.

### Run the chat application

1. In the cloud shell command-line pane, enter the following command to run the app:

    **Python**

    ```
   python rag-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. When prompted, enter a question, such as `Where should I go on vacation to see architecture?` and review the response from your generative AI model.

    Note that the response includes source references to indicate the indexed data in which the answer was found.

1. Try a follow-up question, for example `Where can I stay there?`

1. When you're finished, enter `quit` to exit the program. Then close the cloud shell pane.

-->

## Nettoyage

Pour éviter des coûts Azure inutiles et une utilisation non nécessaire des ressources, vous devez supprimer les ressources que vous avez déployées dans cet exercice.

1. Si vous avez terminé d’explorer Azure AI Foundry, revenez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous si nécessaire en utilisant vos informations d’identification Azure. Supprimez ensuite les ressources dans le groupe de ressources où vous avez provisionné vos ressources Recherche Azure AI et Azure AI.
