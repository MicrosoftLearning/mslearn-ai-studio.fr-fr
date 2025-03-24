---
lab:
  title: Créer une application d’IA générative qui utilise vos propres données
  description: Découvrez comment utiliser le modèle RAG (génération augmentée de récupération) pour créer une application de conversation qui déclenche des invites à l’aide de vos propres données.
---

# Créer une application d’IA générative qui utilise vos propres données

La génération augmentée e récupération (RAG, Retrieval Augmented Generation) est une technique utilisée pour créer des applications qui intègrent des données provenant de sources de données personnalisées dans une invite pour un modèle d’IA générative. La RAG est un modèle couramment utilisé pour développer des applications d’IA générative, qui sont des applications basées sur la conversation instantanée utilisant un modèle de langage pour interpréter les entrées et générer des réponses appropriées.

Dans cet exercice, vous allez utiliser le portail Azure AI Foundry, ainsi que les SDK Azure AI Foundry et Azure OpenAI pour intégrer des données personnalisées dans une application d’IA générative.

Cet exercice prend environ **45** minutes.

## Créer un projet Azure AI Foundry

Commençons par créer un projet Azure AI Foundry et les ressources de service dont il a besoin pour prendre en charge l’utilisation de vos propres données, y compris une ressource Recherche Azure AI.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les conseils ou les volets de démarrage rapide ouverts la première fois que vous vous connectez et, si nécessaire, utilisez le logo **Azure AI Foundry** en haut à gauche pour accéder à la page d’accueil, qui ressemble à l’image suivante :

    ![Capture d’écran du portail Azure AI Foundry.](./media/ai-foundry-home.png)

1. Sur la page d’accueil, sélectionnez **+Créer un projet**.
1. Dans l‘Assistant **Création d‘un projet**, entrez un nom de projet approprié pour (par exemple, `my-ai-project`) et, si un hub existant est suggéré, choisissez l‘option permettant d‘en créer un. Passez ensuite en revue les ressources Azure qui seront créées automatiquement pour prendre en charge votre hub et votre projet.
1. Sélectionnez **Personnaliser** et spécifiez les paramètres suivants pour votre hub :
    - **Nom du hub** : *nom unique, par exemple `my-ai-hub`*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *créez un groupe de ressources avec un nom unique ( par exemple, `my-ai-resources`) ou sélectionnez un groupe de ressources existant.*
    - **Emplacement** : sélectionnez **Aidez-moi à choisir**, puis **gpt-4** et **text-embedding-ada-002** dans la fenêtre de l’assistant d’emplacement et utilisez la région recommandée.\*
    - **Connecter Azure AI Services ou Azure OpenAI** : *créer une ressource AI Services avec un nom approprié (par exemple, `my-ai-services`) ou utiliser une ressource existante*
    - **Connecter Recherche Azure AI** : *créer une ressource Recherche Azure AI avec un nom unique*

    > \* Les ressources Azure OpenAI sont limitées au niveau du locataire par quotas régionaux. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région.

1. Sélectionnez **Suivant** et passez en revue votre configuration. Sélectionnez **Créer** et patientez jusqu’à ce que l’opération se termine.
1. Une fois votre projet créé, fermez les conseils affichés et passez en revue la page **Vue d’ensemble** du projet dans le portail Azure AI Foundry, qui doit ressembler à l’image suivante :

    ![Capture d’écran des détails d’un projet Azure AI dans le portail Azure AI Foundry.](./media/ai-foundry-project.png)
   
## Déployer des modèles

Vous avez besoin de deux modèles pour implémenter votre solution :

- Un modèle *d’incorporation* pour vectoriser les données texte pour une indexation et un traitement efficaces.
- Un modèle qui peut générer des réponses en langage naturel aux questions en fonction de vos données.

