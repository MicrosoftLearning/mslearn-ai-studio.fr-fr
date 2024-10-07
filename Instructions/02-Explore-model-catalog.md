---
lab:
  title: "Explorer, déployer et converser avec des modèles de langage dans Azure\_AI\_Studio"
---

# Explorer, déployer et converser avec des modèles de langage dans Azure AI Studio

Le catalogue de modèles d’Azure AI Studio sert de référentiel central où vous pouvez explorer et utiliser une variété de modèles, facilitant ainsi la création de votre scénario d'IA générative.

Dans cet exercice, vous explorerez le catalogue de modèles dans Azure AI Studio.

Cet exercice prend environ **25** minutes.

## Créer un hub Azure AI

Vous avez besoin d’un hub Azure AI dans votre abonnement Azure pour héberger des projets. Vous pouvez créer cette ressource lors de la création d’un projet ou bien l’approvisionner à l’avance (c’est ce que nous allons faire dans cet exercice).

1. Dans la section **Gestion**, sélectionnez **Toutes les ressources**, puis **+ Nouveau hub**. Créez un hub avec les paramètres suivants :
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

## Choisir un modèle à l’aide de benchmarks de modèle

Avant de déployer un modèle, vous pouvez explorer les benchmarks de modèle pour déterminer le modèle le mieux adapté à vos besoins.

Imaginez que vous souhaitez créer un copilote personnalisé pour servir d’assistant de voyage. Plus précisément, vous souhaitez que votre copilote facilite les démarches liées aux voyages, telles que l’obtention d’un visa, les prévisions météorologiques, l’apprentissage des attractions locales et des normes culturelles.

Votre copilote devra pour cela fournir des informations justes, et par conséquent fondées. Facteur secondaire mais important également, les réponses du copilote devront être faciles à lire et à comprendre. Par conséquent, vous avez besoin d’un modèle cohérent et doté d’une bonne fluidité.

1. Dans Azure AI Studio, accédez aux **benchmarks de modèle** dans la section **Prise en main**, à l’aide du menu de gauche.
    Sous l’onglet **Benchmarks de qualité**, vous trouverez certains graphiques déjà affichés, comparant différents modèles.
1. Filtrez les modèles affichés :
    - **Tâches** : réponses aux questions
    - **Collections** : Azure OpenAI
    - **Métriques** : cohérence, fluidité, fondement
1. Explorez les graphiques résultants et la table de comparaison. Durant votre exploration, vous pouvez essayer de répondre aux questions suivantes :
    - Remarquez-vous une différence de performances entre les modèles GPT-3.5 et GPT-4 ?
    - Existe-t-il une différence entre différentes versions du même modèle ?
    - Comment les variantes 32k diffèrent-elles des modèles de base ?

Dans la collection Azure OpenAI, vous pouvez choisir entre les modèles GPT-3.5 et GPT-4. Déployons ces deux modèles et comparons leur efficacité pour votre cas d’usage.

## Déployer des modèles Azure OpenAI

Maintenant que vous avez exploré vos options via des benchmarks de modèle, vous êtes prêt à déployer des modèles de langage. Vous pouvez parcourir le catalogue de modèles et en déployer un à partir de là, ou vous pouvez le faire via la page **Déploiements**. Examinons les deux options.

### Déployer un modèle à partir du catalogue de modèles

Commençons par déployer un modèle à partir du catalogue de modèles. Cette option est préférable lorsque vous souhaitez filtrer tous les modèles disponibles.

1. Accédez à la page **Catalogue de modèles** dans la section **Prise en main**, à l’aide du menu de gauche.
1. Recherchez et déployez le modèle `gpt-35-turbo`, préparé par Azure AI, avec les paramètres suivants :
    - **Nom du déploiement** : *nom unique pour votre modèle de déploiement, indiquant qu’il s’agit d’un modèle GPT-3.5*
    - **Version du modèle** : *Sélectionnez la version par défaut*
    - **Type de déploiement** : Standard
    - **Ressource Azure OpenAI connectée** : *Sélectionnez la connexion par défaut qui a été créée lors de la création de votre hub*
    - **Limite de débit en jetons par minute (en milliers)** : 5 000
    - **Filtre de contenu** : valeur par défaut

### Déployer un modèle via la page Déploiements

Si vous savez déjà quel modèle vous souhaitez déployer, vous pouvez le faire via la page Déploiements.

