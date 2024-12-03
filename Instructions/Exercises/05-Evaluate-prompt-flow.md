---
lab:
  title: Évaluer les applications linguistiques dans Azure AI Studio
---

# Évaluer les applications linguistiques dans Azure AI Studio

Dans cet exercice, vous allez explorer les évaluations intégrées et personnalisées pour évaluer et comparer les performances de vos applications IA avec Azure AI Studio.

> Pour effectuer cet exercice, votre abonnement Azure doit être approuvé pour un accès au service Azure OpenAI. Renseignez le [formulaire d’inscription](https://learn.microsoft.com/legal/cognitive-services/openai/limited-access) pour demander l’accès aux modèles Azure OpenAI.

Pour créer un copilote avec un flux d’invite, vous devez :

- Créez un hub IA et un projet au sein d’Azure AI Studio.
- Déployez un modèle GPT.
- Évaluez un jeu de données de test avec les métriques intégrées.
- Définissez une métrique d’évaluation personnalisée et exécutez-la sur le jeu de données de test.

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
1. Dans la fenêtre de conversation, entrez la requête : `What can you do?` pour vérifier que le modèle de langage se comporte comme prévu.

Maintenant que vous avez déployé un modèle avec un message système mis à jour, vous pouvez évaluer le modèle.

## Évaluer un modèle de langage dans Azure AI Studio

Vous pouvez examiner manuellement les réponses de modèle en fonction des données de test. L’examen manuel vous permet de tester différentes entrées une à la fois pour évaluer si le modèle s’exécute comme prévu.

1. Dans **terrain de jeu de conversation**, sélectionnez **Évaluer** dans la barre supérieure.
1. Une nouvelle fenêtre s’ouvre avec le message système précédent déjà renseigné et votre modèle déployé sélectionné.
1. Dans la section **Résultat de l’évaluation manuelle**, vous allez ajouter cinq entrées pour lesquelles vous allez passer en revue la sortie. Entrez les cinq questions suivantes sous la forme de cinq **entrées** distinctes :

   `Can you provide a list of the top-rated budget hotels in Rome?`

   `I'm looking for a vegan-friendly restaurant in New York City. Can you help?`

   `Can you suggest a 7-day itinerary for a family vacation in Orlando, Florida?`

   `Can you help me plan a surprise honeymoon trip to the Maldives?`

   `Are there any guided tours available for the Great Wall of China?`

1. Sélectionnez **Exécuter** dans la barre supérieure pour générer des sorties pour toutes les questions que vous avez ajoutées en tant qu’entrées.
1. Vous pouvez maintenant examiner manuellement les sorties de chaque question en sélectionnant l’icône vers le haut ou vers le bas en bas d’une réponse. Évaluez chaque réponse, en vous assurant d’inclure au moins un pouce vers le haut et un pouce vers le bas dans vos évaluations.
1. Sélectionnez **Enregistrer les résultats** dans la barre supérieure. Entrez `manual_evaluation_results` comme nom pour les résultats.
1. À l’aide du menu de gauche, accédez à **Évaluations**.
1. Sélectionnez l’onglet **Évaluations manuelles** pour trouver les évaluations manuelles que vous venez d’enregistrer. Notez que vous pouvez explorer vos évaluations manuelles créées précédemment, continuer là où vous êtes parti et enregistrer les évaluations mises à jour.
1. Sélectionnez l’onglet **Évaluations des métriques** et créez une évaluation avec les paramètres suivants :
    - **Nom d’évaluation**: *Entrez un nom unique*
    - **Quel type de scénario évaluez-vous ?**: Question et réponse sans contexte
    - **Sélectionner les données que vous souhaitez évaluer**: Ajouter votre jeu de données
        - Téléchargez le fichier JSONL https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-qa.jsonl et chargez-le dans l’interface utilisateur.
    - **Sélectionner des métriques**: Cohérence, fluidité
    - **Connexion** : *Votre connexion à vos services IA*
    - **nom de déploiement/Modèle** : *Votre modèle GPT-3.5 déployé*
1. Attendez que les évaluations soient terminées, vous devrez peut-être actualiser.
1. Sélectionnez l’exécution d’évaluation que vous venez de créer.
1. Explorez le **tableau de bord métriques** et **résultat détaillé des métriques**.

## Supprimer les ressources Azure

Une fois l’exploration d’Azure AI Studio terminée, supprimez les ressources créées afin d’éviter des coûts Azure superflus.

- Accédez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com`.
- Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
- Sélectionnez le groupe de ressources créé pour cet exercice.
- Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
- Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.