1. Dans le portail Azure AI Foundry, dans votre projet, dans le volet de navigation de gauche, dans **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
1. Créez un déploiement du modèle **text-embedding-ada-002** avec les paramètres suivants en sélectionnant **Personnaliser** dans l’assistant Déployer le modèle :

    - **Nom du déploiement **: `text-embedding-ada-002`
    - **Type de déploiement** : Standard
    - **Version du modèle** : *Sélectionnez la version par défaut*
    -  **Ressource IA** : *sélectionnez la ressource que vous avez créée précédemment.*
    - **Limite de débit en jetons par minute (en milliers)** : 5 000
    - **Filtre de contenu** : DefaultV2
    - **Enable dynamic quota** : désactivé

    > **Remarque** : si votre emplacement actuel de ressource IA n’a pas de quota disponible pour le modèle que vous souhaitez déployer, vous êtes invité à choisir un autre emplacement où une nouvelle ressource IA sera créée et connectée à votre projet.

1. Répétez les étapes précédentes pour déployer un modèle **gpt-4** avec le nom de déploiement `gpt-4`.

    > **Remarque** : La réduction du nombre de jetons par minute (TPM) permet d’éviter une surutilisation du quota disponible dans l’abonnement que vous utilisez. 5 000 jetons par minute sont suffisants pour les données utilisées dans cet exercice.

## Ajouter des données dans votre projet

Les données de votre copilote sont constituées d’un ensemble de brochures de voyage au format PDF provenant de l’agence de voyage fictive *Margie’s Travel*. Ajoutons-les au projet.

1. Téléchargez l’[archive compressée des brochures](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) depuis `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` et décompressez-la dans un dossier nommé **brochures** sur votre système de fichiers local.
1. Dans le portail Azure AI Foundry, dans votre projet, dans le volet de navigation de gauche, dans **Mes ressources**, sélectionnez la page **Données + index**.
1. Sélectionnez **+ Nouvelles données**.
1. Dans l’Assistant **Ajouter vos données**, développez le menu déroulant pour sélectionner **Charger des fichiers/dossiers**.
1. Sélectionnez **Charger un dossier**, puis sélectionnez le dossier **brochures**.
1. Sélectionnez **Suivant** et définissez le nom des données sur `brochures`.
1. Attendez que le dossier soit chargé et notez qu’il contient plusieurs fichiers .pdf.

## Créer un index pour vos données

Maintenant que vous avez ajouté une source de données à votre projet, vous pouvez l’utiliser pour créer un index dans votre ressource Recherche Azure AI.

1. Dans le portail Azure AI Foundry, dans votre projet, dans le volet de navigation de gauche, dans **Mes ressources**, sélectionnez la page **Données + index**.
1. Dans l’onglet **Index**, ajoutez un nouvel index avec les paramètres suivants :
    - **Emplacement source** :
        - **Source de données** : données dans le portail Azure AI Foundry
            - *Sélectionnez la source de données **brochures***
    - **Configuration de l’index** :
        - **Sélectionnez Service Recherche Azure AI** : *Sélectionnez la connexion **AzureAISearch** à votre ressource Recherche Azure AI*
        - **Index vectoriel** : `brochures-index`
        - **Machine virtuelle** : Sélection automatique
    - **Paramètres de recherche** :
        - **Paramètres de vecteur** : Ajouter la recherche vectorielle à cette ressource de recherche
        - **Connexion Azure OpenAI** : *Sélectionner la ressource Azure OpenAI par défaut pour votre hub*

1. Attendez que le processus d‘indexation soit terminé, ce qui peut prendre un certain temps en fonction des ressources de calcul disponibles dans votre abonnement. L’opération de création d’index est constituée des travaux suivants :

    - Déchiffrez, segmentez et incorporez des jetons de texte dans les données de vos brochures.
    - Créez l’index Recherche Azure AI.
    - Inscrivez la ressource d’index.

## Tester l‘index dans le terrain de jeu

