---
lab:
  title: Créer une application de conversation d’IA générative
  description: "Découvrez comment utiliser le SDK Azure\_AI\_Foundry pour créer une application qui se connecte à votre projet et à vos conversations avec un modèle de langage."
---

# Créer une application de conversation d’IA générative

Dans cet exercice, vous utilisez le SDK Python Azure AI Foundry pour créer une application de chat simple qui se connecte à un projet et communique avec un modèle linguistique.

> **Remarque** : Cet exercice est basé sur une version préliminaire du logiciel SDK, qui est susceptible d'être modifiée. Le cas échéant, nous avons utilisé des versions spécifiques de certains packages, qui ne correspondent pas forcément aux versions les plus récentes disponibles. Il se peut que vous rencontriez des comportements, inattendus, des avertissements ou des erreurs.

Bien que cet exercice soit basé sur le SDK Python Azure AI Foundry, vous pouvez développer des applications de chat IA à l'aide de plusieurs SDK spécifiques à chaque langage, notamment :

- [Projets Azure AI pour Python](https://pypi.org/project/azure-ai-projects)
- [Projets Azure AI pour Microsoft .NET](https://www.nuget.org/packages/Azure.AI.Projects)
- [Projets Azure AI pour JavaScript](https://www.npmjs.com/package/@azure/ai-projects)

Cet exercice prend environ **40** minutes.

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
    - **Région** : *Sélectionnez n’importe quelle **recommandation d’AI Foundry***\*

    > \* Certaines ressources Azure AI sont limitées par des quotas de modèles régionaux. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région.

1. Sélectionnez **Créer** et attendez que votre projet soit créé. Si vous y êtes invité, déployez le modèle gpt-4o en utilisant le type de déploiement **norme Global**, puis personnalisez les détails du déploiement pour définir une **limite de taux de jetons par minute** de 50 000 (ou le maximum disponible s'il est inférieur à 50 000).

    > **Remarque** : La réduction du nombre de jetons par minute permet d’éviter une surutilisation du quota disponible dans l’abonnement que vous utilisez. 50 000 jetons par minute sont suffisants pour les données utilisées dans cet exercice. Si votre quota disponible est inférieur à cette valeur, vous pourrez tout de même terminer l’exercice, mais vous pourriez rencontrer des erreurs en cas de dépassement de la limite.

1. Une fois le projet créé, le terrain de jeu de conversation instantanée s’ouvrira automatiquement pour vous permettre de tester votre modèle :
1. Dans le volet **Configuration**, notez le nom de votre modèle de déploiement ; il devrait s’agir de **gpt-4o**. Vous pouvez le confirmer en affichant le déploiement dans la page **Modèles et points de terminaison** (ouvrez simplement cette page dans le volet de navigation à gauche).
1. Dans le volet de navigation à gauche, sélectionnez **Vue d’ensemble** pour accéder à la page principale de votre projet ; elle se présente comme suit :

    ![Capture d’écran d’une page de présentation d’un projet Azure AI Foundry.](./media/ai-foundry-project.png)

## Créer une application cliente pour converser avec le modèle

Maintenant que vous avez déployé un modèle, vous pouvez utiliser Azure AI Foundry et les SDK Azure OpenAI pour développer une application qui communique avec lui.

### Préparer la configuration de l’application

1. Dans le portail Azure AI Foundry, affichez la page **Vue d’ensemble** de votre projet.
1. Dans la zone **Points de terminaison et clés**, assurez-vous que la bibliothèque **Azure AI Foundry** est sélectionnée et affichez le **point de terminaison du projet Azure AI Foundry**. Vous utiliserez ce point de terminaison pour vous connecter à votre projet et à votre modèle dans une application cliente.

    > **Remarque** : Vous pouvez également utiliser le point de terminaison Azure OpenAI !

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

1. Une fois le référentiel cloné, accédez au dossier contenant les fichiers de code de l'application de chat et affichez-les :

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/python
   ls -a -l
    ```

    Le dossier contient un fichier de code ainsi qu'un fichier de configuration pour les paramètres de l'application et un fichier définissant le runtime du projet et les exigences du package.

1. Dans le volet de ligne de commande Cloud Shell, saisissez la commande suivante pour installer les bibliothèques que vous allez utiliser :

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-identity azure-ai-projects openai
    ```
1. Saisissez la commande suivante pour modifier le fichier de configuration fourni :

    ```
   code .env
    ```

    Le fichier s’ouvre dans un éditeur de code.

1. Dans le fichier de code, remplacez l’espace réservé **your_project_endpoint** avec le **point de terminaison du projet Azure AI Foundry** pour votre projet (copié depuis la page **Aperçu** dans le portail Azure AI Foundry) ; et le paramètre fictif **your_model_deployment** avec le nom de votre déploiement de modèle gpt-4.
1. Une fois que vous avez remplacé les espaces réservés, utilisez la commande **CTRL+S** ou **Clic droit > Enregistrer** dans l’éditeur de code pour enregistrer vos modifications, puis utilisez la commande **CTRL+Q** ou **Clic droit > Quitter** pour fermer l’éditeur tout en gardant la ligne de commande du Cloud Shell ouverte.

### Écrire du code pour vous connecter à votre projet et converser avec votre modèle

> **Conseil** : lorsque vous ajoutez du code, veillez à conserver la mise en retrait correcte.

1. Saisissez la commande suivante pour modifier le fichier de code fourni :

    ```
   code chat-app.py
    ```

1. Dans le fichier de code, notez les instructions existantes qui ont été ajoutées en haut du fichier pour importer les espaces de noms SDK nécessaires. Ensuite, repérez le commentaire **Ajouter des références**, puis ajoutez le code suivant pour référencer les espaces de noms des bibliothèques que vous avez installées précédemment :

    ```python
   # Add references
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from openai import AzureOpenAI
    ```

1. Dans la fonction **main**, sous le commentaire **Obtenir les paramètres de configuration**, notez que le code charge les valeurs de chaîne de connexion du projet et de nom de déploiement du modèle que vous avez définies dans le fichier de configuration.
1. Recherchez le commentaire **Initialiser le client du projet** et ajoutez le code suivant pour vous connecter à votre projet Azure AI Foundry :

    > **Conseil** : veillez à respecter le niveau d’indentation correct dans votre code.

    ```python
   # Initialize the project client
   project_client = AIProjectClient(            
            credential=DefaultAzureCredential(
                exclude_environment_credential=True,
                exclude_managed_identity_credential=True
            ),
            endpoint=project_endpoint,
        )
    ```

1. Recherchez le commentaire **Obtenir un client de conversation instantanée**, puis ajoutez le code suivant pour créer un objet client permettant d’interagir avec un modèle :

    ```python
   # Get a chat client
   openai_client = project_client.get_openai_client(api_version="2024-10-21")
    ```

1. Trouvez le commentaire : **Initialiser l’invite avec le message système**, puis ajoutez le code suivant pour initialiser une collection de messages avec une invite système.

    ```python
   # Initialize prompt with system message
   prompt = [
            {"role": "system", "content": "You are a helpful AI assistant that answers questions."}
        ]
    ```

1. Notez que le code inclut une boucle pour permettre à un utilisateur d’entrer une invite jusqu’à ce qu’il entre « quitter ». Ensuite, dans la section de la boucle, trouvez le commentaire : **Obtenir une complétion de conversation**, puis ajoutez le code suivant pour ajouter l’entrée de l’utilisateur à l’invite, récupérer la complétion générée par votre modèle, et ajouter cette complétion à l’invite (afin de conserver l’historique de la conversation pour les itérations suivantes) :

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

1. Utilisez la commande **Ctrl+S** pour enregistrer les modifications que vous avez apportées au fichier de code.

### Se connecter à Azure et exécuter l’application

1. Dans le volet de la ligne de commande Cloud Shell, entrez la commande suivante pour vous connecter à Azure.

    ```
   az login
    ```

    **<font color="red">Vous devez vous connecter à Azure, même si la session Cloud Shell est déjà authentifiée.</font>**

    > **Remarque** :dans la plupart des scénarios, l’utilisation d’*az login* suffit. Toutefois, si vous avez des abonnements dans plusieurs locataires, vous devrez peut-être spécifier le locataire à l’aide du paramètre *--tenant*. Pour plus d’informations, consultez [Se connecter à Azure de manière interactive à l’aide d’Azure CLI](https://learn.microsoft.com/cli/azure/authenticate-azure-cli-interactively).
    
1. Lorsque l’invite apparaît, suivez les instructions pour ouvrir la page de connexion dans un nouvel onglet et entrez le code d’authentification fourni ainsi que vos informations d’identification Azure. Effectuez ensuite le processus de connexion dans la ligne de commande, en sélectionnant l’abonnement contenant votre hub Azure AI Foundry si nécessaire.
1. Une fois la connexion effectuée, entrez la commande suivante pour exécuter l’application :

    ```
   python chat-app.py
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
