---
lab:
  title: Créer un copilote personnalisé qui utilise vos propres données
---

# Créer un copilote personnalisé qui utilise vos propres données

La génération augmentée e récupération (RAG, Retrieval Augmented Generation) est une technique utilisée pour créer des applications qui intègrent des données provenant de sources de données personnalisées dans une invite pour un modèle d’IA générative. RAG est un modèle couramment utilisé pour développer des *copilotes* personnalisés, qui sont des applications basées sur la conversation utilisant un modèle de langage pour interpréter les entrées et générer des réponses appropriées.

Dans cet exercice, vous allez utiliser Azure AI Studio pour intégrer des données personnalisées dans un flux d’invite d’IA générative.

> **Remarque** : Azure AI Studio est en préversion au moment de la rédaction du présent document et fait l’objet d’un développement actif. Certains éléments du service peuvent ne pas être exactement tels qu’ils sont décrits, et certaines fonctionnalités peuvent ne pas fonctionner comme prévu.

Cet exercice prend environ **45** minutes.

## Créer une ressource Recherche Azure AI

Votre solution de copilote va intégrer des données personnalisées dans un flux d’invite. Pour prendre en charge cette intégration, vous aurez besoin d’une ressource Recherche Azure AI avec laquelle indexer vos données.

1. Dans un navigateur web, ouvrez le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.
1. Dans la page d’accueil, sélectionnez **+ Créer une ressource**, puis recherchez `Azure AI Search`. Créez ensuite une ressource Recherche Azure AI avec les paramètres suivants :
    - **Abonnement** : *Sélectionnez votre abonnement Azure*.
    - **Groupe de ressources** : *sélectionnez ou créez un groupe de ressources*.
    - **Nom du service** : *Entrez un nom de service unique*
    - **Emplacement** : *Sélectionnez un des emplacements disponibles*
    - **Niveau tarifaire** : Standard
1. Attendez que votre déploiement de la ressource Recherche Azure AI soit terminé.

## Créer un projet Azure AI

Vous êtes maintenant prêt à créer un projet Azure AI Studio et les ressources Azure AI pour le prendre en charge.

