---
lab:
  title: Créer une application d’IA générative qui utilise vos propres données
  description: Découvrez comment utiliser le modèle RAG (génération augmentée de récupération) pour créer une application de conversation qui déclenche des invites à l’aide de vos propres données.
---

# Créer une application d’IA générative qui utilise vos propres données

La génération augmentée e récupération (RAG, Retrieval Augmented Generation) est une technique utilisée pour créer des applications qui intègrent des données provenant de sources de données personnalisées dans une invite pour un modèle d’IA générative. La RAG est un modèle couramment utilisé pour développer des applications d’IA générative, qui sont des applications basées sur la conversation instantanée utilisant un modèle de langage pour interpréter les entrées et générer des réponses appropriées.

Dans cet exercice, vous allez utiliser le portail Azure AI Foundry, ainsi que les SDK Azure AI Foundry et Azure OpenAI pour intégrer des données personnalisées dans une application d’IA générative.

Cet exercice prend environ **45** minutes.

> **Remarque** : cet exercice est basé sur des SDK en préversion, susceptibles d’être modifiés. Le cas échéant, nous avons utilisé des versions spécifiques de certains packages, qui ne correspondent pas forcément aux versions les plus récentes disponibles. Un comportement inattendu, des avertissements ou des erreurs peuvent se produire.

## Créer un projet Azure AI Foundry

Commençons par créer un projet Azure AI Foundry et les ressources de service dont il a besoin pour prendre en charge l’utilisation de vos propres données, y compris une ressource Recherche Azure AI.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les conseils ou les volets de démarrage rapide ouverts la première fois que vous vous connectez et, si nécessaire, utilisez le logo **Azure AI Foundry** en haut à gauche pour accéder à la page d’accueil, qui ressemble à l’image suivante :

    ![Capture d’écran du portail Azure AI Foundry.](./media/ai-foundry-home.png)

1. Sur la page d’accueil, sélectionnez **+Créer un projet**.
1. Dans l’assistant **Créer un projet**, saisissez un nom valide et, si un hub existant est suggéré, choisissez l’option permettant d’en créer un. Passez ensuite en revue les ressources Azure qui seront créées automatiquement pour prendre en charge votre hub et votre projet.
1. Sélectionnez **Personnaliser** et spécifiez les paramètres suivants pour votre hub :
    - **Nom du hub** : *nom valide pour votre hub*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *créez ou sélectionnez un groupe de ressources*
    - **Emplacement** : sélectionnez **Aidez-moi à choisir**, puis sélectionnez **gpt-4o** dans la fenêtre d’aide à la sélection de l’emplacement et utilisez la région recommandée\*.
    - **Connecter Azure AI Services ou Azure OpenAI** : *créer des ressources AI Services*
    - **Connecter Recherche Azure AI** : *créer une ressource Recherche Azure AI avec un nom unique*

    > \*Les ressources Azure OpenAI sont soumises à des quotas de modèle par région. En cas de dépassement de quota au cours de l’exercice, vous devrez peut-être créer une autre ressource dans une région différente.

1. Sélectionnez **Suivant** et passez en revue votre configuration. Sélectionnez **Créer** et patientez jusqu’à ce que l’opération se termine.
1. Une fois votre projet créé, fermez les conseils affichés et passez en revue la page du projet dans le portail Azure AI Foundry, qui doit ressembler à l’image suivante :

    ![Capture d’écran des détails d’un projet Azure AI dans le portail Azure AI Foundry.](./media/ai-foundry-project.png)

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
        - **Sélectionnez Service Recherche Azure AI** : *Sélectionnez la connexion **AzureAISearch** à votre ressource Recherche Azure AI*
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

## Créer une application cliente RAG avec les kits SDK Azure AI Foundry et Azure OpenAI

Maintenant que vous disposez d‘un index de travail, vous pouvez utiliser les kits SDK Azure AI Foundry et Azure OpenAI pour implémenter le modèle RAG dans une application cliente. Examinons le code pour y parvenir dans un exemple simple.

> **Conseil** : vous pouvez choisir de développer votre solution RAG à l’aide de Python ou de Microsoft C#. Suivez les instructions de la section appropriée pour votre langue choisie.

### Préparer la configuration de l’application

