---
lab:
  title: Choisir et déployer un modèle de langage
  description: Les applications IA génératives sont basées sur un ou plusieurs modèles de langage. Découvrez comment rechercher et sélectionner les modèles appropriés pour votre projet d’IA générative.
---

# Choisir et déployer un modèle de langage

Le catalogue de modèles d’Azure AI Foundry sert de référentiel central où vous pouvez explorer et utiliser une variété de modèles, facilitant ainsi la création de votre scénario d’IA générative.

Dans cet exercice, vous allez explorer le catalogue de modèles dans le portail Azure AI Foundry et comparer les modèles potentiels pour une application d’IA générative qui aide à résoudre les problèmes.

Cet exercice prend environ **25** minutes.

## Créer un projet et un hub Azure AI

Un hub Azure AI fournit un espace de travail collaboratif dans lequel vous pouvez définir un ou plusieurs *projets*. Nous allons créer un projet et un hub Azure AI.

1. Dans un navigateur web, ouvrez le [portail Azure Ai Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.

1. Sur la page d’accueil, sélectionnez **+Créer un projet**.
1. Dans l’assistant **Créer un projet**, entrez un nom de projet approprié pour (par exemple, `my-ai-project`), puis passez en revue les ressources Azure qui seront automatiquement créées pour prendre en charge votre projet.
1. Sélectionnez **Personnaliser** et spécifiez les paramètres suivants pour votre hub :
    - **Nom du hub** : *nom unique, par exemple `my-ai-hub`*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *créez un groupe de ressources avec un nom unique ( par exemple, `my-ai-resources`) ou sélectionnez un groupe de ressources existant.*
    - **Emplacement** : sélectionnez **Aidez-moi à choisir**, puis sélectionnez **gpt-4** dans la fenêtre de l’assistant de l’emplacement et utilisez la région recommandée.\*
    - **Connecter Azure AI Services ou Azure OpenAI** : *créer une ressource AI Services avec un nom approprié (par exemple, `my-ai-services`) ou utiliser une ressource existante*
    - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \* Les quotas de modèles sont limités au niveau du locataire par des quotas régionaux. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région.

1. Sélectionnez **Suivant** et passez en revue votre configuration. Sélectionnez **Créer** et patientez jusqu’à ce que l’opération se termine.
1. Une fois votre projet créé, fermez les conseils affichés et passez en revue la page du projet dans le portail Azure AI Foundry, qui doit ressembler à l’image suivante :

    ![Capture d’écran des détails d’un projet Azure AI dans le portail Azure AI Foundry.](./media/ai-foundry-project.png)

## Configurer le déploiement du service d’inférence Azure AI

Il existe plusieurs options pour déployer des modèles dans le portail Azure AI Foundry. Dans cet exercice, vous allez utiliser l’option de déploiement d’**inférence de modèle Azure AI**, qui prend en charge les modèles *Azure OpenAI* et les *modèles en tant que service* du catalogue de modèles Azure AI Foundry. Étant donné que tous les modèles sont déployés sur un point de terminaison commun hébergé par votre ressource Azure AI Services, il est facile de basculer entre les modèles lors de leur test afin de comparer le comportement et les performances.

1. Dans la barre d’outils située en haut à droite de votre projet Azure AI Foundry, utilisez l’icône **Préversions de fonctionnalités** pour afficher ces dernières.
1. Activez la fonctionnalité **Déployer des modèles vers le service d’inférence de modèle Azure AI**. Fermez ensuite le volet **Fonctionnalités en version préliminaire**.

## Vérifier les détails et les benchmarks des modèles

Pour vous aider à choisir un modèle, vous pouvez explorer les descriptions et les benchmarks de modèles pour déterminer le modèle le mieux adapté à vos besoins.

1. Dans le volet de navigation de gauche du portail de projet Azure AI Foundry, sélectionnez **Catalogue de modèles**.
1. Dans la page d’accueil du catalogue de modèles, recherchez `gpt-4` pour trouver le modèle de saisie semi-automatique conversationnel **gpt-4**.

    ![Capture d’écran d’une recherche de modèle « gpt-4 » dans le catalogue de modèles.](./media/model-catalog-search-gpt4.png)

1. Sélectionnez le modèle **gpt-4** et affichez ses détails. Lisez la description et passez en revue les autres informations disponibles sur la page.

    ![Capture d’écran de la page des détails du modèle gpt-4.](./media/gpt4-details.png)

1. Dans la page **gpt-4**, affichez l’onglet **Benchmarks** pour voir la comparaison du modèle à d’autres modèles utilisés dans des scénarios similaires en fonction de références de performance standard.

    ![Capture d’écran de la page des benchmarks du modèle gpt-4.](./media/gpt4-benchmarks.png)

1. Utilisez la flèche précédent (**&larr;**) en regard du titre de la page **gpt-4** pour revenir à la page d’accueil du catalogue de modèles.
1. Dans le catalogue de modèles, recherchez `Phi-3.5-mini-instruct` et affichez les détails et les benchmarks du modèle **Phi-3.5-mini-instruct**.

## Comparer des modèles

Vous avez examiné deux modèles différents, qui peuvent être utilisés pour implémenter une application de conversation d’IA générative. Nous allons maintenant comparer visuellement les métriques de ces deux modèles.

1. Revenez à la page d’accueil du **catalogue de modèles**.
1. Sélectionnez **Comparer les modèles**. Un graphique visuel pour la comparaison de modèles s’affiche avec une sélection de modèles courants.

    ![Capture d’écran de la page de comparaison des modèles.](./media/compare-models.png)

1. Dans le volet **Modèles à comparer** à gauche, notez que vous pouvez sélectionner des tâches populaires, telles que les *réponses aux questions* pour sélectionner automatiquement les modèles couramment utilisés pour des tâches spécifiques.
1. Utilisez l’icône **Effacer tous les modèles** (&#128465;) pour supprimer tous les modèles pré-sélectionnés.
1. Utilisez le bouton **+ Modèle à comparer** pour ajouter le modèle **gpt-4** à la liste. Utilisez ensuite le même bouton pour ajouter le modèle **Phi-3.5-mini-instruct** à la liste.
1. Passez en revue le graphique, qui compare les modèles en fonction de l’**index de qualité** (un score standardisé indiquant la qualité du modèle) et le **coût**. Vous pouvez voir les valeurs spécifiques d’un modèle en maintenant la souris sur le point qui le représente dans le graphique.

    ![Capture d’écran du graphique de comparaison des modèles pour gpt-4 et Phi-3.5-mini-instruct.](./media/comparison-chart.png)

1. Dans le menu déroulant **Axe X**, sous **Qualité**, sélectionnez les mesures suivantes et observez chaque graphique obtenu avant de passer au suivant :
    - Précision
    - Cohérence
    - Fluidité
    - Pertinence

## Déployer des modèles

Maintenant que vous avez exploré vos options via des benchmarks de modèle, vous êtes prêt à déployer des modèles de langage. Vous pouvez parcourir le catalogue de modèles et en déployer un à partir de là, ou vous pouvez le faire via la page **Déploiements**. Examinons les deux options.

### Déployer un modèle à partir du *catalogue de modèles*

Commençons par déployer un modèle à partir du catalogue de modèles. Cette option est préférable lorsque vous souhaitez examiner plusieurs modèles disponibles.

1. Revenez à la page d’accueil du **catalogue de modèles**.
1. Recherchez et sélectionnez le modèle `gpt-4`, comme vous l’avez fait précédemment.
1. Sur la page **gpt-4**, sélectionnez **Déployer** et déployez le modèle avec les paramètres suivants en sélectionnant **Personnaliser** dans les détails du déploiement :
    - **Nom du déploiement** : *nom unique pour votre modèle de déploiement, par exemple `gpt-4-model`*
    - **Type de déploiement** : standard global
    - **Version du modèle** : *Sélectionnez la version par défaut*
    - **Ressource IA connectée** : *votre connexion de ressources Azure OpenAI*
    - **Limite de débit en jetons par minute (en milliers)** : 5 000
    - **Filtre de contenu** : DefaultV2
    - **Enable dynamic quota** : désactivé
      
    > **Remarque** : La réduction du nombre de jetons par minute permet d’éviter une surutilisation du quota disponible dans l’abonnement que vous utilisez. 5 000 jetons par minute sont suffisants pour les données utilisées dans cet exercice.

1. Attendez que l’**état d’approvisionnement** du déploiement soit **Réussi**.

### Déployer un modèle par le biais de *Modèles + points de terminaison*

Si vous savez déjà quel modèle vous souhaitez déployer, vous pouvez le faire par le biais de la page **Modèles + points de terminaison**.

1. Dans la barre de navigation de gauche, dans la section **Mes ressources**, sélectionnez **Modèles + points de terminaison**.
1. Sous l’onglet **Déploiements de modèles**, dans la liste déroulante **+ Déployer un modèle**, sélectionnez **Déployer le modèle de base**. Recherchez ensuite `Phi-3.5-mini-instruct`, puis confirmez votre sélection.
1. Acceptez la licence du modèle.
1. Déployez un modèle **Phi-3.5-mini-instruct** avec les paramètres suivants :
    - **Nom du déploiement** : *nom unique pour votre modèle de déploiement, par exemple `phi-35-model`*
    - **Type de déploiement** : standard global
    - **Détails du déploiement** : *utilisez les paramètres par défaut*

1. Attendez que l’**état d’approvisionnement** du déploiement soit **Réussi**.

## Tester vos modèles dans le terrain de jeu de conversation

Maintenant que vous avez deux modèles à comparer, examinons leur comportement dans le cadre d’une interaction conversationnelle.

### Préparer la conversation

1. Dans la barre de navigation, sélectionnez **Terrains de jeu**. Sélectionnez ensuite le **terrain de jeu de conversation**.
1. Dans le volet **Configuration**, dans le champ **Donner des instructions et du contexte au modèle**, définissez l’invite du système sur `You are an AI assistant that helps solve problems.`.
1. Sélectionnez **Appliquer les modifications**.

### Discuter avec le modèle *gpt-4*

Dans le volet **Configuration**, sélectionnez votre modèle *gpt-4*.
1. Dans la fenêtre de conversation, entrez la requête suivante :

    ```
    I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Affichez la réponse. Entrez ensuite la requête de suivi suivante :

    ```
    Explain your reasoning.
    ```

### Discuter avec le modèle *Phi-3.5*

1. Dans le volet **Configuration**, sélectionnez votre modèle *Phi-3.5*.
1. Vérifiez qu’une nouvelle session de conversation est démarrée avant de répéter les mêmes invites que celles que vous avez précédemment utilisées pour tester le modèle gpt-4.
1. Dans la fenêtre de conversation, entrez la requête suivante :

    ```
    I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Affichez la réponse. Entrez ensuite la requête de suivi suivante :

    ```
    Explain your reasoning.
    ```

### Effectuer une comparaison supplémentaire

1. Essayez le puzzle suivant avec les deux modèles, en demandant aux modèles d’expliquer leur raisonnement (la bonne réponse est 40) :

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