1. Dans un navigateur web, ouvrez [Azure AI Studio](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.
1. Dans la page **Build**, sélectionnez **+ Nouveau projet**. Ensuite, dans l’Assistant **Création d’un projet**, créez un projet avec les paramètres suivants :
    - **Nom du projet** : *Un nom unique pour votre projet*
    - **Hub AI** : *Créez une ressource avec les paramètres suivants :*
        - **Nom du hub IA** : *Un nom unique*
        - **Abonnement Azure** : *Votre abonnement Azure*
        - **Groupe de ressources** : *Sélectionnez le groupe de ressources contenant votre ressource Recherche Azure AI*
        - **Emplacement** : *Le même emplacement que votre ressource Recherche Azure AI (ou un emplacement géographiquement proche)*
        - **Azure OpenAI** : (Nouveau) *nom_hub*
        - **Recherche Azure AI** : *Sélectionnez votre ressource Recherche Azure AI*
1. Attendez que votre projet soit créé.

## Déployer des modèles

Vous aurez besoin de deux modèles pour implémenter votre solution :

- Un modèle *d’incorporation* pour vectoriser les données texte pour une indexation et un traitement efficaces.
- Un modèle qui peut générer des réponses en langage naturel aux questions en fonction de vos données.

1. Dans Azure AI Studio, dans votre projet, dans le volet de navigation de gauche, sous **Composants**, sélectionnez la page **Déploiements**.
1. Créez un déploiement (en utilisant un point de terminaison en temps réel) du modèle **text-embedding-ada-002** avec le nom `text-embedding-ada-002`. Définissez les options **Avancées** pour utiliser le filtre de contenu par défaut et pour limiter les jetons par minute (TPM) à **5 000**.
1. Créez un déploiement du modèle **gpt-35-turbo** avec le nom `gpt-35-turbo`. Définissez les options **Avancées** pour utiliser le filtre de contenu par défaut et pour limiter les jetons par minute (TPM) à **5 000**.

> **Remarque** : La réduction du nombre de jetons par minute permet d’éviter une surutilisation du quota disponible dans l’abonnement que vous utilisez. 5 000 jetons par minute sont suffisants pour les données utilisées dans cet exercice.

## Ajouter des données dans votre projet

Les données de votre copilote sont constituées d’un ensemble de brochures de voyage au format PDF provenant de l’agence de voyage fictive *Margie’s Travel*. Ajoutons-les au projet.

1. Téléchargez l’[archive compressée des brochures](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) depuis `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` et décompressez-la dans un dossier nommé **brochures** sur votre système de fichiers local.
1. Dans Azure AI Studio, dans votre projet, dans le volet de navigation de gauche, sous **Composants**, sélectionnez la page **Données**.
1. Sélectionnez **+ Nouvelles données** et ajoutez une nouvelle connexion de source de données en chargeant le dossier **brochures** (choisissez l’option de chargement d’un *dossier*, et non pas d’un *fichier*). Nommez la nouvelle source de données **brochures**.

## Créer un index pour vos données

Maintenant que vous avez ajouté une source de données à votre projet, vous pouvez l’utiliser pour créer un index dans votre ressource Recherche Azure AI.

1. Dans Azure AI Studio, dans votre projet, dans le volet de navigation de gauche, sous **Composants**, sélectionnez la page **Index**.
1. Ajoutez un nouvel index avec les paramètres suivants :
    - **Données sources** :
        - **Source de données** : Utiliser des données de projet existantes
            - *Sélectionnez la source de données **brochures***
    - **Stockage de l’index** :
        - *Sélectionnez la connexion **AzureAISearch** à votre ressource Recherche Azure AI*
    - **Paramètres de recherche** :
        - **Paramètres de vecteur** : Ajouter la recherche vectorielle à cette ressource de recherche
        - **Ressource Azure OpenAI** : Default_AzureOpenAI
        - *Confirmer qu’un modèle d’incorporation va être déployé*
    - **Paramètres d’index** :
        - **Nom de l’index** : brochures-index
        - **Machine virtuelle** : Sélection automatique
1. Attendez que votre index soit prêt, ce qui peut prendre plusieurs minutes. L’opération de création d’index est constituée des travaux suivants :
    - Décomposer, segmenter et incorporer les jetons de texte dans les données de vos brochures
    - Mettre à jour l’index
    - Inscrire la ressource d’index

## Tester l’index

Avant d’utiliser votre index dans un flux d’invite basé sur RAG, nous allons vérifier qu’il peut être utilisé pour affecter les réponses de l’IA générative.

1. Dans le volet de navigation de gauche, sous **Outils**, sélectionnez la page **Playground**.
1. Dans la page Playground, dans le volet **Configuration**, vérifiez que votre modèle de déploiement **gpt-35-turbo** est sélectionné. Ensuite, dans le volet **Session de conversation**, soumettez l’invite `Where can I stay in New York?`.
1. Examinez la réponse, qui doit être une réponse générique du modèle sans aucune donnée de l’index.
1. Dans le volet **Configuration de l’Assistant**, sélectionnez **Ajouter vos données**, puis ajoutez une source de données avec les paramètres suivants :
    - **Source des données** :
        - **Sélectionner la source de données** : Recherche Azure AI
        - **Abonnement** : *votre abonnement Azure*
        - **Service Recherche Azure AI** : *Votre ressource Recherche Azure AI*
        - **Index Recherche Azure AI** : brochures-index
        - **Ajouter une recherche vectorielle** : <u>non</u> sélectionné
    - **Mappage des champs de données** :
        - **Données de contenu** : contenu
        - **Nom de fichier** : chemin d’accès du fichier
        - **Titre** : titre
        - **URL** : url
    - **Gestion des données** :
        - **type de recherche** : mot clé
1. Une fois la source de données ajoutée et la session de conversation redémarrée, soumettez à nouveau l’invite `Where can I stay in New York?`
1. Examinez la réponse, qui doit être basée sur les données de l’index.

## Utiliser l’index dans un flux d’invite

Votre index vectoriel a été enregistré dans votre projet Azure AI Studio, ce qui vous permet de l’utiliser facilement dans un flux d’invite.

1. Dans Azure AI Studio, dans votre projet, dans le volet de navigation de gauche, sous **Composants**, sélectionnez **Données**.
1. Sélectionnez le dossier **brochures-index** qui contient les données pour l’index que vous avez créé précédemment.
1. Dans la section **Liens de données** pour votre index, copiez la valeur de **URI de stockage** dans le Presse-papiers (il doit ressembler à `https://xxx.blob.core.windows.net/xxx/azureml/xxx/index/`). Vous aurez besoin de cet URI pour vous connecter aux données de votre index dans le flux d’invite.
1. Dans votre projet, dans le volet de navigation de gauche, sous **Outils**, sélectionnez la page **Flux d’invite**.
1. Créez un flux d’invite en clonant l’exemple **Multi-Round Q&A on Your Data** (Questions et réponses à plusieurs tours sur vos données) dans la galerie. Enregistrez votre clone de cet exemple dans un dossier nommé `brochure-flow`.
1. Quand la page du concepteur de flux d’invite s’ouvre, passez en revue **brochure-flow**. Son graphique devrait ressembler à l’image suivante :

    ![Une capture d’écran d’un graphique de flux d’invite](./media/brochure-flow.png)

    L’exemple de flux d’invite que vous utilisez implémente la logique d’invite pour une application de conversation dans laquelle l’utilisateur peut soumettre de façon itérative une entrée de texte à l’interface de conversation. L’historique de la conversation est conservé et inclus dans le contexte de chaque itération. Le flux d’invite orchestre une séquence d’*outils* pour :

    1. Ajoutez l’historique à l’entrée de conversation pour définir une invite sous la forme d’une forme contextualisée d’une question.
    1. Créez une *incorporation* pour la question (utilisez un modèle d’incorporation pour convertir le texte en vecteurs).
    1. Rechercher dans un index vectoriel des informations pertinentes en fonction de la question.
    1. Générer un contexte d’invite en utilisant les données récupérées dans l’index pour augmenter la question.
    1. Créez des variantes d’invite en ajoutant un message système et en structurant l’historique de la conversation.
    1. Soumettre l’invite à un modèle de langage pour générer une réponse en langage naturel.

1. Dans la liste **Runtime**, sélectionnez **Démarrer** pour démarrer le runtime automatique. Attendez ensuite qu’il démarre. Ceci fournit un contexte de calcul pour le flux d’invite. Pendant que vous attendez, sous l’onglet **Flux**, passez en revue les sections pour les outils dans le flux.
1. Dans la section **Entrées**, vérifiez que les entrées incluent **chat_history** et **chat_input**. L’historique de conversation par défaut de cet exemple inclut une conversation sur l’IA.
1. Dans la section **Sorties**, vérifiez que la valeur de **chat_output** est *${chat_with_context.output}*.
1. Dans la section **modify_query_with_history**, sélectionnez les paramètres suivants (en laissant les autres tels qu’ils sont) :
    - **Connexion** : Default_AzureOpenAI
    - **Api** : Conversation instantanée
    - **deployment_name** : gpt-35-turbo
    - **response_format** : {"type":"text"}
1. Dans la section **embed_the_question**, définissez les valeurs de paramètre suivantes :
    - **Connexion** *(Azure OpenAI, OpenAI)* : Default_AzureOpenAI
    - **deployment_name** *(string)* : text-embedding-ada-00
    - **input** *(string)* : ${modify_query_with_history.output}
1. Dans la section **search_question_from_indexed_docs**, définissez les valeurs de paramètre suivantes :
    - **path** *(string)* : *Supprimez l’URI existant et collez l’URI de votre index vectoriel*
    - **query** *(object)* : ${embed_the_question.output}
    - **top_k** *(int)* : 2
1. Dans la section **generate_prompt_context**, passez en revue le script Python et vérifiez que les **entrées** pour cet outil incluent le paramètre suivant :
    - **search_result** *(object)* : ${search_question_from_indexed_docs.output}
1. Dans la section **Prompt_variants**, passez en revue le script Python et vérifiez que les **entrées** pour cet outil incluent le paramètre suivant :
    - **contexts** *(string)* : ${generate_prompt_context.output}
    - **chat_history** *(string)* : ${inputs.chat_history}
    - **chat_input** *(string)* : ${inputs.chat_input}
1. Dans la section **chat_with_context**, sélectionnez les paramètres suivants (en laissant les autres tels qu’ils sont) :
    - **Connexion** : Default_AzureOpenAI
    - **Api** : Conversation instantanée
    - **deployment_name** : gpt-35-turbo
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

1. Dans la barre d’outils, sélectionnez **Déployer**.
1. Créez un déploiement avec les paramètres suivants :
    - **Paramètres de base** :
        - **Point de terminaison** : Nouvelle
        - **Nom du point de terminaison** : brochure-endpoint
        - **Nom du déploiement** : brochure-endpoint-1
        - **Machine virtuelle** : Standard_DS3_v2
        - **Nombre d’instances** : 3
        - **Collecte des données d’inférence** : Sélectionné
        - **Diagnostics Application Insights** : Sélectionné
    - **Paramètres avancés** :
        - *Utiliser les paramètres par défaut*
1. Dans Azure AI Studio, dans votre projet, dans le volet de navigation de gauche, sous **Composants**, sélectionnez la page **Déploiements**.
1. Continuez à actualiser la vue jusqu’à ce que le déploiement **brochure-endpoint-1** ait l’état *réussi* sous le point de terminaison **brochure-endpoint** (ceci peut prendre un certain temps).
1. Une fois le déploiement réussi, sélectionnez-le. Ensuite, dans sa page **Test**, entrez l’invite `What is there to do in San Francisco?` et examinez la réponse.
1. Entrez l’invite `Where else could I go?` et examinez la réponse.
1. Visualisez la page **Consommer** pour le point de terminaison et notez qu’elle contient des informations de connexion et un exemple de code que vous pouvez utiliser pour créer une application cliente pour votre point de terminaison, ce qui vous permet d’intégrer la solution de flux d’invite dans une application en tant que copilote personnalisé.

## Nettoyage

Pour éviter des coûts Azure inutiles et une utilisation non nécessaire des ressources, vous devez supprimer les ressources que vous avez déployées dans cet exercice.

1. Dans Azure AI Studio, affichez la page **Build**. Sélectionnez ensuite le projet que vous avez créé dans cet exercice et utilisez le bouton **Supprimer le projet** pour le supprimer. La suppression de tous les composants peut prendre quelques minutes.
1. Si vous avez terminé d’explorer Azure AI Studio, revenez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous si nécessaire en utilisant vos informations d’identification Azure. Supprimez ensuite le groupe de ressources que vous avez créé pour vos ressources Recherche Azure AI et Azure AI.

