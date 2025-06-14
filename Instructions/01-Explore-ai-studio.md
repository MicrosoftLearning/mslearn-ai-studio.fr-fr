---
lab:
  title: Préparer un projet de développement IA
  description: Découvrez comment organiser des ressources cloud dans des hubs et des projets afin de préparer les développeurs à créer des solutions IA réussies.
---

# Préparer un projet de développement IA

Dans cet exercice, vous utilisez le portail Azure AI Foundry pour créer un projet prêt à servir de base à une solution d’IA.

Cet exercice prend environ **30** minutes.

> **Note** : certaines des technologies utilisées dans cet exercice sont en version préliminaire ou en cours de développement. Un comportement inattendu, des avertissements ou des erreurs peuvent se produire.

## Ouvrir le portail Azure AI Foundry

Commençons par nous connecter au portail Azure AI Foundry.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les conseils ou les volets de démarrage rapide ouverts la première fois que vous vous connectez et, si nécessaire, utilisez le logo **Azure AI Foundry** en haut à gauche pour accéder à la page d’accueil, qui ressemble à l’image suivante (fermez le volet **Aide** s’il est ouvert) :

    ![Capture d’écran du portail Azure AI Foundry.](./media/ai-foundry-home.png)

1. Examinez les informations sur la page d’accueil.

## Créer un projet

Un *projet* Azure AI fournit un espace de travail collaboratif pour le développement de solutions d’IA. Commençons par choisir un modèle avec lequel nous voulons travailler, puis créons un projet pour l’utiliser.

> **Remarque** : les projets AI Foundry peuvent reposer sur une ressource *Azure AI Foundry*, qui donne accès à des modèles d’IA (y compris Azure OpenAI), aux services Azure AI et à d’autres ressources pour le développement d’assistants d’IA et de solutions de discussion. Les projets peuvent aussi être basés sur des ressources *Hub IA*, qui incluent des connexions aux ressources Azure pour le stockage sécurisé, la capacité de calcul et des outils spécialisés. Les projets fondés sur Azure AI Foundry conviennent parfaitement aux développeurs souhaitant gérer eux-mêmes les ressources pour le développement d’assistants d’IA ou d’applications de discussion. Les projets basés sur Hub IA sont plus adaptés aux équipes de développement en entreprise qui travaillent sur des solutions d’IA complexes.

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
1. Une fois le projet créé, le terrain de jeu de conversation instantanée s’ouvrira automatiquement pour vous permettre de tester votre modèle :

    ![Capture d’écran du terrain de jeu de conversation instantanée d’un projet Azure AI Foundry.](./media/ai-foundry-chat-playground.png)

1. Dans le volet de navigation à gauche, sélectionnez **Vue d’ensemble** pour accéder à la page principale de votre projet ; elle se présente comme suit :

    > **Remarque** : si une erreur *Autorisations insuffisantes* s’affiche, utilisez le bouton **Corriger** pour la résoudre.

    ![Capture d’écran d’une page de présentation d’un projet Azure AI Foundry.](./media/ai-foundry-project.png)

1. En bas à gauche du volet de navigation, sélectionnez **Centre de gestion**. Le centre de gestion permet de configurer les paramètres aux niveaux *ressource* et *projet*, tous deux visibles dans le volet de navigation.

    ![Capture d’écran de la page du centre de gestion dans le portail Azure AI Foundry.](./media/ai-foundry-management.png)

    Le niveau *ressource* correspond à la ressource **Azure AI Foundry** qui a été créée pour prendre en charge votre projet. Cette ressource comprend des connexions aux services Azure AI et aux modèles Azure AI Foundry, et constitue un point central pour gérer l’accès des utilisateurs aux projets de développement d’IA.

    Le niveau *projet* correspond à votre projet individuel, dans lequel vous pouvez ajouter et gérer des ressources spécifiques.

1. Dans le volet de navigation, dans la section relative à votre ressource Azure AI Foundry, sélectionnez la page **Vue d’ensemble** pour en afficher les détails.
1. Sélectionnez le lien vers le **groupe de ressources** associé à la ressource pour ouvrir un nouvel onglet de navigateur et accéder au portail Azure. Si vous y êtes invité, connectez-vous avec vos informations d’identification Azure.
1. Affichez le groupe de ressources dans le portail Azure pour voir les ressources Azure créées pour prendre en charge votre ressource Azure AI Foundry et votre projet.

    ![Capture d’écran d’une ressource Azure AI Foundry et des ressources de projet dans le portail Azure.](./media/azure-portal-resources.png)

    Notez que les ressources ont été créées dans la région que vous avez sélectionnée lors de la création du projet.

1. Fermez l’onglet du portail Azure et revenez au portail Azure AI Foundry.

## Examiner les connexions de projet

Votre projet Azure AI Foundry et la ressource Azure AI Foundry à laquelle il appartient incluent des connexions à des ressources que vous pouvez utiliser dans des applications d’IA.

1. Dans la page Centre de gestion, dans le volet de navigation, dans votre projet, sélectionnez **Accéder à la ressource**.
1. Dans la page **Vue d’ensemble** du projet, consultez la section **Points de terminaison et clés**, qui contient des points de terminaison et des clés d’autorisation à utiliser dans le code de votre application pour accéder :
    - au projet Azure AI Foundry et aux modèles qui y sont déployés.
    - à Azure OpenAI dans les modèles Azure AI Foundry.
    - Azure AI services

## Tester un modèle d’IA générative

Maintenant que vous connaissez la configuration de votre projet Azure AI Foundry, vous pouvez retourner dans le terrain de jeu de conversation instantanée pour explorer le modèle que vous avez déployé.

1. Dans le volet de navigation à gauche de votre projet, sélectionnez **Terrains de jeu**. 
1. Ouvrez le **terrain de jeu de conversation instantanée** et vérifiez que votre modèle de déploiement **gpt-4o** est sélectionné dans la section **Déploiement**.
1. Dans le volet **Configuration**, dans la zone **Fournir des instructions et un contexte au modèle**, saisissez les instructions suivantes :

    ```
   You are a history teacher who can answer questions about past events all around the world.
    ```

1. Appliquez les modifications pour mettre à jour le message système.
1. Dans la fenêtre de conversation, entrez une requête telle que `What are the key events in the history of Scotland?` et affichez la réponse :

    ![Capture d’écran du terrain de jeu dans le portail Azure AI Foundry.](./media/ai-foundry-playground.png)

## Résumé

Dans cet exercice, vous avez exploré Azure AI Foundry et vu comment créer et gérer des projets ainsi que les ressources associées.

## Nettoyage

Si vous avez terminé d’explorer Azure AI Foundry, vous devez supprimer les ressources que vous avez créées dans cet exercice pour éviter d’entraîner des coûts Azure inutiles.

1. Dans le [portail Azure](https://portal.azure.com) à l’emplacement `https://portal.azure.com`, affichez le contenu du groupe de ressources dans lequel vous avez déployé les ressources utilisées dans cet exercice.
1. Dans la barre d’outils, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources et confirmez que vous souhaitez le supprimer.