Avant d’utiliser votre index dans un flux d’invite basé sur RAG, nous allons vérifier qu’il peut être utilisé pour affecter les réponses de l’IA générative.

1. Dans le volet de navigation de gauche, sélectionnez la page **Terrains de jeu**, puis ouvrez le terrain de jeu **Conversation**.
1. Dans la page du terrain de jeu Conversation, dans le volet Configuration, vérifiez si votre modèle de déploiement **gpt-4** est sélectionné. Ensuite, dans le panneau de session de conversation instantanée, soumettez l’invite `Where can I stay in New York?`
1. Examinez la réponse, qui doit être une réponse générique du modèle sans aucune donnée de l’index.
1. Dans le volet Configuration, développez le champ **Ajouter vos données**, puis ajoutez l’index du projet **brochures-index**, puis sélectionnez le type de recherche **hybride (vecteur + mot clé)**.

   > **Conseil** : dans certains cas, les index nouvellement créés peuvent ne pas être disponibles de suite. Actualiser le navigateur permet généralement de résoudre le problème, mais si cela ne suffit pas, vous devrez peut-être attendre que l’index soit reconnu.

1. Une fois l’index ajouté et la session de conversation redémarrée, soumettez à nouveau l’invite `Where can I stay in New York?`
1. Examinez la réponse, qui doit être basée sur les données de l’index.

## Créer une application cliente RAG avec les kits SDK Azure AI Foundry et Azure OpenAI

Maintenant que vous disposez d‘un index de travail, vous pouvez utiliser les kits SDK Azure AI Foundry et Azure OpenAI pour implémenter le modèle RAG dans une application cliente. Examinons le code pour y parvenir dans un exemple simple.

> **Conseil** : vous pouvez choisir de développer votre solution RAG à l’aide de Python ou de Microsoft C#. Suivez les instructions de la section appropriée pour votre langue choisie.

### Préparer la configuration de l’application

1. Dans le portail Azure AI Foundry, affichez la page **Vue d’ensemble** de votre projet.
1. Dans la zone **Détails du projet**, notez la **chaîne de connexion du projet**. Vous utiliserez cette chaîne de connexion pour vous connecter à votre projet dans une application cliente.
1. Ouvrez un nouvel onglet de navigateur (en gardant le portail Azure AI Foundry ouvert dans l’onglet existant). Dans un nouvel onglet du navigateur, ouvrez le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.
1. Cliquez sur le bouton **[\>_]** à droite de la barre de recherche, en haut de la page, pour créer un environnement Cloud Shell dans le portail Azure, puis sélectionnez un environnement ***PowerShell***. Cloud Shell fournit une interface de ligne de commande dans un volet situé en bas du portail Azure.

    > **Remarque** : si vous avez déjà créé un Cloud Shell qui utilise un environnement *Bash*, basculez-le vers ***PowerShell***.

1. Dans la barre d’outils Cloud Shell, dans le menu **Paramètres**, sélectionnez **Accéder à la version classique** (cela est nécessaire pour utiliser l’éditeur de code).

    > **Conseil** : lorsque vous collez des commandes dans cloudshell, la sortie peut prendre une grande quantité de mémoire tampon d’écran. Vous pouvez effacer le contenu de l’écran en saisissant la commande `cls` pour faciliter le focus sur chaque tâche.

1. Dans le volet PowerShell, entrez les commandes suivantes pour cloner le référentiel GitHub pour cet exercice :

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

> **Note** : suivez les étapes de votre langage de programmation choisi.

1. Une fois le référentiel cloné, accédez au dossier contenant les fichiers de code de l’application de conversation :  

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/c-sharp
    ```

1. Dans le volet de ligne de commande Cloud Shell, saisissez la commande suivante pour installer les bibliothèques que vous utiliserez, c’est-à-dire :

    **Python**

    ```
   pip install python-dotenv azure-ai-projects azure-identity openai
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI --prerelease
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

