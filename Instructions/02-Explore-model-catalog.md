---
lab:
  title: Choisir et déployer un modèle de langage
  description: Les applications IA génératives sont basées sur un ou plusieurs modèles de langage. Découvrez comment rechercher et sélectionner les modèles appropriés pour votre projet d’IA générative.
---

# Choisir et déployer un modèle de langage

Le catalogue de modèles d’Azure AI Foundry sert de référentiel central où vous pouvez explorer et utiliser une variété de modèles, facilitant ainsi la création de votre scénario d’IA générative.

Dans cet exercice, vous allez explorer le catalogue de modèles dans le portail Azure AI Foundry et comparer les modèles potentiels pour une application d’IA générative qui aide à résoudre les problèmes.

Cet exercice prend environ **25** minutes.

> **Note** : certaines des technologies utilisées dans cet exercice sont en version préliminaire ou en cours de développement. Un comportement inattendu, des avertissements ou des erreurs peuvent se produire.

## Explorer les modèles

Commençons par nous connecter au portail Azure AI Foundry et explorer certains des modèles disponibles.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les conseils ou les volets de démarrage rapide ouverts la première fois que vous vous connectez et, si nécessaire, utilisez le logo **Azure AI Foundry** en haut à gauche pour accéder à la page d’accueil, qui ressemble à l’image suivante (fermez le volet **Aide** s’il est ouvert) :

    ![Capture d’écran du portail Azure AI Foundry.](./media/ai-foundry-home.png)

1. Examinez les informations sur la page d’accueil.
1. Dans la page d’accueil, dans la section **Explorer les modèles et les fonctionnalités**, recherchez le modèle `gpt-4o` ; que nous utiliserons dans notre projet.
1. Dans les résultats de recherche, sélectionnez le modèle **gpt-4o** pour afficher ses détails.
1. Lisez la description et examinez les autres informations disponibles dans l’onglet **Détails**.

    ![Capture d’écran de la page des détails du modèle gpt-4o.](./media/gpt4-details.png)

1. Sur la page **gpt-4o**, ouvrez l’onglet **Références** pour voir comment le modèle se compare, selon des indicateurs de performance standard, à d’autres modèles utilisés dans des scénarios similaires.

    ![Capture d’écran de la page des benchmarks du modèle gpt-4o.](./media/gpt4-benchmarks.png)

1. Utilisez la flèche de retour (**&larr;**) à côté du titre de la page **gpt-4o** pour revenir au catalogue des modèles.
1. Recherchez `Phi-4-mini-instruct`, puis affichez les détails et les performances du modèle **Phi-4-mini-instruct**.

## Comparer des modèles

Vous avez examiné deux modèles différents, qui peuvent être utilisés pour implémenter une application de conversation d’IA générative. Nous allons maintenant comparer visuellement les métriques de ces deux modèles.

1. Utilisez la flèche de retour (**&larr;**) pour revenir au catalogue des modèles.
1. Sélectionnez **Comparer les modèles**. Un graphique visuel pour la comparaison de modèles s’affiche avec une sélection de modèles courants.

    ![Capture d’écran de la page de comparaison des modèles.](./media/compare-models.png)