1. Dans le portail Azure AI Foundry, affichez la page **Vue d’ensemble** de votre projet.
1. Dans la zone **Détails du projet**, notez la **chaîne de connexion du projet**. Vous utiliserez cette chaîne de connexion pour vous connecter à votre projet dans une application cliente.
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

    > **Conseil** : lorsque vous collez des commandes dans Cloud Shell, la sortie peut prendre une grande quantité de mémoire tampon d’écran. Vous pouvez effacer le contenu de l’écran en saisissant la commande `cls` pour faciliter le focus sur chaque tâche.

1. Une fois le référentiel cloné, accédez au dossier contenant les fichiers de code de l’application de conversation :

    > **Note** : suivez les étapes de votre langage de programmation choisi.

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/c-sharp
    ```

1. Dans le volet de ligne de commande Cloud Shell, saisissez la commande suivante pour installer les bibliothèques que vous allez utiliser :

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
    - **your_project_connection_string** : remplacez par la chaîne de connexion de votre projet (copiée à partir de la page **Vue d’ensemble** du projet dans le portail Azure AI Foundry).
    - **your_gpt_model_deployment** :remplacez par le nom que vous avez affecté à votre déploiement de modèle **gpt-4o**.
    - **your_embedding_model_deployment** :remplacez par le nom que vous avez affecté à votre déploiement de modèle **text-embedding-ada-002**.
    - **your_index** : remplacez par votre nom d’index (qui doit être `brochures-index`).
1. Une fois que vous avez remplacé les espaces réservés, dans l’éditeur de code, utilisez la commande **Ctrl+S** ou **Clic droit > Enregistrer** pour enregistrer vos modifications, puis utilisez la commande **Ctrl+Q** ou **Clic droit > Quitter** pour fermer l’éditeur de code tout en gardant la ligne de commande Cloud Shell ouverte.

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
    - Crée un client Azure OpenAI authentifié à partir de votre connexion de projet
    - Récupère la connexion Recherche Azure AI par défaut à partir de votre projet afin qu’il puisse déterminer le point de terminaison et la clé de votre service Recherche Azure AI
    - Crée un message système approprié
    - Envoie une invite (y compris le système et un message utilisateur basé sur l’entrée utilisateur) au client Azure OpenAI, en ajoutant :
        - Les détails de connexion de l’index Recherche Azure IA à interroger
        - Les détails du modèle d’intégration à utiliser pour vectoriser la requête\*
    - Affiche la réponse à partir de l’invite ancrée
    - Ajoute la réponse à l’historique des conversations

    \**La requête de l’index de recherche est basée sur l’invite et est utilisée pour rechercher du texte pertinent dans les documents indexés. Vous pouvez utiliser une recherche basée sur des mots clés qui soumet la requête en tant que texte, mais l’utilisation d’une recherche vectorielle peut être plus efficace, d’où l’utilisation d’un modèle d’intégration pour vectoriser le texte de la requête avant de l’envoyer.*

1. Utilisez la commande **Ctrl+Q** pour fermer l’éditeur de code sans enregistrer les modifications, tout en gardant la ligne de commande Cloud Shell ouverte.

### Exécutez l’application de conversation

1. Dans le volet en ligne de commande du Cloud Shell, saisissez la commande suivante pour exécuter l’application :

    **Python**

    ```
   python rag-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Lorsque vous y êtes invité, entrez une question, par exemple `Where should I go on vacation to see architecture?`, et passez en revue la réponse de votre modèle d’IA générative.

    Notez que la réponse inclut des références sources pour indiquer les données indexées dans lesquelles la réponse a été trouvée.

1. Essayez une question de suivi, par exemple `Where can I stay there?`

1. Lorsque vous avez terminé, entrez `quit` pour quitter le programme. Fermez le volet Cloud Shell.

## Nettoyage

Pour éviter des coûts Azure inutiles et une utilisation non nécessaire des ressources, vous devez supprimer les ressources que vous avez déployées dans cet exercice.

1. Si vous avez terminé d’explorer Azure AI Foundry, revenez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous si nécessaire en utilisant vos informations d’identification Azure. Supprimez ensuite les ressources dans le groupe de ressources où vous avez provisionné vos ressources Recherche Azure AI et Azure AI.
