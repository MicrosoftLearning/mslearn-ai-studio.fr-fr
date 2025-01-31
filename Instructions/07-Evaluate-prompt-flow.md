---
lab:
  title: "Évaluer les performances de votre copilote personnalisé dans Azure\_AI\_Foundry"
---

# Évaluer les performances de votre copilote personnalisé dans Azure AI Foundry

Dans cet exercice, vous allez explorer les évaluations intégrées et personnalisées pour évaluer et comparer les performances de vos applications IA avec le portail Azure AI Foundry.

Cet exercice prend environ **30** minutes.

## Créer un hub IA et un projet dans Azure AI Foundry

Vous commencez par créer un projet Azure AI Foundry au sein d’un hub Azure AI :

1. Dans un navigateur web, ouvrez [https://ai.azure.com](https://ai.azure.com) et connectez-vous à l’aide de vos informations d’identification Azure.
1. Sélectionnez la page **Accueil**, puis sélectionnez **+ Nouveau projet**.
1. Dans l’Assistant **Créer un projet**, donnez un nom unique à votre projet, puis sélectionnez **Personnaliser** et définissez les paramètres suivants :
    - **Hub name** : *Un nom unique*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *Un nouveau groupe de ressources*
    - **Emplacement** : sélectionnez **Aidez-moi à choisir**, puis sélectionnez **gpt-35-turbo** dans la fenêtre de l’assistant de l’emplacement et utilisez la région recommandée.\*
    - **Connecter Azure AI Services ou Azure OpenAI** : (Nouveauté) *permet de remplir automatiquement le nom de votre hub sélectionné*
    - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \* Les ressources Azure OpenAI sont limitées au niveau du locataire par quotas régionaux. Les régions répertoriées dans l’assistant de l’emplacement incluent le quota par défaut pour le ou les types de modèles utilisés dans cet exercice. Le choix aléatoire d’une région réduit le risque qu’une seule région atteigne sa limite de quota. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région. En savoir plus sur la [disponibilité du modèle par région](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Sélectionnez **Suivant** et passez en revue votre configuration.
1. Sélectionnez **Créer un projet** et patientez jusqu’à ce que l’opération se termine.

## Déployer un modèle GPT

Pour utiliser un modèle de langage dans le flux d’invite, vous devez d’abord déployer un modèle. Le portail Azure AI Foundry vous permet de déployer des modèles OpenAI à utiliser dans vos flux.

1. Accédez à la page **Modèles + points de terminaison** dans la section **Mes ressources**, à l’aide du menu de gauche.
1. Sélectionnez le bouton **+ Déployer un modèle**, puis sélectionnez l’option **Déployer le modèle de base**.
1. Créez un déploiement du modèle **gpt-35-turbo** avec les paramètres suivants en sélectionnant **Personnaliser** dans l’Assistant **Déployer le modèle** :
    - **Nom du déploiement** : *Un nom unique pour votre modèle de déploiement*
    - **Type de déploiement** : Standard
    - **Version du modèle** : *Sélectionnez la version par défaut*
    -  **Ressource IA** : *sélectionnez la ressource que vous avez créée précédemment.*
    - **Limite de débit en jetons par minute (en milliers)** : 5 000
    - **Filtre de contenu** : DefaultV2
    - **Enable dynamic quota** : désactivé

    > **Remarque** : si votre emplacement actuel de ressource IA n’a pas de quota disponible pour le modèle que vous souhaitez déployer, vous êtes invité à choisir un autre emplacement où une nouvelle ressource IA sera créée et connectée à votre projet.

1. Attendez que le modèle soit déployé. Lorsque le déploiement est prêt, sélectionnez **Ouvrir dans le terrain de jeu**.
1. Dans la zone de texte **Donner des instructions et du contexte au modèle**, remplacez le contenu par ce qui suit :

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
1. Dans la fenêtre de conversation (historique), entrez la requête : `What can you do?` pour vérifier que le modèle de langage se comporte comme prévu.

Maintenant que vous avez déployé un modèle avec un message système mis à jour, vous pouvez évaluer le modèle.

## Évaluer manuellement un modèle de langage dans le portail Azure AI Foundry

Vous pouvez examiner manuellement les réponses de modèle en fonction des données de test. L’examen manuel vous permet de tester différentes entrées une à la fois pour évaluer si le modèle s’exécute comme prévu.

1. Dans le **terrain de jeu de conversation**, sélectionnez la liste déroulante **Évaluer** dans la barre supérieure, puis sélectionnez **Évaluation manuelle**.
1. Remplacez le **message système** par le message que vous avez utilisé ci-dessus (répété ici) :

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

1. Dans la section **Résultat de l’évaluation manuelle**, vous allez ajouter cinq entrées pour lesquelles vous allez passer en revue la sortie. Entrez les cinq questions suivantes sous la forme de cinq **entrées** distinctes :

   `Can you provide a list of the top-rated budget hotels in Rome?`

   `I'm looking for a vegan-friendly restaurant in New York City. Can you help?`

   `Can you suggest a 7-day itinerary for a family vacation in Orlando, Florida?`

   `Can you help me plan a surprise honeymoon trip to the Maldives?`

   `Are there any guided tours available for the Great Wall of China?`

1. Sélectionnez **Exécuter** dans la barre supérieure pour générer des sorties pour toutes les questions que vous avez ajoutées en tant qu’entrées.
1. Vous pouvez maintenant examiner manuellement les sorties de chaque question en sélectionnant l’icône vers le haut ou vers le bas en bas d’une réponse. Évaluez chaque réponse, en vous assurant d’inclure au moins un pouce vers le haut et un pouce vers le bas dans vos évaluations.
1. Sélectionnez **Enregistrer les résultats** dans la barre supérieure. Entrez `manual_evaluation_results` comme nom pour les résultats.
1. À l’aide du menu de gauche, accédez à **Évaluation**.
1. Sélectionnez l’onglet **Évaluations manuelles** pour trouver les évaluations manuelles que vous venez d’enregistrer. Notez que vous pouvez explorer vos évaluations manuelles créées précédemment, continuer là où vous êtes parti et enregistrer les évaluations mises à jour.

## Évaluer votre copilote avec des métriques intégrées

Après avoir créé une application de conversation avec un flux d’invite, vous pouvez évaluer ce flux en effectuant une exécution par lot et en évaluant les performances du flux avec des mesures intégrées.

![Diagramme de la construction du jeu de données d’entrée à des fins d’évaluation.](./media/diagram-dataset-evaluation.png)

Pour évaluer un flux de conversation, les requêtes utilisateur et les réponses de conversation sont fournies comme entrée pour une évaluation.

Pour gagner du temps, nous avons créé un jeu de données de sortie par lot pour vous qui contient les résultats de plusieurs entrées traitées par un flux d’invite. Chacun des résultats est stocké dans le jeu de données que vous allez évaluer à l’étape suivante.

1. Sélectionnez l’onglet **Évaluations automatisées** et créez une **nouvelle évaluation** avec les paramètres suivants : <details>  
      <summary><b>Conseil de résolution des problèmes</b> : erreur d’autorisations</summary>
        <p>Si vous recevez une erreur d’autorisations lorsque vous créez un flux d’invite, essayez ce qui suit :</p>
        <ul>
          <li>Dans le Portail Azure, sélectionnez la ressource AI Services.</li>
          <li>Dans l’onglet Identité, dans Gestion des ressources, vérifiez qu’il s’agit d’une identité managée affectée par le système.</li>
          <li>Accédez au compte de stockage associé. Sur la page IAM, ajoutez une attribution de rôle <em>Lecteur des données blob du stockage</em>.</li>
          <li>Sous <strong>Attribuer l’accès à</strong>, sélectionnez <strong>Identité managée</strong>, <strong>+ Sélectionner des membres</strong>, puis sélectionnez <strong>Toutes les identités managées affectées par le système</strong>.</li>
          <li>À l’aide de Passer en revue et attribuer, enregistrez les nouveaux paramètres et procédez à nouveau à l’étape précédente.</li>
        </ul>
    </details>

    - **Que voulez-vous évaluer ?**  : jeu de données
    - **Nom d’évaluation**: *Entrez un nom unique*
    - Sélectionnez **Suivant**.
    - **Sélectionner les données que vous souhaitez évaluer**: Ajouter votre jeu de données
        - Téléchargez le [jeu de données de validation](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-qa.jsonl) à l’adresse `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-qa.jsonl`, enregistrez-le en tant que fichier JSONL et chargez-le dans l’interface utilisateur.

    > **Remarque** : votre appareil peut par défaut enregistrer le fichier en tant que fichier .txt. Sélectionnez tous les fichiers et supprimez le suffixe .txt pour vous assurer que vous enregistrez le fichier au format JSONL.

    - Sélectionnez **Suivant**.
    - **Sélectionner des métriques**: Cohérence, fluidité
    - **Connexion** : *Votre connexion à vos services IA*
    - **nom de déploiement/Modèle** : *Votre modèle GPT-3.5 déployé*
    - **Requête** : sélectionne la **requête** comme source de données.
    - **Réponse** : sélectionne la **réponse** comme source de données.
      
1. Sélectionnez **Suivant**, puis passez en revue vos données et **soumettez** la nouvelle évaluation.
1. Attendez que les évaluations soient terminées, vous devrez peut-être actualiser.
1. Sélectionnez l’exécution d’évaluation que vous venez de créer.
1. Explorez le **tableau de bord métriques** et **résultat détaillé des métriques**.

## Supprimer les ressources Azure

Une fois l’exploration d’Azure AI Foundry terminée, supprimez les ressources créées afin d’éviter des coûts Azure superflus.

- Accédez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com`.
- Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
- Sélectionnez le groupe de ressources créé pour cet exercice.
- Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
- Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.
