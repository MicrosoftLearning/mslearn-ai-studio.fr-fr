---
lab:
  title: Créer une application d’IA générative qui utilise vos propres données
---

# Créer une application d’IA générative qui utilise vos propres données

La génération augmentée e récupération (RAG, Retrieval Augmented Generation) est une technique utilisée pour créer des applications qui intègrent des données provenant de sources de données personnalisées dans une invite pour un modèle d’IA générative. La RAG est un modèle couramment utilisé pour développer des applications d’IA générative, qui sont des applications basées sur la conversation instantanée utilisant un modèle de langage pour interpréter les entrées et générer des réponses appropriées.

Dans cet exercice, vous allez utiliser le portail Azure AI Foundry pour intégrer des données personnalisées dans un flux d’invite d’IA générative.

Cet exercice prend environ **45** minutes.

## Créer une ressource Recherche Azure AI

Votre solution d’application d’IA générative va intégrer des données personnalisées dans un flux d’invite. Pour prendre en charge cette intégration, vous aurez besoin d’une ressource Recherche Azure AI avec laquelle indexer vos données.

1. Dans un navigateur web, ouvrez le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.
1. Dans la page d’accueil, sélectionnez **+ Créer une ressource**, puis recherchez `Azure AI Search`. Créez ensuite une ressource Recherche Azure AI avec les paramètres suivants :

    - **Abonnement** : *Sélectionnez votre abonnement Azure*.
    - **Groupe de ressources** : *sélectionnez ou créez un groupe de ressources*.
    - **Nom du service** : *Entrez un nom de service unique*
    - **Emplacement** : *Choisir de manière **aléatoire** une région parmi les suivantes*\*
        - Australie Est
        - Est du Canada
        - USA Est
        - USA Est 2
        - France Centre
        - Japon Est
        - Centre-Nord des États-Unis
        - Suède Centre
        - Suisse 
    - **Niveau tarifaire** : Standard

    > \* Plus tard, vous allez créer un hub Azure AI (qui inclut un service Azure OpenAI) dans la même région que votre ressource Recherche Azure AI. Les ressources Azure OpenAI sont limitées au niveau du tenant par des quotas régionaux. Les régions répertoriées incluent le quota par défaut pour les types de modèle utilisés dans cet exercice. Le choix aléatoire d’une région réduit le risque d’atteindre sa limite de quota dans les scénarios où vous partagez un tenant avec d’autres utilisateurs. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer un autre hub Azure AI dans une autre région.

1. Attendez que votre déploiement de la ressource Recherche Azure AI soit terminé.

## Créer un projet Azure AI

Vous pouvez désormais créer un projet Azure AI Foundry et les ressources Azure AI pour le prendre en charge.

