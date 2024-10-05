---
lab:
  title: "Explorer les composants et les outils d’Azure\_AI\_Studio"
---

# Explorer les composants et les outils d’Azure AI Studio

Dans cet exercice, vous utilisez Azure AI Studio pour créer un projet et explorer un modèle d’IA générative.

Cet exercice prend environ **30** minutes.

## Ouvrir Azure AI Studio

Commençons par explorer Azure AI Studio.

1. Dans un navigateur web, ouvrez [https://ai.azure.com](https://ai.azure.com) et connectez-vous à l’aide de vos informations d’identification Azure. La page d’accueil d’Azure AI Studio ressemble à l’image suivante :

    ![Capture d’écran d’Azure AI Studio.](./media/azure-ai-studio-home.png)

1. Passez en revue les informations de la page d’accueil et affichez chacun des onglets, en notant les options permettant d’explorer les modèles et les fonctionnalités, de créer des projets et de gérer des ressources.

## Créer un hub Azure AI

Vous avez besoin d’un hub Azure AI dans votre abonnement Azure pour héberger des projets. Vous pouvez créer cette ressource lors de la création d’un projet ou bien l’approvisionner à l’avance (c’est ce que nous allons faire dans cet exercice).

1. Dans la section **Gestion**, sélectionnez **Tous les hubs**, puis **+ Nouveau hub**. Créez un hub avec les paramètres suivants :
    - **Hub name** : *Un nom unique*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *Créer un groupe de ressources avec un nom unique ou sélectionner un groupe de ressources existant*
    - **Emplacement** : *Choisir de manière **aléatoire** une région parmi les suivantes*\*
        - Australie Est
        - Est du Canada
        - USA Est
        - USA Est 2
        - France Centre
        - Japon Est
        - Centre-Nord des États-Unis
        - Suède Centre
        - Suisse Nord
        - Sud du Royaume-Uni
    - **Connecter Azure AI Services ou Azure OpenAI** : *Sélectionnez ce paramètre pour créer un service IA ou utilisez un service existant*
    - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \* Les ressources Azure OpenAI sont limitées au niveau du locataire par quotas régionaux. Les régions répertoriées incluent le quota par défaut pour les types de modèle utilisés dans cet exercice. Le choix aléatoire d’une région réduit le risque d’atteindre sa limite de quota dans les scénarios où vous partagez un locataire avec d’autres utilisateurs. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région.

    Une fois le hub Azure AI créé, il doit être similaire à l’image suivante :

    ![Capture d’écran des détails d’un hub Azure AI dans Azure AI Studio.](./media/azure-ai-resource.png)

1. Ouvrez un nouvel onglet de navigateur (en laissant l’onglet Azure AI Studio ouvert) et accédez au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com?azure-portal=true), en vous connectant avec vos informations d’identification Azure si vous y êtes invité.
1. Accédez au groupe de ressources où vous avez créé votre hub Azure AI et regardez les ressources Azure qui ont été créées.

    ![Capture d’écran d’un hub Azure AI et des ressources associées sur le Portail Azure.](./media/azure-portal.png)

1. Revenez à l’onglet Azure AI Studio dans le navigateur.
1. Visualisez chacune des pages dans le volet de gauche de la page de votre hub Azure AI, et notez les artefacts que vous pouvez créer et gérer. Sur la page **Connexions**, observez que les connexions à Azure OpenAI et aux services IA ont déjà été créées.

## Créer un projet

Un hub Azure AI fournit un espace de travail collaboratif dans lequel vous pouvez définir un ou plusieurs *projets*. Nous allons créer un projet dans votre hub Azure AI.

1. Dans Azure AI Studio, vérifiez que vous êtes dans le hub que vous venez de créer (vous pouvez vérifier votre emplacement en vérifiant le chemin en haut de l’écran).
1. Accédez à **Tous les projets** à l’aide du menu de gauche.
1. Sélectionnez **+ Nouveau projet**.
1. Dans l’Assistant **Créer un projet**, créez ensuite un projet avec les paramètres suivants :
    - **Hub actuel** : *Votre hub AI*
    - **Nom du projet** : *Un nom unique pour votre projet*
1. Attendez que votre projet soit créé. Quand il est prêt, il doit ressembler à l’image suivante :

    ![Capture d’écran de la page Détails du projet dans Azure AI Studio.](./media/azure-ai-project.png)

1. Affichez les pages du volet de gauche, en développant chaque section, et notez les tâches que vous pouvez effectuer et les ressources que vous pouvez gérer dans un projet.

## Déployer et tester un modèle

Vous pouvez utiliser un projet pour créer des solutions IA complexes basées sur des modèles IA génératifs. Une exploration complète de toutes les options de développement disponibles dans Azure AI Studio dépasse l’étendue de cet exercice, mais nous allons explorer des fonctions de base que vous pouvez utiliser pour travailler avec des modèles dans un projet.

1. Dans le volet de gauche de votre projet, dans la section **Composants**, sélectionnez la page **Déploiements**.
1. Sur la page **Déploiements**, sous l’onglet **Déploiements de modèles**, sélectionnez **+ Créer un déploiement**.
1. Recherchez le modèle **gpt-35-turbo** dans la liste, sélectionnez et confirmez.
1. Déployez le modèle avec les paramètres suivants :
    - **Nom du déploiement** : *Un nom unique pour votre modèle de déploiement*
    - **Version du modèle** : *Sélectionnez la version par défaut*
    - **Type de déploiement** : Standard
    - **Ressource Azure OpenAI connectée** : *Sélectionnez la connexion par défaut qui a été créée lors de la création de votre hub*
    - **Limite de débit en jetons par minute (en milliers)** : 5 000
    - **Filtre de contenu** : valeur par défaut

    > **Remarque** : La réduction du nombre de jetons par minute permet d’éviter une surutilisation du quota disponible dans l’abonnement que vous utilisez. 5 000 jetons par minute sont suffisants pour les données utilisées dans cet exercice.

1. Une fois le modèle déployé, sur la page de vue d’ensemble du déploiement, sélectionnez **Ouvrir dans le terrain de jeu**.
1. Sur la page **Terrain de jeu de conversation**, vérifiez que votre modèle de déploiement est sélectionné dans la section **Déploiement**.
1. Dans la fenêtre de conversation, entrez une requête telle que *Qu’est-ce que l’IA ?* et affichez la réponse :

    ![Capture d’écran du playground dans Azure AI Studio.](./media/playground.png)

## Nettoyage

Si vous avez terminé d’explorer Azure AI Studio, vous devez supprimer les ressources que vous avez créées dans cet exercice pour éviter d’entraîner des coûts Azure inutiles.

1. Revenez à l’onglet du navigateur contenant le portail Azure (ou ouvrez à nouveau le [portail Azure](https://portal.azure.com?azure-portal=true) dans un nouvel onglet de navigateur) et affichez le contenu du groupe de ressources où vous avez déployé les ressources utilisées dans cet exercice.
1. Dans la barre d’outils, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources et confirmez que vous souhaitez le supprimer.
