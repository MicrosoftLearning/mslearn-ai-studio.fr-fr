---
lab:
  title: "Explorer les composants et les outils d’Azure\_AI\_Foundry"
---

# Explorer les composants et les outils d’Azure AI Foundry

Dans cet exercice, vous allez utiliser Azure AI Foundry pour créer un projet et explorer un modèle d’IA générative.

Cet exercice prend environ **30** minutes.

## Ouvrir le portail Azure AI Foundry

Commençons par explorer le portail Azure AI Foundry.

1. Dans un navigateur web, ouvrez [https://ai.azure.com](https://ai.azure.com) et connectez-vous à l’aide de vos informations d’identification Azure. La page d’accueil du portail Azure AI Foundry ressemble à l’image suivante :

    ![Capture d’écran du portail Azure AI Foundry.](./media/azure-ai-studio-home.png)

1. Passez en revue les informations de la page d’accueil et affichez chacun des onglets, en notant les options permettant d’explorer les modèles et les fonctionnalités, de créer des projets et de gérer des ressources.

## Créer un projet et un hub Azure AI

Un hub Azure AI fournit un espace de travail collaboratif dans lequel vous pouvez définir un ou plusieurs *projets*. Nous allons créer un projet et un hub Azure AI.

1. Sur la page d’accueil, sélectionnez **+Créer un projet**. Dans l’assistant **Créer un projet**, vous pouvez voir toutes les ressources Azure qui seront créées automatiquement avec votre projet, ou vous pouvez personnaliser les paramètres suivants en sélectionnant **Personnaliser** avant de sélectionner **Créer** :
   
    - **Hub name** : *Un nom unique*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *Créer un groupe de ressources avec un nom unique ou sélectionner un groupe de ressources existant*
    - **Emplacement** : sélectionnez **Aidez-moi à choisir**, puis sélectionnez **gpt-35-turbo** dans la fenêtre de l’assistant de l’emplacement et utilisez la région recommandée.\*
    - **Connecter Azure AI Services ou Azure OpenAI** : *Sélectionnez ce paramètre pour créer un service IA ou utilisez un service existant*
    - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \* Les ressources Azure OpenAI sont limitées au niveau du locataire par quotas régionaux. Les régions répertoriées incluent le quota par défaut pour les types de modèle utilisés dans cet exercice. Le choix aléatoire d’une région réduit le risque d’atteindre sa limite de quota dans les scénarios où vous partagez un locataire avec d’autres utilisateurs. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région.

1. Si vous avez sélectionné **Personnaliser**, sélectionnez **Suivant** et passez en revue votre configuration.
1. Sélectionnez **Créer** et patientez jusqu’à ce que l’opération se termine.
   
    Une fois le projet et le hub Azure AI créés, ils devraient ressembler à l’image suivante :

    ![Capture d’écran des détails d’un hub Azure AI dans le portail Azure AI Foundry.](./media/azure-ai-resource.png)

1. Ouvrez un nouvel onglet de navigateur (en laissant l’onglet du portail Azure AI Foundry ouvert) et accédez au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com?azure-portal=true), en vous connectant avec vos informations d’identification Azure si vous y êtes invité.
1. Accédez au groupe de ressources où vous avez créé votre hub Azure AI et regardez les ressources Azure qui ont été créées.

    ![Capture d’écran d’un hub Azure AI et des ressources associées sur le Portail Azure.](./media/azure-portal.png)

1. Revenez à l’onglet du navigateur du portail Azure AI Foundry.
1. Visualisez chacune des pages dans le volet de gauche de la page de votre hub Azure AI, et notez les artefacts que vous pouvez créer et gérer. Dans la page **Centre d’administration**, vous pouvez sélectionner **Ressources connectées**, dans votre hub ou votre projet, et observer que les connexions aux services Azure OpenAI et AI ont déjà été créées.
1. Si vous êtes dans la page Centre d’administration, sélectionnez **Accéder au projet**.

## Déployer et tester un modèle

Vous pouvez utiliser un projet pour créer des solutions IA complexes basées sur des modèles IA génératifs. Une exploration complète de toutes les options de développement disponibles dans Azure AI Foundry dépasse l’étendue de cet exercice, mais nous allons explorer des fonctions de base que vous pouvez utiliser pour travailler avec des modèles dans un projet.

1. Dans le volet de gauche de votre projet, dans la section **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
1. Sur la page **Modèles + points de terminaison**, dans l’onglet **Déploiements de modèles**, sélectionnez **+ Déployer un modèle**.
1. Recherchez le modèle **gpt-35-turbo** dans la liste, sélectionnez et confirmez.
1. Déployez le modèle avec les paramètres suivants en sélectionnant **Personnaliser** dans les détails du déploiement :
    - **Nom du déploiement** : *Un nom unique pour votre modèle de déploiement*
    - **Type de déploiement** : Standard
    - **Version du modèle** : *Sélectionnez la version par défaut*
    -  **Ressource IA** : *sélectionnez la ressource que vous avez créée précédemment.*
    - **Limite de débit en jetons par minute (en milliers)** : 5 000
    - **Filtre de contenu** : DefaultV2
    - **Enable dynamic quota** : désactivé
      
    > **Remarque** : La réduction du nombre de jetons par minute permet d’éviter une surutilisation du quota disponible dans l’abonnement que vous utilisez. 5 000 jetons par minute sont suffisants pour les données utilisées dans cet exercice.

1. Une fois le modèle déployé, sur la page de vue d’ensemble du déploiement, sélectionnez **Ouvrir dans le terrain de jeu**.
1. Sur la page **Terrain de jeu de conversation**, vérifiez que votre modèle de déploiement est sélectionné dans la section **Déploiement**.
1. Dans la fenêtre de conversation, entrez une requête telle que *Qu’est-ce que l’IA ?* et affichez la réponse :

    ![Capture d’écran du terrain de jeu dans le portail Azure AI Foundry.](./media/playground.png)

## Nettoyage

Si vous avez terminé d’explorer Azure AI Foundry, vous devez supprimer les ressources que vous avez créées dans cet exercice pour éviter d’entraîner des coûts Azure inutiles.

1. Revenez à l’onglet du navigateur contenant le portail Azure (ou ouvrez à nouveau le [portail Azure](https://portal.azure.com?azure-portal=true) dans un nouvel onglet de navigateur) et affichez le contenu du groupe de ressources où vous avez déployé les ressources utilisées dans cet exercice.
1. Dans la barre d’outils, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources et confirmez que vous souhaitez le supprimer.