1. Dans un navigateur web, ouvrez le [portail Azure Ai Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.
1. Sur la page d’accueil, sélectionnez **+Créer un projet**.
1. Dans l’assistant **Créer un projet** , vous pouvez voir toutes les ressources Azure qui seront créées automatiquement avec votre projet. Sélectionnez **Personnaliser** et connectez-vous à votre ressource Recherche Azure AI :

    - **Hub name** : *Un nom unique*
    - **Abonnement Azure** : *Votre abonnement Azure*
    - **Groupe de ressources** : *Sélectionner le groupe de ressources contenant votre ressource Recherche Azure AI*
    - **Emplacement** : *La même localisation que votre ressource Recherche Azure AI*
    - **Connecter Azure AI Services ou Azure OpenAI** : (Nouveauté) *permet de remplir automatiquement le nom de votre hub sélectionné*
    - **Connecter Recherche Azure AI** : *Sélectionner votre ressource Recherche Azure AI*

1. Sélectionnez **Suivant** et passez en revue votre configuration.
1. Sélectionnez **Créer** et patientez jusqu’à ce que l’opération se termine.
   
## Déployer des modèles

Vous avez besoin de deux modèles pour implémenter votre solution :

- Un modèle *d’incorporation* pour vectoriser les données texte pour une indexation et un traitement efficaces.
- Un modèle qui peut générer des réponses en langage naturel aux questions en fonction de vos données.

1. Dans le portail Azure AI Foundry, dans votre projet, dans le volet de navigation de gauche, dans **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
1. Créez un déploiement du modèle **text-embedding-ada-002** avec les paramètres suivants en sélectionnant **Personnaliser** dans l’assistant Déployer le modèle :

    - **Nom du déploiement **: `text-embedding-ada-002`
    - **Type de déploiement** : Standard
    - **Version du modèle** : *Sélectionnez la version par défaut*
    -  **Ressource IA** : *sélectionnez la ressource que vous avez créée précédemment.*
    - **Limite de débit en jetons par minute (en milliers)** : 5 000
    - **Filtre de contenu** : DefaultV2
    - **Enable dynamic quota** : désactivé
      
1. Répétez les étapes précédentes pour déployer un modèle **gpt-35-turbo-16k** avec le nom de déploiement `gpt-35-turbo-16k`.

    > **Remarque** : La réduction du nombre de jetons par minute (TPM) permet d’éviter une surutilisation du quota disponible dans l’abonnement que vous utilisez. 5 000 jetons par minute sont suffisants pour les données utilisées dans cet exercice.

## Ajouter des données dans votre projet

Les données de votre copilote sont constituées d’un ensemble de brochures de voyage au format PDF provenant de l’agence de voyage fictive *Margie’s Travel*. Ajoutons-les au projet.

1. Téléchargez l’[archive compressée des brochures](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) depuis `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` et décompressez-la dans un dossier nommé **brochures** sur votre système de fichiers local.
1. Dans le portail Azure AI Foundry, dans votre projet, dans le volet de navigation de gauche, dans **Mes ressources**, sélectionnez la page **Données + index**.
1. Sélectionnez **+ Nouvelles données**.
1. Dans l’Assistant **Ajouter vos données**, développez le menu déroulant pour sélectionner **Charger des fichiers/dossiers**.
1. Sélectionnez **Charger un dossier**, puis sélectionnez le dossier **brochures**.
1. Sélectionnez **Suivant** et définissez le nom des données sur `brochures`.
1. Attendez que le dossier soit chargé et notez qu’il contient plusieurs fichiers .pdf.

## Créer un index pour vos données

Maintenant que vous avez ajouté une source de données à votre projet, vous pouvez l’utiliser pour créer un index dans votre ressource Recherche Azure AI.

1. Dans le portail Azure AI Foundry, dans votre projet, dans le volet de navigation de gauche, dans **Mes ressources**, sélectionnez la page **Données + index**.
1. Dans l’onglet **Index**, ajoutez un nouvel index avec les paramètres suivants :
    - **Emplacement source** :
        - **Source de données** : données dans le portail Azure AI Foundry
            - *Sélectionnez la source de données **brochures***
    - **Configuration de l’index** :
        - **Sélectionnez Service Recherche Azure AI** : *Sélectionnez la connexion **AzureAISearch** à votre ressource Recherche Azure AI*
        - **Index vectoriel** : `brochures-index`
        - **Machine virtuelle** : Sélection automatique
    - **Paramètres de recherche** :
        - **Paramètres de vecteur** : Ajouter la recherche vectorielle à cette ressource de recherche
        - **Connexion Azure OpenAI** : *Sélectionner la ressource Azure OpenAI par défaut pour votre hub*
        
1. Attendez que le processus d'indexation soit terminé, ce qui peut prendre plusieurs minutes. L’opération de création d’index est constituée des travaux suivants :

    - Déchiffrez, segmentez et incorporez des jetons de texte dans les données de vos brochures.
    - Créez l’index Recherche Azure AI.
    - Inscrivez la ressource d’index.

## Tester l’index

Avant d’utiliser votre index dans un flux d’invite basé sur RAG, nous allons vérifier qu’il peut être utilisé pour affecter les réponses de l’IA générative.

1. Dans le volet de navigation de gauche, sélectionnez la page **Terrains de jeu**.
1. Dans la page Conversation, dans le volet Configuration, vérifiez que votre modèle de déploiement **gpt-35-turbo-16k** est sélectionné. Ensuite, dans le panneau de session de conversation instantanée, soumettez l’invite `Where can I stay in New York?`
1. Examinez la réponse, qui doit être une réponse générique du modèle sans aucune donnée de l’index.
1. Dans le volet Configuration, développez le champ **Ajouter vos données**, puis ajoutez l’index du projet **brochures-index**, puis sélectionnez le type de recherche **hybride (vecteur + mot clé)**.

   > **Remarque** : certains utilisateurs peuvent ne pas trouver immédiatement les index nouvellement créés. Actualiser le navigateur permet généralement de résoudre le problème, mais si cela ne suffit pas, vous devrez peut-être attendre que l’index soit reconnu.

1. Une fois l’index ajouté et la session de conversation redémarrée, soumettez à nouveau l’invite `Where can I stay in New York?`
1. Examinez la réponse, qui doit être basée sur les données de l’index.

## Utiliser l’index dans un flux d’invite

Votre index vectoriel a été enregistré dans votre projet Azure AI Foundry, ce qui vous permet de l’utiliser facilement dans un flux d’invite.

1. Dans le portail Azure AI Foundry, dans votre projet, dans le volet de navigation de gauche, dans **Créer et personnaliser**, sélectionnez la page **Flux d’invite**.
1. Créez un flux d’invite en clonant l’exemple **Multi-Round Q&A on Your Data** (Questions et réponses à plusieurs tours sur vos données) dans la galerie. Enregistrez votre clone de cet exemple dans un dossier nommé `brochure-flow`.
    <details>  
      <summary><b>Conseil de résolution des problèmes</b> : erreur d’autorisations</summary>
        <p>Si vous recevez une erreur d’autorisations lorsque vous créez un flux d’invite, essayez ce qui suit :</p>
        <ul>
          <li>Dans le Portail Azure, sélectionnez la ressource AI Services.</li>
          <li>Dans l’onglet Identité, dans Gestion des ressources, vérifiez qu’il s’agit d’une identité managée affectée par le système.</li>
          <li>Accédez au compte de stockage associé. Sur la page IAM, ajoutez une attribution de rôle <em>Lecteur des données blob du stockage</em>.</li>
          <li>Sous <strong>Attribuer l’accès à</strong>, sélectionnez <strong>Identité managée</strong>, <strong>+ Sélectionner des membres</strong>, puis <strong>Toutes les identités managées affectées par le système</strong> et sélectionnez votre ressource Azure AI Services.</li>
          <li>À l’aide de Passer en revue et attribuer, enregistrez les nouveaux paramètres et procédez à nouveau à l’étape précédente.</li>
        </ul>
    </details>

1. Quand la page du concepteur de flux d’invite s’ouvre, passez en revue **brochure-flow**. Son graphique devrait ressembler à l’image suivante :

    ![Une capture d’écran d’un graphique de flux d’invite](./media/chat-flow.png)

    L’exemple de flux d’invite que vous utilisez implémente la logique d’invite pour une application de conversation dans laquelle l’utilisateur peut soumettre de façon itérative une entrée de texte à l’interface de conversation. L’historique de la conversation est conservé et inclus dans le contexte de chaque itération. Le flux d’invite orchestre une séquence d’*outils* pour :

    - Ajoutez l’historique à l’entrée de conversation pour définir une invite sous la forme d’une forme contextualisée d’une question.
    - Récupérez le contexte à l’aide de votre index et d’un type de requête de votre choix en fonction de la question.
    - Générer un contexte d’invite en utilisant les données récupérées dans l’index pour augmenter la question.
    - Créez des variantes d’invite en ajoutant un message système et en structurant l’historique de la conversation.
    - Soumettre l’invite à un modèle de langage pour générer une réponse en langage naturel.

1. Utilisez le bouton **Démarrer la session de calcul** pour démarrer le calcul d’exécution du flux.

    Attendez le démarrage du runtime. Ceci fournit un contexte de calcul pour le flux d’invite. Pendant que vous attendez, sous l’onglet **Flux**, passez en revue les sections pour les outils dans le flux.

1. Dans la section **Entrées**, vérifiez que les entrées incluent :
    - **chat_history**
    - **chat_input**

    L’historique de conversation par défaut de cet exemple inclut une conversation sur l’IA.

1. Dans la section **Sorties**, vérifiez que la sortie inclut :

    - **chat_output** avec la valeur ${chat_with_context.output}

1. Dans la section **modify_query_with_history**, sélectionnez les paramètres suivants (en laissant les autres tels qu’ils sont) :

    - **Connexion** : *La ressource Azure OpenAI par défaut pour votre hub IA*
    - **Api**: conversation
    - **deployment_name** : gpt-35-turbo-16k
    - **response_format** : {"type":"text"}

1. Attendez que la session de calcul démarre, puis dans la section de **recherche** , définissez les valeurs de paramètre suivantes :

    - **mlindex_content** : *Sélectionnez le champ vide pour ouvrir le volet Générer*
        - **index_type** : Index inscrit
        - **mlindex_asset_id** : brochures-index : 1
    - **requêtes**: ${modify_query_with_history.output}
    - **query_type** : Hybride (vecteur + mot clé)
    - **top_k** : 2

1. Dans la section **generate_prompt_context**, passez en revue le script Python et vérifiez que les **entrées** pour cet outil incluent le paramètre suivant :

    - **search_result** *(objet)*  : ${lookup.output}

1. Dans la section **Prompt_variants**, passez en revue le script Python et vérifiez que les **entrées** pour cet outil incluent le paramètre suivant :

    - **contexts** *(string)* : ${generate_prompt_context.output}
    - **chat_history** *(string)* : ${inputs.chat_history}
    - **chat_input** *(string)* : ${inputs.chat_input}

1. Dans la section **chat_with_context**, sélectionnez les paramètres suivants (en laissant les autres tels qu’ils sont) :

    - **Connexion** : Default_AzureOpenAI
    - **Api** : Conversation instantanée
    - **deployment_name** : gpt-35-turbo-16k
    - **response_format** : {"type":"text"}

    Vérifiez ensuite que les **entrées** pour cet outil incluent les paramètres suivants :
    - **prompt_text** *(string)* : ${Prompt_variants.output}

1. Dans la barre d’outils, utilisez le bouton **Enregistrer** pour enregistrer les modifications que vous avez apportées aux outils dans le flux d’invite.
1. Dans la barre d’outils, sélectionnez **Conversation**. Un volet de conversation s’ouvre avec l’exemple d’historique de conversation et l’entrée déjà renseignée en fonction des exemples de valeurs. Vous pouvez les ignorer.
1. Dans le volet de conversation, remplacez l’entrée par défaut par la question `Where can I stay in London?`, puis soumettez-la.
1. Examinez la réponse, qui doit être basée sur les données de l’index.
1. Passez en revue les sorties de chaque outil dans le flux.
1. Dans le volet de conversation, entrez la question `What can I do there?`
1. Examinez la réponse, qui doit être basée sur les données de l’index et prendre en compte l’historique de la conversation (« there » est donc compris comme étant « London »).
1. Examinez les sorties de chaque outil dans le flux, en notant comment chaque outil du flux a fonctionné sur ses entrées pour préparer une invite contextualisée et obtenir une réponse appropriée.

## Déployer le flux

Maintenant que vous disposez d’un flux opérationnel qui utilise vos données indexées, vous pouvez le déployer en tant que service pour être consommé par une application copilote.

> **Remarque** : en fonction de la région et de la charge du centre de données, les déploiements peuvent parfois prendre un certain temps et génèrent par moments une erreur lors de l’interaction avec le déploiement. N’hésitez pas à passer à la section défi ci-dessous pendant le déploiement ou ignorez le test de votre déploiement si vous manquez de temps.

1. Dans la barre d’outils, sélectionnez **Déployer**.
1. Créez un déploiement avec les paramètres suivants :
    - **Paramètres de base** :
        - **Point de terminaison** : Nouvelle
        - **Nom du point de terminaison** : *Utilisez le nom de point de terminaison unique par défaut*
        - **Nom du déploiement** : *Utilisez le nom de point de terminaison de déploiement par défaut*
        - **Machine virtuelle** : Standard_DS3_v2
        - **Nombre d’instances** : 3
        - **Collecte des données d’inférence** : Sélectionné
    - **Paramètres avancés** :
        - *Utiliser les paramètres par défaut*
1. Dans le portail Azure AI Foundry, dans votre projet, dans le volet de navigation de gauche, dans **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
1. Continuez à actualiser la vue jusqu’à ce que le déploiement **brochure-endpoint-1** ait l’état *réussi* sous le point de terminaison **brochure-endpoint** (ceci peut prendre un certain temps).
1. Une fois le déploiement réussi, sélectionnez-le. Ensuite, dans sa page **Test**, entrez l’invite `What is there to do in San Francisco?` et examinez la réponse.
1. Entrez l’invite `Where else could I go?` et examinez la réponse.
1. Visualisez la page **Consommer** pour le point de terminaison et notez qu’elle contient des informations de connexion et un exemple de code que vous pouvez utiliser pour créer une application cliente pour votre point de terminaison, ce qui vous permet d’intégrer la solution de flux d’invite dans une application en tant que copilote personnalisé.

## Défi 

Maintenant que vous avez découvert comment intégrer vos propres données dans une application d’IA générative créée avec le portail Azure AI Foundry, allons plus loin.

Essayez d’ajouter une nouvelle source de données via le portail Azure AI Foundry, indexez-la et intégrez les données indexées dans un flux d’invite. Voici quelques jeux de données que vous pouvez essayer :

- Une collection d’articles (de recherche) que vous avez sur votre ordinateur.
- Un ensemble de présentations de conférences passées.
- N’importe quels jeux de données disponibles dans le référentiel [Exemples de données Recherche Azure](https://github.com/Azure-Samples/azure-search-sample-data).

Utilisez toutes vos ressources possibles pour créer votre source de données et l’intégrer dans votre flux d’invite. Essayez le nouveau flux d’invite et envoyez des invites auxquelles seul le jeu de données que vous avez choisi peut répondre !

## Nettoyage

Pour éviter des coûts Azure inutiles et une utilisation non nécessaire des ressources, vous devez supprimer les ressources que vous avez déployées dans cet exercice.

1. Si vous avez terminé d’explorer Azure AI Foundry, revenez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous si nécessaire en utilisant vos informations d’identification Azure. Supprimez ensuite les ressources dans le groupe de ressources où vous avez provisionné vos ressources Recherche Azure AI et Azure AI.
