---
lab:
  title: Préparer un projet de développement IA
  description: Découvrez comment organiser des ressources cloud dans des hubs et des projets afin de préparer les développeurs à créer des solutions IA réussies.
---

# Préparer un projet de développement IA

Dans cet exercice, vous utilisez le portail Azure AI Foundry pour créer un hub et un projet, pour qu’une équipe de développeurs crée une solution IA.

Cet exercice prend environ **30** minutes.

## Ouvrir le portail Azure AI Foundry

Commençons par nous connecter au portail Azure AI Foundry.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les conseils ou les volets de démarrage rapide ouverts la première fois que vous vous connectez et, si nécessaire, utilisez le logo **Azure AI Foundry** en haut à gauche pour accéder à la page d’accueil, qui ressemble à l’image suivante (fermez le volet **Aide** s’il est ouvert) :

    ![Capture d’écran du portail Azure AI Foundry.](./media/ai-foundry-home.png)

1. Examinez les informations sur la page d’accueil.

## Créez un hub et un projet.

Un *hub* Azure AI fournit un espace de travail collaboratif dans lequel vous pouvez définir un ou plusieurs *projets*. Nous allons créer un projet et un hub Azure AI, et passer en revue les ressources Azure créées pour les prendre en charge.

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

1. En bas à gauche du volet de navigation, sélectionnez **Centre de gestion**. Le centre de gestion est l’endroit où vous pouvez configurer les paramètres au niveau du *hub* et du *projet*, qui sont tous deux affichés dans le volet de navigation.

    ![Capture d’écran de la page du centre de gestion dans le portail Azure AI Foundry.](./media/ai-foundry-management.png)

    Notez que dans le volet de navigation, vous pouvez afficher et gérer les ressources au niveau du hub et du projet dans les pages suivantes :

    - Vue d’ensemble
    - Utilisateurs
    - Modèles et points de terminaison
    - Ressources connectées.
    - Calcul (*au niveau du hub uniquement*)

    > **Note** : en fonction des autorisations affectées à votre ID Entra dans votre client Azure, vous ne pourrez peut-être pas gérer les ressources au niveau du hub.

1. Dans le volet de navigation, dans la section de votre hub, sélectionnez la page **Vue d’ensemble** pour afficher les détails de votre hub. 
1. Dans le volet **Propriétés du hub**, sélectionnez le lien vers le groupe de ressources associé au hub pour ouvrir un nouvel onglet de navigateur et accédez au portail Azure. Si vous y êtes invité, connectez-vous avec vos informations d’identification Azure.
1. Affichez le groupe de ressources dans le portail Azure pour voir les ressources Azure qui ont été créées pour prendre en charge votre hub et votre projet.

    ![Capture d’écran d’un hub Azure AI et des ressources associées sur le Portail Azure.](./media/azure-portal-resources.png)

    Notez que les ressources ont été créées dans la région que vous avez sélectionnée lors de la création du hub.

## Ajouter une ressource connectée

Supposons que votre projet a besoin d’accéder à une deuxième ressource **Azure AI Services** dans une autre région.

1. Dans le portail Azure, dans la page de votre groupe de ressources, sélectionnez **+Créer** et recherchez `Azure AI Services`. Dans les résultats, sélectionnez la ressource multiservices **Azure AI Services**, comme illustré dans l’image suivante :

    ![Capture d’écran de la vignette de la ressource Azure AI Services dans le portail Azure.](./media/azure-ai-services.png)

1. Créez ensuite une ressource **Azure AI Services** avec les paramètres suivants :
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *le groupe de ressources contenant votre ressource Azure AI Foundry*
    - **Région** : *sélectionnez une région disponible autre que celle contenant vos ressources existantes*
    - **Nom** : *un nom approprié pour votre deuxième ressource Azure AI Services*
    - **Niveau tarifaire** : Standard S0
1. Attendez que la ressource AI Services soit créée.
1. Revenez à l’onglet navigateur du portail Azure AI Foundry et, dans la vue **Centre de gestion**, dans le volet de navigation, dans la section de votre *<u>projet</u>*, affichez la page **Ressources connectées**. Les ressources connectées existantes dans votre projet sont répertoriées.

    ![Capture d’écran des ressources connectées dans un projet AI Foundry.](./media/ai-foundry-project-resources.png)