1. Dans le volet **Modèles à comparer**, notez que vous pouvez sélectionner des tâches courantes, comme *répondre à des questions*, pour sélectionner automatiquement les modèles les plus utilisés pour ces tâches spécifiques.
1. Utilisez l’icône **Effacer tous les modèles** (&#128465;) pour supprimer tous les modèles pré-sélectionnés.
1.  Utilisez le bouton **+ Modèle à comparer** pour ajouter le modèle **gpt-4o** à la liste. Utilisez ensuite le même bouton pour ajouter le modèle **Phi-4-mini-instruct** à la liste.
1. Passez en revue le graphique, qui compare les modèles en fonction de l’**index de qualité** (un score standardisé indiquant la qualité du modèle) et le **coût**. Vous pouvez voir les valeurs spécifiques d’un modèle en maintenant la souris sur le point qui le représente dans le graphique.

    ![Capture d’écran du graphique de comparaison de modèles pour gpt-4o et Phi-4-mini-instruct.](./media/comparison-chart.png)

1. Dans le menu déroulant **Axe X**, sous **Qualité**, sélectionnez les mesures suivantes et observez chaque graphique obtenu avant de passer au suivant :
    - Précision
    - Index de qualité

    D’après les benchmarks, le modèle gpt-4o semble offrir les meilleures performances globales, mais à un coût plus élevé.

1. Dans la liste des modèles à comparer, sélectionnez le modèle **gpt-4o** pour rouvrir sa page de benchmarks.
1. Dans la page du modèle **gpt-4o**, sélectionnez l’onglet **Vue d’ensemble** pour afficher les détails du modèle.

## Créer un projet Azure AI Foundry

Pour utiliser un modèle, vous devez créer un *projet* Azure AI Foundry.

1. En haut de la page de vue d’ensemble du modèle **gpt-4o**, sélectionnez **Utiliser ce modèle**.
1. Lorsque vous êtes invité à créer un projet, entrez un nom valide pour votre projet et développez **les options avancées**.
1. Dans la section **Options avancées**, indiquez les paramètres suivants pour votre projet :
    - **Ressource Azure AI Foundry** : *un nom valide pour votre ressource Azure AI Foundry.*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *créez ou sélectionnez un groupe de ressources*
    - **Région** : *sélectionnez n’importe quel emplacement pris en charge par les services d’IA***\*

    > \* Certaines ressources Azure AI sont limitées par des quotas de modèles régionaux. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région.

1. Sélectionnez **Créer** et attendez que votre projet, y compris le déploiement du modèle gpt-4 que vous avez sélectionné, soit créé.
1. Une fois le projet créé, le terrain de jeu de conversation instantanée s’ouvrira automatiquement pour vous permettre de tester votre modèle :

    ![Capture d’écran du terrain de jeu de conversation instantanée d’un projet Azure AI Foundry.](./media/ai-foundry-chat-playground.png)

## Discuter avec le modèle *gpt-4o*

Maintenant que vous avez déployé un modèle, vous pouvez utiliser le terrain de jeu pour le tester.

1. Dans le terrain de jeu de conversation instantanée, dans le volet **Configuration**, vérifiez que votre modèle **gpt-4o** est sélectionné, puis définissez l’invite du système dans le champ **Fournir des instructions et un contexte au modèle** à `You are an AI assistant that helps solve problems.`.
1. Sélectionnez **Appliquer les modifications** pour mettre à jour l’invite système.

1. Dans la fenêtre de conversation, entrez la requête suivante :

    ```
   I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Affichez la réponse. Entrez ensuite la requête de suivi suivante :

    ```
   Explain your reasoning.
    ```

## Déployer un autre modèle

Lorsque vous avez créé votre projet, le modèle **gpt-4o** que vous aviez sélectionné a été automatiquement déployé. Déployons le modèle * **Phi-4-mini-instruct** que vous avez également considéré.

1. Dans la barre de navigation de gauche, dans la section **Mes ressources**, sélectionnez **Modèles + points de terminaison**.
1. Sous l’onglet **Déploiements de modèles**, dans la liste déroulante **+ Déployer un modèle**, sélectionnez **Déployer le modèle de base**. Recherchez ensuite `Phi-4-mini-instruct`, puis confirmez votre sélection.
1. Acceptez la licence du modèle.
1. Déployez un modèle **Phi-4-mini-instruct** avec les paramètres suivants :
    - **Nom du déploiement** : *nom valide pour votre modèle de déploiement*
    - **Type de déploiement** : standard global
    - **Détails du déploiement** : *utilisez les paramètres par défaut*

1. Attendez la fin du déploiement.

## Converser avec le modèle *Phi-4*

Discutons maintenant avec le nouveau modèle dans le terrain de jeu.

1. Dans la barre de navigation, sélectionnez **Terrains de jeu**. Sélectionnez ensuite le **terrain de jeu de conversation**.
1. Dans le terrain de jeu de conversation, dans le volet **Configuration**, assurez-vous que votre modèle **Phi-4-mini-instruct** est sélectionné et dans la zone de conversation, fournissez la première ligne comme `System message: You are an AI assistant that helps solve problems.` (la même invite système que vous avez utilisée pour tester le modèle gpt-4o, mais étant donné qu’il n’existe aucune configuration de message système, nous le fournissons dans la première conversation pour le contexte.)
1. Sur une nouvelle ligne dans la fenêtre de conversation (sous votre message système), entrez la requête suivante.

    ```
   I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Affichez la réponse. Entrez ensuite la requête de suivi suivante :

    ```
   Explain your reasoning.
    ```

## Effectuer une comparaison supplémentaire

1. Utilisez la liste déroulante du volet **Configuration** pour basculer entre vos modèles, et testez-les tous les deux avec l’énigme suivante (la réponse correcte est 40 !) :

    ```
   I have 53 socks in my drawer: 21 identical blue, 15 identical black and 17 identical red. The lights are out, and it is completely dark. How many socks must I take out to make 100 percent certain I have at least one pair of black socks?
    ```

## Réfléchir sur les modèles

Vous avez comparé deux modèles, qui peuvent varier en termes de capacité à générer des réponses appropriées et dans leur coût. Dans n’importe quel scénario de génération, vous devez trouver un modèle qui offre le bon rapport entre son adéquation à la tâche à effectuer et son coût d’utilisation pour le nombre de demandes que vous prévoyer de traiter.

Les détails et les benchmarks fournis dans le catalogue de modèles, ainsi que la possibilité de comparer visuellement les modèles offrent un point de départ utile lors de l’identification des modèles potentiels pour une solution d’IA générative. Vous pouvez ensuite tester les modèles potentiels avec une variété d’invites système et utilisateur dans le terrain de jeu de conversation.

## Nettoyage

Si vous avez terminé d’explorer Azure AI Foundry, vous devez supprimer les ressources que vous avez créées dans cet exercice pour éviter d’entraîner des coûts Azure inutiles.

1. Ouvrez le [portail Azure](https://portal.azure.com) et affichez le contenu du groupe de ressources où vous avez déployé les ressources utilisées dans cet exercice.
1. Dans la barre d’outils, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources et confirmez que vous souhaitez le supprimer.
