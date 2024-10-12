---
lab:
  title: Créer un copilote personnalisé à l’aide d’outils de développement orientés code
---

# Créer un copilote personnalisé à l’aide d’outils de développement orientés code

Dans cet exercice, vous allez cloner et déployer un modèle Azure Developer CLI qui approvisionne et [déploie votre projet IA sur un point de terminaison en ligne](https://learn.microsoft.com/azure/developer/azure-developer-cli/azure-ai-ml-endpoints?WT.mc_id=academic-140829-cacaste) sur Azure AI Studio. Vous l’utiliserez ensuite comme base pour créer votre propre copilote personnalisé avec Azure AI et une expérience orientée code.

Cet exercice prend environ **90** minutes.

## Avant de commencer

Pour effectuer cet exercice, vous avez besoin des éléments suivants :

- Un compte GitHub pour dupliquer le référentiel de projet et le tester dans un environnement GitHub Codespaces. Créez un compte gratuit [sur GitHub](https://github.com/).
- Le niveau de base d’Azure AI Search pour activer le ranker sémantique. En savoir plus sur les [détails de la tarification AI Search](https://azure.microsoft.com/pricing/details/search/).
- Trois modèles OpenAI que vous devrez déployer (`gpt-35-turbo`, `gpt-4`, `text-embedding-ada-002`). Pour pouvoir déployer ces modèles, vous devez créer un hub d’IA dans une région dotée d’un quota suffisant. En savoir plus sur la [disponibilité des modèles par région](https://learn.microsoft.com/azure/ai-services/openai/concepts/models?WT.mc_id=academic-140829-cacaste#model-summary-table-and-region-availability).

## Présentation du scénario

Pour commencer à utiliser le modèle de projet IA Azure Developer CLI, accédez à la [collection Azure AI Templates with Azure Developer CLI](https://learn.microsoft.com/collections/5pq0uompdgje8d/?WT.mc_id=academic-140829-cacaste). En explorant la collection, vous trouverez plusieurs projets regroupés par technologie et cas d’usage, notamment des exemples de projets multimodaux et multi-agents, des projets de copilote et des exemples intégrant différents frameworks et services Azure.

Pour cet exercice, vous allez vous servir du modèle de projet de **[copilote Contoso Chat Retail avec Azure AI Studio & PromptFlow (Python)](https://aka.ms/contoso-retail-sample)** comme point de départ. Ce modèle de projet est une expérience orientée code qui utilise Prompty et PromptFlow pour créer un copilote personnalisé (IA de conversation) qui peut être intégré au site web de vente au détail (interface utilisateur de conversation) d’une société fictive appelée Contoso Outdoors.

![Interface utilisateur/expérience utilisateur de conversation Contoso](./media/contoso_outdoors_website.png)

La solution de copilote de vente au détail utilise un modèle de génération augmentée de récupération (RAG) pour baser les réponses sur les données produit et client de l’entreprise. Les clients peuvent poser au chatbot des questions sur le catalogue de produits de l’entreprise et obtenir des recommandations en fonction de leurs achats antérieurs.

En sélectionnant le lien du projet inclus dans la collection, vous êtes redirigé vers le référentiel GitHub hébergeant le code du modèle. Le fichier [README.md](https://github.com/Azure-Samples/contoso-chat/blob/main/README.md) dans le référentiel fournit une description détaillée du projet, notamment l’architecture, les prérequis et les étapes à suivre pour déployer le projet.

![Architecture de conversation Contoso](./media/contoso_chat_architecture.png)

## Configurer GitHub Codespaces

Dans cet exercice, vous allez utiliser [GitHub Codespaces](https://github.com/features/codespaces), une fonctionnalité GitHub qui vous permet de lancer un [conteneur de développement](https://docs.github.com/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration/introduction-to-dev-containers) hébergé dans le cloud, préconfiguré, directement à partir de votre référentiel, en un clic. De cette façon, vous pouvez rapidement démarrer le codage sans avoir à configurer votre environnement de développement local, car Codespaces dispose déjà de tous les outils et dépendances nécessaires préinstallés.

Pour initialiser votre environnement de développement, procédez comme suit :

1. **Dupliquez le référentiel** : sélectionnez le bouton **Dupliquer** dans le coin supérieur droit de la page du référentiel GitHub pour créer une copie du référentiel dans votre compte GitHub.
1. Une fois votre référentiel dupliqué, sélectionnez le bouton **Code** puis sélectionnez **Codespaces**.
1. Sélectionnez le bouton **+** pour créer un codespace sur la branche principale de votre référentiel dupliqué.

    ![Créer des codespaces GitHub](./media/create_codespaces.png)

1. Après quelques secondes, vous êtes redirigé vers un nouvel onglet de navigateur dans lequel l’environnement Codespaces est configuré avec un [éditeur Visual Studio Code attaché](https://code.visualstudio.com/docs/devcontainers/containers) par défaut. Vous pouvez continuer à travailler dans l’onglet du navigateur ou vous reconnecter aux codespaces en cours d’exécution à partir de votre éditeur Visual Studio Code local en cliquant sur le bouton **Ouvrir dans VS Code pour appareil de bureau** dans le menu supérieur gauche.

## Connecter l’environnement VS Code à Azure

L’étape suivante consiste à connecter votre environnement de développement local à l’abonnement Azure dans lequel vous souhaitez déployer le projet. Commencez par ouvrir un nouveau terminal dans votre IDE Visual Studio Code.

1. Tout d’abord, vérifiez que la [dernière version](https://github.com/Azure/azure-dev/releases/tag/azure-dev-cli_1.9.3) d’Azure Developer CLI est installée.
    ```bash
    azd version
    ```

1. Ensuite, connectez-vous à votre compte Azure à partir du terminal VS Code.

    ```bash
    azd auth login 
    ```

## Approvisionner des ressources Azure pour votre projet

Une fois connecté, vous êtes prêt à approvisionner les ressources Azure pour le projet dans votre abonnement. Vous pouvez le faire dans le même terminal VS Code que celui que vous avez utilisé pour la connexion.

1. Approvisionnez *et déployez* votre application IA à l’aide d’azd.

    ```bash
    azd up
    ```

1. Les invites suivantes doivent s’afficher. Répondez à l’aide des instructions ci-dessous :
    - **Entrer un nouveau nom d’environnement :***sert à définir le nom de votre groupe de ressources*.
    - **Sélectionner un abonnement Azure à utiliser** : *sélectionnez un abonnement qui a accès aux modèles Azure OpenAI*.
    - **Sélectionner un emplacement Azure à utiliser** : *sélectionnez un emplacement avec un quota de modèle disponible*.

    > Utilisez le [tableau récapitulatif des modèles et de leur disponibilité par région](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models?WT.mc_id=academic-140829-cacaste#model-summary-table-and-region-availability) pour trouver la région dont vous avez besoin. Par exemple, vous pouvez utiliser `sweden central` comme emplacement Azure, car il s’agit dune région où la majorité des modèles Azure OpenAI sont disponibles.

## Valider l’approvisionnement en utilisant le Portail Azure

L’approvisionnement et le déploiement d’une application IA à l’aide d’azd peuvent prendre 10 minutes ou plus. Vous pouvez suivre la progression en :

- Affichant la progression détaillée dans le [Portail Azure](https://ms.portal.azure.com/). Recherchez le groupe de ressources correspondant au nom de votre environnement. Sélectionnez l’option **Déploiements** dans la barre latérale, puis surveillez l’état du déploiement des ressources en cours de création.
- Visitant le portail [Azure AI Studio](https://ai.azure.com). Connectez-vous à l'aide de votre compte Azure. Recherchez le hub d’IA correspondant au groupe de ressources ci-dessus (vous devrez peut-être actualiser quelques fois). Sélectionnez le projet IA, puis sélectionnez **Déploiements** dans la barre latérale pour suivre l’état des modèles et des déploiements d’applications de conversation.

Examinons comment valider l’approvisionnement des ressources à l’aide du Portail Azure.

1. Dans le navigateur, accédez au [Portail Azure](https://ms.portal.azure.com/).
1. Connectez-vous et recherchez le groupe de ressources correspondant à l’abonnement et au nom de l’environnement que vous avez entrés précédemment. Le panneau **Vue d’ensemble** doit ressembler à ceci :

    ![Vue d’ensemble du groupe de ressources dans le Portail Azure](./media/azure-portal-resource-group.png)

1. Commençons par vérifier que les ressources clés d’[architecture Azure AI Studio](https://learn.microsoft.com/azure/ai-studio/concepts/architecture) ont été créées. L’illustration ci-dessous décrit en détails ce que chacune de ces ressources fournit à notre application d’IA.

    - **Hub Azure AI** : ressource Azure de niveau supérieur. Fournit un environnement de collaboration pour les équipes.
    - **Projet Azure AI** : enfant du hub. Regroupe les composants d’application pour l’orchestration et la personnalisation.
    - **Services Azure AI** : gère vos points de terminaison de modèle.

    ![Architecture d’Azure AI Studio](./media/resource-provider-connected-resources.svg)

1. Ensuite, vérifions que nous avons approvisionné deux ressources clés pour implémenter notre modèle de conception de [génération augmentée de récupération](https://learn.microsoft.com/azure/ai-studio/concepts/retrieval-augmented-generation) en stockant le produit et les données client pour la récupération pilotée par des requêtes.

    - **Service Search** : pour gérer les index de recherche pour nos données de catalogue de produits.
    - **Compte Azure Cosmos DB** : pour créer une base de données pour nos données de commande client.

1. Ensuite, nous pouvons vérifier que nous disposons des ressources nécessaires pour gérer nos besoins d’applications d’IA :

    - **Application Insights** : pour prendre en charge la surveillance et la télémétrie de l’application déployée.
    - **Registre de conteneurs** : pour stocker et gérer les images Docker utilisées dans le projet, de façon privée.
    - **Coffre de clés** : pour stocker en toute sécurité les secrets du projet (clés, informations d’identification).
    - **Compte de stockage** : pour stocker les données liées à la gestion de projets d’IA (y compris les journaux d’activité).
    - **Règle d’alerte de détecteur intelligent** : détecteur d’anomalies Application Insights (pour les requêtes).

1. Enfin, détail d’importance, vous remarquerez une nouvelle ressource de type **Déploiement en ligne Machine Learning**. Il s’agit de la ressource correspondant à notre point de terminaison de projet Azure AI déployé (pour le copilote de conversation).

## Valider le déploiement à l’aide d’Azure AI Studio

Le Portail Azure vous aide à gérer les ressources Azure sous-jacentes pour votre projet. Le portail Azure AI Studio vous permet de *créer et de gérer* les projets d’IA eux-mêmes, de bout en bout, de la sélection du modèle au déploiement d’applications. La commande `azd up` doit avoir permis de terminer l’intégralité du processus, de l’approvisionnement des modèles requis au déploiement et à l’hébergement du point de terminaison de l’API copilote à des fins d’utilisation. Vérifions que l’application fonctionne comme prévu.

1. Visitez la page **Gérer** dans [Azure AI Studio](https://ai.azure.com/manage) pour afficher tous les hubs Azure AI dans votre abonnement.
1. Sélectionnez le hub de votre groupe de ressources pour afficher tous les projets Azure AI qu’il contient.
1. Sélectionnez le projet IA par défaut dans le hub, puis sélectionnez **Déploiements** dans le menu de gauche.
1. Sous **Déploiements de modèles**, vérifiez que vous disposez d’une connexion Azure OpenAI comprenant les déploiements suivants :
    - **gpt-35-turbo** : utilisé pour la saisie semi-automatique, formant le moteur de conversation principal.
    - **gpt-4** : utilisé pour l’évaluation des conversations, en particulier les flux assistés par l’IA.
    - **text-embedding-ada-002** : utilisé pour la vectorisation de requête et la recherche.
1. Vérifiez que vous disposez d’un point de terminaison en ligne Machine Learning avec :
    - **chat-model** : déploiement d’IA de conversation avec la ressource de point de terminaison *mloe-xxx*.

    ![Déploiements de projets Azure AI](./media/azure-ai-project-deployment.png)

## Tester le déploiement (dans le cloud) à l’aide d’Azure AI Studio

Pour vérifier que le copilote déployé fonctionne, utilisez la fonctionnalité de terrain de jeu de test intégrée dans Azure AI Studio.

![Détails du déploiement de conversation](./media/chat-deployment-details.png)

1. Dans Azure AI Studio, dans la liste **Déploiements d’applications**, sélectionnez le déploiement **chat-deployment-xxxx**.
1. Dans la page **Détails** de l’application de conversation déployée, sélectionnez l’onglet **Test** pour obtenir l’interface de test.

    Notez que l’onglet **Détails** comporte également les valeurs `Target URI` et `Key` que vous pouvez utiliser avec d’autres applications front-end (par exemple, le site web Contoso Outdoor) afin d’intégrer cet assistant de conversation pour des interactions utilisateur réelles.

1. Pour le moment, testez le déploiement de copilote avec l’**entrée** de test suivante :

    ```bash
    {"question": "tell me about your hiking shoes", "customerId": "2", "chat_history": []}
    ```

Vous devriez obtenir une réponse JSON valide dans le composant de sortie, comme indiqué.

![Test de déploiement de conversation](./media/chat-deployment-test.png)

## Tester le déploiement (localement) à l’aide de Visual Studio Code

La commande **azd up** approvisionne et déploie l’application sur Azure ; elle *configure également votre environnement local* dans Visual Studio Code pour prendre en charge le développement, le test et l’itération locaux. Voyons ça.

1. Tout d’abord, vérifiez que votre environnement VS Code a été correctement configuré. Recherchez un fichier **config.json** dans le dossier racine et vérifiez qu’il possède les trois propriétés définies ci-dessous, avec des valeurs valides.

    ```json
    {
        "subscription_id": "xxxxxxxxxxxxxxxx",
        "resource_group": "rg-xxxxxx",
        "workspace_name": "ai-project-xxxxxxx"
    }

    ```

1. Vérifiez qu’un fichier **.env** a été créé dans votre dossier racine. Il doit contenir une liste de variables d’environnement *avec des valeurs pré-renseignées*.

    ```bash
    AZUREAI_HUB_NAME=
    AZUREAI_PROJECT_NAME=
    AZURE_CONTAINER_REGISTRY_ENDPOINT=
    AZURE_CONTAINER_REGISTRY_NAME=
    AZURE_COSMOS_NAME=
    AZURE_ENV_NAME=
    AZURE_KEY_VAULT_ENDPOINT=
    AZURE_KEY_VAULT_NAME=
    AZURE_LOCATION=
    AZURE_OPENAI_API_VERSION=
    AZURE_OPENAI_CHAT_DEPLOYMENT=
    AZURE_OPENAI_ENDPOINT=
    AZURE_OPENAI_NAME=
    AZURE_RESOURCE_GROUP=
    AZURE_SEARCH_ENDPOINT=
    AZURE_SEARCH_NAME=
    AZURE_SUBSCRIPTION_ID=
    AZURE_TENANT_ID=
    COSMOS_ENDPOINT=
    ```

1. Vérifiez que les **outils Promptflow** sont installés dans votre environnement de développement.

    ```bash
    pf version
    ```

1. Utilisez l’outil de **test de flux pf** pour tester l’application de flux flexible **contoso_chat** localement, avec l’exemple de question ci-dessous. Faites attention à la syntaxe de la commande pour transmettre les entrées :

    ```bash
    pf flow test --flow ./contoso_chat --inputs question="tell me about your jackets" customerId="3" chat_history=[]
    ```

La réponse doit ressembler à ceci :

![Exemple de sortie d’application](./media/example_app_output.png)

### Afficher le suivi (localement) à l’aide de Visual Studio Code

1. Vous pouvez suivre les détails de votre exécution avec l’indicateur `--ui` , comme indiqué ci-dessous.

    ```bash
    pf flow test --flow ./contoso_chat --inputs question="tell me about your jackets" customerId="3" chat_history=[] --ui
    ```

Cette commande doit afficher une **vue de suivi** dans votre navigateur (dans un nouvel onglet), sous forme d’un tableau comportant des informations détaillées sur cette exécution de test, notamment l’utilisation de la latence et des jetons.

![ligne de test pf](./media/pf-flow-test-row.png)

1. Sélectionnez l’enregistrement à développer dans une vue de suivi plus détaillée, qui vous permettra d’inspecter tous les détails du flux, des données brutes (entrée, sortie) aux étapes individuelles du flux et aux composants concernés (par exemple, les modèles d’invite utilisés pour LLM).

![détail du test pf](./media/pf-flow-test-detail.png)

## Comprendre la codebase de conversation Contoso

Votre back-end Azure est approvisionné et prêt. Votre environnement de développement local est configuré pour fonctionner avec votre back-end Azure. À présent, vous devez commencer à modifier le contenu pour personnaliser et redéployer votre propre version de l’application. Examinons rapidement comment la codebase est structurée.

> Ceci est une **liste simplifiée** du référentiel, dont sont absents certains fichiers et dossiers pour plus de clarté.

```bash
data/
    customer_info/  
        create-cosmos-db.ipynb      # Run notebook to upload data to Cosmos DB
        customer_info_1.json        # Example Customer info and orders file
        customer_info_2.json 
        ...
        ...
    product_info/   
        create-azure-search.ipynb   # Run notebook to index product data in AI Search
        products.csv                # Example Products data file

contoso_chat/                       # Main folder for application content
    ai_search.py                    # Search retrieval tool (for RAG design)
    chat.json                       # Example chat file (for Prompty template)
    chat.prompty                    # Chat asset (using Prompty format)
    chat_request.py                 # LLM request tool (for chat completion)
    flow.flex.yaml                  # Promptflow flex flow (define entry point)
    requirements.txt                # App dependencies (define runtime environment)

azure.yaml                          # Main configuration file for Azure Developer CLI  
infra/      
    ai.yaml                         # Define AI model deployments
    app/                            # Infrastructure-as-code config specific to app
    core/                           # Infrastructure-as-code config for core resources
    hooks/                          # Contains post-provisioning scripts
    main.bicep                      # Entry point for Bicep template used by azd
deployment/                         # ai.endpoint config files (named in azure.yaml)
    chat-deployment.yaml 
    chat-model.yaml  
    environment.yaml  

requirements.txt
```

Pour personnaliser le code :

- Si vous apportez des modifications à l’application (dans `contoso_chat/`) exécutez simplement `azd deploy` pour redéployer l’application vers le serveur principal précédemment approvisionné. Aucune nouvelle mise en service ou aucune procédure d’intervention manuelle supplémentaire n’est requise.
- Si vous apportez des modifications à la ressource (dans le dossier `infra/`), exécutez `azd up` pour réapprovisionner et redéployer l’application. Cela devrait permettre de récupérer automatiquement vos valeurs de configuration antérieures dans `.azure/` et de les modifier.

## Facultatif : personnaliser et redéployer le copilote

Il est temps de construire votre propre copilote personnalisé. Voici quelques options à étudier pour vous faire la main.

N’oubliez pas, quelle que soit l’option choisie, de :

- Utiliser `azd deploy` pour redéployer votre application si vous avez modifié uniquement le code de l’application.
- Utiliser `azd up` pour réapprovisionner et redéployer l’application si vous avez modifié la configuration des ressources.

### Personnaliser les données de l’historique des commandes et des clients

1. Étudiez les exemples de données sous **data/customer_info** pour vous faire une idée du schéma par défaut.
1. Explorez le notebook **data/create-cosmos-db.ipynb** pour une approche orientée code des mises à jour des données.
1. **Modifiez** les exemples de données et **exécutez** le notebook pour modifier la base de données Azure CosmosDB par défaut.
1. **Redéployez** l’application. Saisissez une question de test pour vérifier que les nouvelles données client sont bien renvoyées.

### Personnaliser les données du catalogue de produits

1. Étudiez les exemples de données sous **data/product_info/** pour vous faire une idée du schéma par défaut.
1. Explorez le notebook **create-azure-search.ipynb** pour une approche orientée code des mises à jour d’index.
1. **Modifiez** les exemples de données et **exécutez** le notebook pour modifier les index de recherche Azure AI par défaut.
1. **Redéployez** l’application. Saisissez une question de test pour vérifier que les nouvelles données de produit sont bien renvoyées.

### Personnaliser le modèle d’invite

1. Étudiez le fichier **contoso_chat/chat.prompty** pour vous faire une idée du modèle d’invite par défaut.
1. Étudiez **contoso_chat/chat.json** pour comprendre l’exemple de schéma de données à tester.
1. **Modifiez** le modèle (message système, sécurité, documentation ou instructions).
1. **Modifiez** les exemples de données si nécessaire.
1. **Utilisez** l’interface CLI Promptflow pour tester le flux localement avec le nouveau modèle d’invite.
1. **Installez et utilisez** l’extension Prompty pour créer un modèle d’invite à partir de zéro.

### Explorer l’automatisation de l’évaluation et du pipeline

Remplacez le jeu de données de test utilisé pour exécuter le pipeline d’évaluation d’application via GitHub Actions par vos propres données. Le jeu de données de test se trouve dans le dossier **data** du projet, et il est au format **.jsonl**.

1. Remplacez le fichier de jeu de données de test par vos propres données.
1. Ensuite, exécutez le pipeline d’évaluation en transmettant les modifications à la branche primaire de votre référentiel dupliqué.

    Le pipeline d’évaluation s’exécute automatiquement et vous pouvez vérifier les résultats sous l’onglet GitHub Actions de votre référentiel.

1. Vous pouvez personnaliser le pipeline d’évaluation en modifiant le fichier **evaluate.yaml** dans le dossier **.github/workflows** du projet et le script **evaluations_chat.py** dans le dossier **evaluations**.

## Nettoyer et supprimer les ressources Azure

Ce projet utilise des modèles et des services (par exemple, Recherche Azure AI) qui peuvent entraîner des coûts non négligeables à long terme. Une fois l’exploration de ce modèle Azure AI AZD terminée, supprimez les ressources créées afin d’éviter des coûts Azure superflus. Pour ce faire, exécutez la commande suivante dans le terminal VS Code :

```bash
azd down
```

Non seulement cela annule les étapes ayant servi à approvisionner et à déployer l’application, mais cela permet également de *supprimer définitivement* les ressources qui auraient pu être conservées dans un état de « suppression réversible », impactant ainsi votre capacité à réutiliser les noms de ressources ou à récupérer le quota de modèles. **Cette commande vous demandera de confirmer ces actions pendant l’arrêt. Veillez donc à répondre correctement**.