1. Sélectionnez **+ Nouvelle connexion**, puis le type de ressource **Azure AI Services**. Parcourez ensuite les ressources disponibles pour rechercher la ressource AI Services que vous avez créée dans le portail Azure et utilisez son bouton **Ajouter une connexion** pour l’ajouter à votre projet.

    ![Capture d’écran de la boîte de dialogue Connecter des ressources Azure AI Services dans un projet AI Foundry.](./media/add-resource.png)

1. Lorsque la nouvelle ressource est connectée, fermez la boîte de dialogue **Connecter des ressources Azure AI Services** et vérifiez que de nouvelles ressources connectées pour Azure AI Services et Azure OpenAI Service sont répertoriées.

## Explorer AI Services

Votre projet Azure AI Foundry a accès à Azure AI Services. Essayons dans le portail.

1. Dans la page Centre de gestion, dans le volet de navigation, dans votre projet, sélectionnez **Accéder au projet**.
1. Dans le volet de navigation de votre projet, sélectionnez **AI Services**, puis sélectionnez la vignette **Language et Traducteur**.

    ![Capture d’écran de la vignette Language et Traducteur dans le portail Azure AI Foundry.](./media/language-and-translator.png)

1. Dans la section **Explorer les fonctionnalités Language**, affichez l’onglet **Traduction** et sélectionnez **Traduction de texte**.

    ![Capture d’écran de la vignette Traduction de texte dans le portail Azure AI Foundry.](./media/text-translation.png)

1. Dans la page **Traduction de texte**, dans la section **Faire un essai**, affichez l’onglet **Essayer avec le vôtre**.
1. Sélectionnez l’une de vos ressources Azure AI Services, puis essayez de traduire du texte (par exemple, `Hello world`) d’une langue à une autre.

    ![Capture d’écran de la vignette Traduction de texte dans le portail Azure AI Foundry.](./media/try-translation.png)

## Déployer un modèle d’IA générative

Votre projet contient également des ressources connectées pour Azure OpenAI, ce qui vous permet d’utiliser des modèles de langage Azure OpenAI pour implémenter des solutions d’IA génératives. Vous pouvez également trouver et utiliser des modèles d’IA générative provenant d’autres fournisseurs dans le catalogue de modèles.

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

1. Une fois le modèle déployé, sur la page de vue d’ensemble du déploiement, sélectionnez **Ouvrir dans le terrain de jeu**.
1. Sur la page **Terrain de jeu de conversation**, vérifiez que votre modèle de déploiement est sélectionné dans la section **Déploiement**.
1. Dans le volet **Configuration**, dans la zone **Fournir des instructions et un contexte au modèle**, saisissez les instructions suivantes :

    ```
    You are a history teacher who can answer questions about past events all around the world.
    ```

1. Appliquez les modifications pour mettre à jour le message système.
1. Dans la fenêtre de conversation, entrez une requête telle que `What are the key events in the history of Scotland?` et affichez la réponse :

    ![Capture d’écran du terrain de jeu dans le portail Azure AI Foundry.](./media/ai-foundry-playground.png)

## Résumé

Dans cet exercice, vous avez exploré Azure AI Foundry et découvert comment créer et gérer des hubs et des projets, ajouter des ressources connectées et explorer les modèles Azure AI Services et Azure OpenAI dans le portail Azure AI Foundry.

## Nettoyage

Si vous avez terminé d’explorer Azure AI Foundry, vous devez supprimer les ressources que vous avez créées dans cet exercice pour éviter d’entraîner des coûts Azure inutiles.

1. Revenez à l’onglet du navigateur contenant le portail Azure (ou ouvrez à nouveau le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` dans un nouvel onglet de navigateur) et affichez le contenu du groupe de ressources dans lequel vous avez déployé les ressources utilisées dans cet exercice.
1. Dans la barre d’outils, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources et confirmez que vous souhaitez le supprimer.
