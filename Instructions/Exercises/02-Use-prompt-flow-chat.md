---
lab:
  title: Bien démarrer la création de copilotes personnalisés avec un flux d’invite dans Azure AI Studio
---

# Bien démarrer la création de copilotes personnalisés avec un flux d’invite dans Azure AI Studio

Dans cet exercice, vous utiliserez le flux d'invite d’Azure AI Studio pour créer un copilote personnalisé qui utilise une invite de l'utilisateur et l'historique de conversation instantanée comme entrées, et qui utilise un modèle GPT d’Azure OpenAI pour générer un résultat.

> Pour effectuer cet exercice, votre abonnement Azure doit être approuvé pour un accès au service Azure OpenAI. Renseignez le [formulaire d’inscription](https://learn.microsoft.com/legal/cognitive-services/openai/limited-access) pour demander l’accès aux modèles Azure OpenAI.

Pour créer un copilote avec un flux d’invite, vous devez :

- Créez un hub IA et un projet au sein d’Azure AI Studio.
- Déployez un modèle GPT.
- Créer un flux qui utilise le modèle GPT déployé pour générer une réponse basée sur l'entrée de l'utilisateur.
- Tester et déployer le flux.

## Créer un hub IA et un projet dans Azure AI Studio

Vous commencez par créer un projet Azure AI Studio au sein d’un hub Azure AI :

1. Dans un navigateur web, ouvrez [https://ai.azure.com](https://ai.azure.com) et connectez-vous à l’aide de vos informations d’identification Azure.
1. Sélectionnez la page **Accueil**, puis sélectionnez **+ Nouveau projet**.
1. Dans l’Assistant **Créer un projet**, créez ensuite un projet avec les paramètres suivants :
    - **Nom du projet** : *Un nom unique pour votre projet*
    - **Hub** : *Créer un hub avec les paramètres suivants :*
        - **Hub name** : *Un nom unique*
        - **Abonnement** : *votre abonnement Azure*
        - **Groupe de ressources** : *Un nouveau groupe de ressources*
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
    - **Connecter Azure AI Services ou Azure OpenAI** : *Créer une connexion*
    - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \* Les ressources Azure OpenAI sont limitées au niveau du locataire par quotas régionaux. Les régions répertoriées incluent le quota par défaut pour les types de modèle utilisés dans cet exercice. Le choix aléatoire d’une région réduit le risque qu’une seule région atteigne sa limite de quota. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région. En savoir plus sur la [disponibilité du modèle par région](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Examinez votre configuration et créez votre projet.
1. Patientez entre 5 et 10 minutes jusqu’à la création de votre projet.

## Déployer un modèle GPT

Pour utiliser un modèle de langage dans le flux d’invite, vous devez d’abord déployer un modèle. Azure AI Studio vous permet de déployer des modèles OpenAI à utiliser dans vos flux.

1. Dans le volet de navigation de gauche, sous **Composants**, sélectionnez la page **Déploiements**.
1. Créez un déploiement du modèle **gpt-35-turbo** avec les paramètres suivants :
    - **Nom du déploiement** : *Un nom unique pour votre modèle de déploiement*
    - **Version du modèle** : *Sélectionnez la version par défaut*
    - **Type de déploiement** : Standard
    - **Ressource Azure OpenAI connectée** : *Sélectionner la connexion par défaut*
    - **Limite de débit en jetons par minute (en milliers)** : 5 000
    - **Filtre de contenu** : valeur par défaut
1. Attendez que le modèle soit déployé. Lorsque le déploiement est prêt, sélectionnez **Ouvrir dans le terrain de jeu**.
1. Dans la fenêtre de conversation, entrez la requête `What can you do?`.

    Notez que la réponse est générique car il n'y a pas d'instructions spécifiques pour l'assistant. Pour qu'il se concentre sur une tâche, vous pouvez modifier l'invite du système.

1. Remplacez le **message système** par les éléments suivants :

   ```
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. Sélectionnez **Appliquer les modifications**.
1. Dans la fenêtre de conversation, entrez la même requête que précédemment : `What can you do?` Notez la modification en réponse.

Maintenant que vous avez joué avec le message système pour le modèle GPT déployé, vous pouvez personnaliser davantage l'application en travaillant avec le flux d'invite.

## Créer et exécuter un flux de conversation dans Azure AI Studio

Vous pouvez créer un nouveau flux à partir d'un modèle ou créer un flux basé sur vos configurations dans l'aire de jeu. Comme vous avez déjà fait des expériences dans l'aire de jeu, vous utiliserez cette option pour créer un nouveau flux.

1. Dans **terrain de jeu de conversation**, sélectionnez **Flux d’invite** dans la barre supérieure.
1. Entrez `Travel-Chat` comme nom du dossier.

    Un flux de conversation instantanée simple est créé pour vous. Notez qu'il y a deux entrées (l'historique du chat et la question de l'utilisateur), un nœud LLM qui se connectera à votre modèle linguistique déployé, et une sortie pour refléter la réponse dans le chat.

    Pour être capable de tester votre flux, vous avez besoin de calcul.

1. Sélectionnez **Démarrer la session de calcul** dans la barre supérieure.
1. La session de calcul prend 1 à 3 minutes.
1. Trouvez le nœud LLM nommé **conversation**. Notez que l'invite comprend déjà l'invite système que vous avez spécifiée dans l'aire de jeu du chat.

    Vous devez encore connecter le nœud LLM à votre modèle déployé.

1. Dans la section nœud LLM, pour **Connexion**, sélectionnez la connexion qui a été créée pour vous lorsque vous avez créé le hub IA.
1. Pour **Api**, sélectionnez **conversation**.
1. Pour la valeur **deployment_name**, sélectionnez le modèle **gpt-35-turbo** que vous avez déployé.
1. Pour **response_format**, sélectionnez **{"type":"text"}**.
1. Passez en revue le champ d’invite et vérifiez qu’il ressemble à ce qui suit :

   ```yml
   {% raw %}
   system:
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.

   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.

   {% for item in chat_history %}
   user:
   {{item.inputs.question}}
   assistant:
   {{item.outputs.answer}}
   {% endfor %}

   user:
   {{question}}
   {% endraw %}
   ```

### Tester et déployer le flux

Votre flux étant à présent développé, vous pouvez utiliser la fenêtre de conversation pour tester le flux.

1. Assurez-vous que la session de calcul est en cours d'exécution.
1. Cliquez sur **Enregistrer**.
1. Sélectionnez **Conversation** pour tester le flux.
1. Entrez la requête : `I have one day in London, what should I do?` et passez en revue la sortie.

    Lorsque vous êtes satisfait du comportement du flux que vous avez créé, vous pouvez le déployer.

1. Sélectionnez **Déployer** pour déployer le flux avec les paramètres suivants :
    - **Paramètres de base** :
        - **Point de terminaison** : Nouvelle
        - **Nom du point de terminaison** : *Entrez un nom unique*
        - **Nom du déploiement** : *Entrez un nom unique*
        - **Machine virtuelle** : Standard_DS3_v2
        - **Nombre d’instances** : 3
        - **Collecte des données d’inférence** : Activé
    - **Paramètres avancés** :
        - *Utiliser les paramètres par défaut*
1. Dans Azure AI Studio, dans votre projet, dans le volet de navigation de gauche, sous **Composants**, sélectionnez la page **Déploiements**.
1. Notez que par défaut, les **déploiements de modèle** sont répertoriés, y compris votre modèle de langage déployé.
1. Sélectionnez l’onglet **Déploiements d’applications** pour rechercher votre flux déployé. Il peut s'écouler un certain temps avant que le déploiement ne soit répertorié et créé avec succès.
1. Une fois le déploiement réussi, sélectionnez-le. Ensuite, dans sa page **Test**, entrez l’invite `What is there to do in San Francisco?` et examinez la réponse.
1. Entrez l’invite `Where else could I go?` et examinez la réponse.
1. Visualisez la page **Consommer** pour le point de terminaison et notez qu’elle contient des informations de connexion et un exemple de code que vous pouvez utiliser pour créer une application cliente pour votre point de terminaison, ce qui vous permet d’intégrer la solution de flux d’invite dans une application en tant que copilote personnalisé.

## Supprimer les ressources Azure

Une fois l’exploration d’Azure AI Studio terminée, supprimez les ressources créées afin d’éviter des coûts Azure superflus.

- Accédez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com`.
- Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
- Sélectionnez le groupe de ressources créé pour cet exercice.
- Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
- Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.
