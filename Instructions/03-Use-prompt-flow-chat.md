---
lab:
  title: Utiliser un flux d’invite pour gérer la conversation dans une application de conversation
  description: Découvrez comment utiliser des flux d’invite pour gérer les dialogues conversationnels et vérifier que les invites sont construites et orchestrées pour obtenir de meilleurs résultats.
---

# Utiliser un flux d’invite pour gérer la conversation dans une application de conversation

Dans cet exercice, vous allez utiliser le flux d’invite du portail Azure AI Foundry pour créer une application de conversation personnalisée qui utilise une invite utilisateur et un historique des conversations comme entrées, et qui utilise un modèle GPT d’Azure OpenAI pour générer un résultat.

Cet exercice prend environ **30** minutes.

## Créer un projet et un hub IA dans le portail Azure AI Foundry

Vous commencez par créer un projet de portail Azure AI Foundry au sein d’un hub Azure AI :

1. Dans un navigateur web, ouvrez [https://ai.azure.com](https://ai.azure.com) et connectez-vous à l’aide de vos informations d’identification Azure.
1. Sur la page d’accueil, sélectionnez **+Créer un projet**.
1. Dans l’assistant **Créer un projet**, vous pouvez voir toutes les ressources Azure qui seront créées automatiquement avec votre projet, ou vous pouvez personnaliser les paramètres suivants en sélectionnant **Personnaliser** avant de sélectionner **Créer** :

    - **Hub name** : *Un nom unique*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *Un nouveau groupe de ressources*
    - **Emplacement** : sélectionnez **Aidez-moi à choisir**, puis sélectionnez **gpt-4** dans la fenêtre de l’assistant de l’emplacement et utilisez la région recommandée.\*
    - **Connecter Azure AI Services ou Azure OpenAI** : (Nouveauté) *permet de remplir automatiquement le nom de votre hub sélectionné*
    - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \* Les ressources Azure OpenAI sont limitées au niveau du locataire par quotas régionaux. Les régions répertoriées dans l’assistant de l’emplacement incluent le quota par défaut pour le ou les types de modèles utilisés dans cet exercice. Le choix aléatoire d’une région réduit le risque qu’une seule région atteigne sa limite de quota. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région. En savoir plus sur la [disponibilité du modèle par région](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#availability)

1. Si vous avez sélectionné **Personnaliser**, sélectionnez **Suivant** et passez en revue votre configuration.
1. Sélectionnez **Créer** et patientez jusqu’à ce que l’opération se termine.

## Déployer un modèle GPT

Pour utiliser un modèle de langage dans le flux d’invite, vous devez d’abord déployer un modèle. Le portail Azure AI Foundry vous permet de déployer des modèles OpenAI à utiliser dans vos flux.

1. Dans le volet de navigation de gauche, dans **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
1. Sélectionnez **+ Déployer le modèle** et **Déployer le modèle de base**. 
1. Créez un déploiement du modèle **gpt-4** avec les paramètres suivants en sélectionnant **Personnaliser** dans les détails du déploiment :
    - **Nom du déploiement** : *Un nom unique pour votre modèle de déploiement*
    - **Type de déploiement** : Standard
    - **Version du modèle** : *Sélectionnez la version par défaut*
    -  **Ressource IA** : *sélectionnez la ressource que vous avez créée précédemment.*
    - **Limite de débit en jetons par minute (en milliers)** : 5 000
    - **Filtre de contenu** : DefaultV2
    - **Enable dynamic quota** : désactivé

    > **Remarque** : si votre emplacement actuel de ressource IA n’a pas de quota disponible pour le modèle que vous souhaitez déployer, vous êtes invité à choisir un autre emplacement où une nouvelle ressource IA sera créée et connectée à votre projet.

1. Attendez que le modèle soit déployé. Lorsque le déploiement est prêt, sélectionnez **Ouvrir dans le terrain de jeu**.
1. Dans la fenêtre de conversation, entrez la requête `What can you do?`.

    Notez que la réponse est générique car il n'y a pas d'instructions spécifiques pour l'assistant. Pour qu'il se concentre sur une tâche, vous pouvez modifier l'invite du système.

1. Remplacez le message **Donner des instructions et du contexte au modèle**, par ce qui suit :

   ```md
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

## Créer et exécuter un flux de conversation dans Azure AI Foundry

Vous pouvez créer un nouveau flux à partir d'un modèle ou créer un flux basé sur vos configurations dans l'aire de jeu. Comme vous avez déjà fait des expériences dans l'aire de jeu, vous utiliserez cette option pour créer un nouveau flux.

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
1. Pour la valeur **deployment_name**, sélectionnez le modèle **gpt-4** que vous avez déployé.
1. Pour **response_format**, sélectionnez **{"type":"text"}**.
1. Passez en revue le champ d’invite et vérifiez qu’il ressemble à ce qui suit :

   ```yml
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
1. Dans le portail Azure AI Foundry, dans votre projet, dans le volet de navigation de gauche, dans **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
1. Notez que par défaut, les **déploiements de modèle** sont répertoriés, y compris votre modèle de langage déployé et le flux déployé. Il peut s'écouler un certain temps avant que le déploiement ne soit répertorié et créé avec succès.
1. Une fois le déploiement réussi, sélectionnez-le. Ensuite, dans sa page **Test**, entrez l’invite `What is there to do in San Francisco?` et examinez la réponse.
1. Entrez l’invite `Where else could I go?` et examinez la réponse.
1. Visualisez la page **Consommer** pour le point de terminaison et notez qu’elle contient des informations de connexion et un exemple de code que vous pouvez utiliser pour créer une application cliente pour votre point de terminaison, ce qui vous permet d’intégrer la solution de flux d’invite dans une application en tant que copilote personnalisé.

## Supprimer les ressources Azure

Une fois l’exploration d’Azure AI Foundry terminée, supprimez les ressources créées afin d’éviter des coûts Azure superflus.

- Accédez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com`.
- Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
- Sélectionnez le groupe de ressources créé pour cet exercice.
- Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
- Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.
