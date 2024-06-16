---
lab:
  title: Découverte du catalogue de modèles dans Azure AI Studio
---

# Découverte du catalogue de modèles dans Azure AI Studio

Le catalogue de modèles d’Azure AI Studio sert de référentiel central où vous pouvez explorer et utiliser une variété de modèles, facilitant ainsi la création de votre scénario d'IA générative.

Dans cet exercice, vous explorerez le catalogue de modèles dans Azure AI Studio.

> **Remarque** : Azure AI Studio est en préversion au moment de la rédaction du présent document et fait l’objet d’un développement actif. Il est possible que certains éléments du service ne soient pas exactement tels qu’ils sont décrits et que certaines fonctionnalités ne fonctionnent pas comme prévu.

> Pour effectuer cet exercice, votre abonnement Azure doit être approuvé pour un accès au service Azure OpenAI.

Cet exercice prend environ **25** minutes.

## Créer un hub Azure AI

Vous avez besoin d’un hub Azure AI dans votre abonnement Azure pour héberger des projets. Vous pouvez créer cette ressource lors de la création d’un projet ou bien l’approvisionner à l’avance (c’est ce que nous allons faire dans cet exercice).

1. Dans un navigateur web, ouvrez [https://ai.azure.com](https://ai.azure.com) et connectez-vous à l’aide de vos informations d’identification Azure.

1. Dans la section Gestion, sélectionnez Tous les hubs, puis **+ Nouveau hub**. Créez un hub avec les paramètres suivants :
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
    - **Connecter Azure AI Services ou Azure OpenAI** : sélectionner ce paramètre pour créer un service IA ou utiliser un service existant
    - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \* Les ressources Azure OpenAI sont limitées au niveau du locataire par quotas régionaux. Les régions répertoriées incluent le quota par défaut pour les types de modèle utilisés dans cet exercice. Le choix aléatoire d’une région réduit le risque d’atteindre sa limite de quota dans les scénarios où vous partagez un locataire avec d’autres utilisateurs. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région.

1. Sélectionnez **Créer**. La création du premier hub peut prendre quelques minutes. Lors de la création du hub, les ressources IA suivantes seront également créées pour vous : 
    - Services IA
    - Compte de stockage
    - Key vault

1. Une fois le hub Azure AI créé, il doit être similaire à l’image suivante :

    ![Capture d’écran des détails d’un hub Azure AI dans Azure AI Studio.](./media/azure-ai-overview.png)

## Créer un projet

Un hub Azure AI fournit un espace de travail collaboratif dans lequel vous pouvez définir un ou plusieurs *projets*. Nous allons créer un projet dans votre hub Azure AI.

1. Dans Azure AI Studio, sur la page **Build**, sélectionnez **+ Nouveau projet**. Ensuite, dans l’Assistant **Création d’un projet**, créez un projet avec les paramètres suivants :

    - **Nom du projet** : *Un nom unique pour votre projet*
    - **Hub** : *Votre hub AI*

1. Attendez que votre projet soit créé. Quand il est prêt, il doit ressembler à l’image suivante :

    ![Capture d’écran de la page Détails du projet dans Azure AI Studio.](./media/azure-ai-project.png)

1. Affichez les pages du volet de gauche, en développant chaque section, et notez les tâches que vous pouvez effectuer et les ressources que vous pouvez gérer dans un projet.

## Déployer un modèle

Vous êtes maintenant prêt à déployer un modèle à utiliser via **Azure AI Studio**. Lorsque le modèle est déployé, vous l’utilisez pour générer du contenu en langage naturel.

1. Dans Azure AI Studio, créez un déploiement avec les paramètres suivants :

    - **Modèle** : gpt-35-turbo
    - **Type de déploiement** : Standard
    - **Ressource Azure OpenAI connectée** : *Votre connexion Azure OpenAI*
    - **Version du modèle** : mise à jour automatique avec la valeur par défaut
    - **Nom du déploiement** : *nom unique de votre choix*
    - **Options avancées**
        - **Filtre de contenu** : valeur par défaut
        - **Limitation du débit en jetons par minute** : 5 000

> **Remarque** : Chaque modèle Azure AI Studio est optimisé pour un équilibre différent entre les fonctionnalités et les performances. Dans cet exercice, nous utiliserons le modèle **GPT 3.5 Turbo**, qui est très performant pour la génération de langage naturel et les scénarios de conversation.

## Tester le modèle dans le terrain de jeu de conversation

Examinons le comportement du modèle dans le cadre d’une interaction conversationnelle.

1. Accédez au **Playground** dans le volet gauche.

1. Dans le mode **Conversation**, entrez l’invite suivante dans la section **Session de conversation**.

    ```
   <Placeholder>
    ```

1. Le modèle répondra probablement par un texte ...

## Modifier le message d'invite du système

Dans l'aire de jeu de conversation instantanée, vous pouvez modifier l'invite du système pour changer le comportement du modèle. L'invite du système est le message initial que le modèle reçoit avant de commencer à générer des réponses.

1. Dans la section **Message système**, remplacez le message système par le texte suivant :

    ```
    <Placeholder>
    ```

1. Appliquez les modifications dans le message système.

1. Dans la section **Session de conversation**, saisissez à nouveau l’invite suivante.

    ```
   <Placeholder>
    ```

8. Observez la sortie, qui devrait indiquer que le ...


## Ajouter les données dans le terrain de jeu de conversation

<Placeholder>

## Nettoyage

Lorsque vous avez terminé avec votre ressource Azure OpenAI, n’oubliez pas de supprimer le déploiement ou l’intégralité de la ressource dans le [portail Azure](https://portal.azure.com/?azure-portal=true).