1. Dans le fichier de code, remplacez les espaces réservés suivants : 
    - **your_project_endpoint** : remplacez par la chaîne de connexion de votre projet (copiée à partir de la page **Vue d’ensemble** du projet dans le portail Azure AI Foundry).
    - **your_model_deployment** :remplacez par le nom que vous avez affecté à votre déploiement de modèle (qui doit être `gpt-4`).
    - **your_index** : remplacez par votre nom d’index (qui doit être `brochures-index`).
1. Une fois que vous avez remplacé les espaces réservés, utilisez la commande **Ctrl+S** pour enregistrer vos modifications, puis utilisez la commande **Ctrl+Q** pour fermer l’éditeur de code tout en gardant la ligne de commande Cloud Shell ouverte.

### Explorer le code pour implémenter le modèle RAG

1. Saisissez la commande suivante pour modifier le fichier de code fourni :

    **Python**

    ```
   code rag-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Passez en revue le code dans le fichier, en notant qu’il :
    - Utilise le kit SDK Azure AI Foundry pour vous connecter à votre projet (à l’aide de la chaîne de connexion du projet)
    - Récupère la connexion Recherche Azure AI par défaut à partir de votre projet afin qu’il puisse déterminer le point de terminaison et la clé de votre service Recherche Azure AI
    - Crée un client Azure OpenAI authentifié basé sur la connexion de service Azure OpenAI par défaut dans votre projet
    - Envoie une invite (y compris un système et un message utilisateur) au client Azure OpenAI, en ajoutant des informations supplémentaires sur l’index Recherche Azure AI à utiliser pour ancrer l’invite
    - Affiche la réponse à partir de l’invite ancrée
1. Utilisez la commande **Ctrl+Q** pour fermer l’éditeur de code sans enregistrer les modifications, tout en gardant la ligne de commande Cloud Shell ouverte.

### Exécutez l’application de conversation

1. Dans le volet de ligne de commande Cloud Shell, entrez la commande suivante pour exécuter l’application :

    **Python**

    ```
   python rag-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Lorsque vous y êtes invité, entrez une question, par exemple `Where can I travel to?`, et passez en revue la réponse de votre modèle d’IA générative.

    Notez que la réponse inclut des références sources pour indiquer les données indexées dans lesquelles la réponse a été trouvée.

1. Essayez quelques questions supplémentaires, par exemple `Where should I stay in London?`.

    > **Note** : cet exemple d’application simple n’inclut aucune logique pour conserver l’historique des conversations. Chaque invite est donc traitée comme une nouvelle conversation.

1. Lorsque vous avez terminé, entrez `quit` pour quitter le programme. Fermez le volet Cloud Shell.

## Défi

Maintenant que vous avez découvert comment intégrer vos propres données dans une application d’IA générative créée avec le portail Azure AI Foundry, allons plus loin.

Essayez d’ajouter une nouvelle source de données via le portail Azure AI Foundry, indexez-la et intégrez les données indexées dans une application cliente. Voici quelques jeux de données que vous pouvez essayer :

- Une collection d’articles (de recherche) que vous avez sur votre ordinateur.
- Un ensemble de présentations de conférences passées.
- N’importe quels jeux de données disponibles dans le référentiel [Exemples de données Recherche Azure](https://github.com/Azure-Samples/azure-search-sample-data).

Testez votre solution en envoyant des invites qui ne pourraient obtenir une réponse que par le jeu de données que vous avez choisi.

## Nettoyage

Pour éviter des coûts Azure inutiles et une utilisation non nécessaire des ressources, vous devez supprimer les ressources que vous avez déployées dans cet exercice.

1. Si vous avez terminé d’explorer Azure AI Foundry, revenez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous si nécessaire en utilisant vos informations d’identification Azure. Supprimez ensuite les ressources dans le groupe de ressources où vous avez provisionné vos ressources Recherche Azure AI et Azure AI.