1. Accédez à la page **Déploiements** dans la section **Composants**, à l’aide du menu de gauche.
1. Dans le panneau **Déploiements de modèles**, créez un déploiement avec les paramètres suivants :
    - **Modèle** : gpt-4
    - **Nom du déploiement** : *nom unique pour votre modèle de déploiement, indiquant qu’il s’agit d’un modèle GPT-4*
    - **Version du modèle** : *Sélectionnez la version par défaut*
    - **Type de déploiement** : Standard
    - **Ressource Azure OpenAI connectée** : *Sélectionnez la connexion par défaut qui a été créée lors de la création de votre hub*
    - **Limite de débit en jetons par minute (en milliers)** : 5 000
    - **Filtre de contenu** : valeur par défaut

    > **Remarque** : vous avez peut-être remarqué que certains modèles comportent les benchmarks de modèle, mais ce n’est pas une option dans votre catalogue de modèles. La disponibilité des modèles dépend de l’emplacement. Votre emplacement est affiché au niveau du hub d’IA, vous pouvez y utiliser l’**assistant Emplacement** pour spécifier le modèle que vous souhaitez déployer afin d’obtenir la liste des emplacements dans lesquels vous pouvez le déployer.

## Tester vos modèles dans le terrain de jeu de conversation

Maintenant que nous avons deux modèles à comparer, examinons leur comportement dans le cadre d’une interaction conversationnelle.

1. Accédez à la page **Conversation** dans la section **Terrain de jeu de projet**, à l’aide du menu de gauche.
1. Dans le** terrain de jeu de conversation**, sélectionnez votre déploiement GPT-3.5.
1. Dans la fenêtre de conversation, entrez la requête `What can you do?` et lisez la réponse.
    Elle sera très générique. Souvenez-vous que nous voulons créer un copilote personnalisé pour servir d’assistant de voyage. Vous pouvez spécifier le type d’aide souhaité tout en posant votre question.
1. Dans la fenêtre de conversation, entrez la requête `Imagine you're a travel assistant, what can you help me with?` La réponse sera déjà plus précise. Vous voudrez peut-être éviter à vos utilisateurs finaux de devoir préciser le contexte chaque fois qu’ils interagiront avec votre copilote. Pour ajouter des instructions globales, vous pouvez modifier le message système.
1. Mettez à jour le message système avec l’invite suivante :

   ```
   You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
   ```

1. Sélectionnez **Appliquer les modifications** et **Effacer la conversation**.
1. Dans la fenêtre de conversation, entrez la requête `What can you do?` et lisez la nouvelle réponse. Elle devrait être très différente de la réponse que vous avez reçue précédemment. La réponse est maintenant adaptée au contexte de voyages.
1. Poursuivez la conversation en demandant : `I'm planning a trip to London, what can I do there?` Le copilote affichera de nombreuses informations liées aux voyages. Vous souhaiterez peut-être améliorer davantage la qualité de cette réponse. Par exemple, afin qu’elle soit plus succincte.
1. Mettez à jour le message système en ajoutant `Answer with a maximum of two sentences.` à la fin du message. Appliquez la modification, effacez la conversation et procédez à un nouveau test en demandant : `I'm planning a trip to London, what can I do there?` Vous voudrez peut-être également que votre copilote continue la conversation au lieu de simplement répondre à la question.
1. Mettez à jour le message système en ajoutant `End your answer with a follow-up question.` à la fin du message. Appliquez la modification, effacez la conversation et testez à nouveau la conversation en demandant : `I'm planning a trip to London, what can I do there?`
1. Remplacez votre **Déploiement** par votre modèle GPT-4 et répétez toutes les étapes de cette section. Vous devriez observer des différences entre les réponses des deux modèles.
1. Enfin, testez les deux modèles avec la requête `Who is the prime minister of the UK?`. Avec cette question, le niveau de performance dépend du fondement (la capacité à fournir des réponses factuellement exactes) des modèles. Les performances correspondent-elles aux conclusions des benchmarks de modèle ?

Maintenant que vous avez exploré les deux modèles, essayez de déterminer celui qui conviendra le mieux à votre cas d’usage. Au début, les sorties des deux modèles peuvent différer, et vous pouvez être tenté de préférer un modèle à l’autre. Toutefois, après avoir mis à jour le message système, les différences vous sembleront sans doute minimes. Dans une optique d’optimisation des coûts, vous pouvez alors opter pour le modèle GPT-3.5 plutôt que le modèle GPT-4, car leurs performances sont très similaires.

## Nettoyage

Si vous avez terminé d’explorer Azure AI Studio, vous devez supprimer les ressources que vous avez créées dans cet exercice pour éviter d’entraîner des coûts Azure inutiles.

1. Revenez à l’onglet du navigateur contenant le portail Azure (ou ouvrez à nouveau le [portail Azure](https://portal.azure.com?azure-portal=true) dans un nouvel onglet de navigateur) et affichez le contenu du groupe de ressources où vous avez déployé les ressources utilisées dans cet exercice.
1. Dans la barre d’outils, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources et confirmez que vous souhaitez le supprimer.
