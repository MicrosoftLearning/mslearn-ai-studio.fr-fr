---
lab:
  title: Évaluer les performances d’un modèle d’IA générative
  description: Apprenez à évaluer les modèles et les invites afin d’optimiser les performances de votre application de conversation instantanée et sa capacité à fournir des réponses pertinentes.
---

# Évaluer les performances d’un modèle d’IA générative

Dans cet exercice, vous utiliserez des évaluations manuelles et automatisées pour mesurer les performances d’un modèle dans le portail Azure AI Foundry.

Cet exercice prend environ **30** minutes.

## Créer un projet Azure AI Foundry

Commençons par créer un projet Azure AI Foundry.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les volets Astuces ou Démarrage rapide qui s’ouvrent lors de votre première connexion, et utilisez si nécessaire le logo **Azure AI Foundry** en haut à gauche pour revenir à la page d’accueil, qui ressemble à l’image suivante (fermez le volet **Aide** s’il est ouvert) :

    ![Capture d’écran du portail Azure AI Foundry.](./media/ai-foundry-home.png)

1. Sur la page d’accueil, sélectionnez **+Créer un projet**.
1. Dans l’assistant **Créer un projet**, saisissez un nom valide pour votre projet. Si un hub existant est suggéré, choisissez l’option permettant d’en créer un nouveau. Passez ensuite en revue les ressources Azure qui seront créées automatiquement pour prendre en charge votre hub et votre projet.
1. Sélectionnez **Personnaliser** et spécifiez les paramètres suivants pour votre hub :
    - **Nom du hub** : *un nom valide pour votre hub*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *créez ou sélectionnez un groupe de ressources*
    - **Région** : sélectionnez l’une des régions suivantes\*
        - USA Est 2
        - France Centre
        - Sud du Royaume-Uni
        - Suède Centre
    - **Connecter Azure AI Services ou Azure OpenAI** : *créer une nouvelle ressource AI Services*
    - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \* À la date de rédaction, ces régions prennent en charge l’évaluation des indicateurs de sécurité de l’IA.

1. Sélectionnez **Suivant** et passez en revue votre configuration. Sélectionnez **Créer** et patientez jusqu’à ce que l’opération se termine.
1. Une fois votre projet créé, fermez les conseils affichés et passez en revue la page du projet dans le portail Azure AI Foundry, qui doit ressembler à l’image suivante :

    ![Capture d’écran des détails d’un projet Azure AI dans le portail Azure AI Foundry.](./media/ai-foundry-project.png)

## Déployer des modèles

Dans cet exercice, vous évaluerez les performances d’un modèle gpt-4o-mini. Vous utiliserez également un modèle gpt-4o pour générer des indicateurs d’évaluation assistée par IA.

1. Dans le volet de navigation à gauche de votre projet, dans la section **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
1. Sur la page **Modèles + points de terminaison**, dans l’onglet **Déploiements de modèles**, dans le menu **+ Déployer un modèle**, sélectionnez **Déployer le modèle de base**.
1. Recherchez le modèle **gpt-4** dans la liste, puis sélectionnez-le et confirmez votre choix.
1. Déployez le modèle avec les paramètres suivants en sélectionnant **Personnaliser** dans les détails du déploiement :
    - **Nom du déploiement** : *Un nom valide pour le modèle de déploiement*
    - **Type de déploiement** : standard global
    - **Mise à jour automatique de la version** : Activée
    - **Version du modèle** : *Sélectionnez la version la plus récente disponible*
    - **Ressource IA connectée** : *sélectionnez votre connexion de ressources Azure OpenAI*
    - **Limite de jetons par minute (en milliers)**  : 50K *(ou le maximum disponible dans votre abonnement si inférieur à 50K)*
    - **Filtre de contenu** : DefaultV2

    > **Remarque** : La réduction du nombre de jetons par minute permet d’éviter une surutilisation du quota disponible dans l’abonnement que vous utilisez. 50 000 TPM devraient suffire pour les données utilisées dans cet exercice. Si votre quota disponible est inférieur à cette valeur, vous pourrez tout de même terminer l’exercice, mais vous pourriez rencontrer des erreurs en cas de dépassement de la limite.

1. Attendez la fin du déploiement.
1. Revenez à la page **Modèles + points de terminaison** et répétez les étapes précédentes pour déployer un modèle **gpt-4o-mini** avec les mêmes paramètres.

## Évaluer manuellement un modèle

Vous pouvez examiner manuellement les réponses de modèle en fonction des données de test. L’évaluation manuelle permet de tester différentes entrées afin de vérifier si le modèle se comporte comme attendu.

1. Dans un nouvel onglet de navigateur, téléchargez le fichier [travel_evaluation_data.csv](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.csv) depuis `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.csv`et enregistrez-le dans un dossier local.
1. De retour sur l’onglet du portail Azure AI Foundry, dans le volet de navigation, dans la section **Évaluer et améliorer**, sélectionnez **Évaluation**.
1. Sur la page **Évaluation**, ouvrez l’onglet **Évaluations manuelles**, puis sélectionnez **+ Nouvelle évaluation manuelle**.
1. Modifiez le **Message système** en y saisissant les instructions suivantes pour un assistant de voyage IA :

   ```
   Objective: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   Capabilities:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   Instructions:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. Dans la section **Configurations**, dans la liste **Modèle**, sélectionnez votre déploiement du modèle **gpt-4o-mini**.
1. Dans la section **Résultat de l’évaluation manuelle**, sélectionnez **Importer des données de test** et chargez le fichier **travel_evaluation_data.csv** que vous avez téléchargé précédemment, en effectuant les correspondances suivantes :
    - **Entrée** : Question
    - **Réponse attendue** : ExpectedResponse
1. Passez en revue les questions et les réponses attendues dans le fichier de test - elles serviront à évaluer les réponses générées par le modèle.
1. Sélectionnez **Exécuter** dans la barre supérieure pour générer des sorties pour toutes les questions que vous avez ajoutées en tant qu’entrées. Après quelques minutes, les réponses du modèle devraient s’afficher dans une nouvelle colonne **Sortie**, comme ceci :

    ![Capture d’écran d’une page d’évaluation manuelle dans le portail Azure AI Foundry.](./media/manual-evaluation.png)

1. Analysez les sorties pour chaque question, comparez la réponse du modèle à la réponse attendue et « notez » les résultats en cliquant sur l’icône pouce levé ou baissé en bas à droite de chaque réponse.
1. Une fois les réponses notées, examinez les tuiles de synthèse situées au-dessus de la liste. Ensuite, dans la barre d’outils, sélectionnez **Enregistrer les résultats** et attribuez un nom approprié. L’enregistrement des résultats vous permettra de les retrouver ultérieurement pour une nouvelle évaluation ou une comparaison avec un autre modèle.

## Utiliser l’évaluation automatisée

Comparer manuellement les réponses du modèle à vos attentes peut être utile pour évaluer ses performances, mais cette méthode devient fastidieuse lorsque vous vous attendez à une large variété de questions et réponses, et elle fournit peu d’indicateurs standardisés pour comparer différents modèles et invites.

L’évaluation automatisée vise à pallier ces limites en calculant des indicateurs et en s’appuyant sur l’IA pour évaluer la cohérence, la pertinence et d’autres aspects des réponses.

1. Utilisez la flèche de retour (**&larr;**) à côté du titre de la page **Évaluation manuelle** pour revenir à la page **Évaluation**.
1. Ouvrez l’onglet **Évaluations automatisées**.
1. Sélectionnez **Créer une nouvelle évaluation** et, lorsqu’on vous le demande, choisissez l’option permettant d’évaluer un **Modèle et une invite**
1. Sur la page **Créer une nouvelle évaluation**, dans la section **Informations de base**, examinez le nom d’évaluation généré automatiquement (vous pouvez le modifier si vous le souhaitez) et sélectionnez votre déploiement du modèle **gpt-40-mini**.
1. Modifiez le **Message système** en y entrant les mêmes instructions pour l’assistant de voyage IA que vous avez utilisées précédemment :

   ```
   Objective: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   Capabilities:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   Instructions:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. Dans la section **Configurer les données de test**, notez que vous pouvez utiliser un modèle GPT pour générer des données de test (que vous pourrez ensuite modifier et compléter selon vos attentes), utiliser un jeu de données existant ou importer un fichier. Dans cet exercice, sélectionnez **Utiliser un jeu de données existant**, puis sélectionnez le jeu de données **travel_evaluation_data_csv_xxxx...** (créé lors de l’importation préalable du fichier .csv).
1. Passez en revue les lignes d’exemple du jeu de données, puis, dans la section **Choisir vos colonnes de données**, sélectionnez les correspondances suivantes :
    - **Requête** : Question
    - **Contexte** : *Laissez ce champ vide. Il est utilisé pour évaluer l’« ancrage » lorsque des sources de données contextuelles sont associées au modèle.*
    - **Vérité terrain** : ExpectedAnswer
1. Dans la section **Choisir les éléments à évaluer**, sélectionnez <u>toutes</u> les catégories d’évaluation suivantes :
    - Qualité de l’IA (assistée par l’IA)
    - Risque et sécurité (avec l’assistance de l’IA)
    - Qualité de l’IA (NLP)
1. Dans la liste **Choisir un modèle déployé comme juge**, sélectionnez votre modèle **gpt-4o**. Ce modèle sera utilisé pour évaluer les réponses du modèle **gpt-4o-mini** en termes de qualité linguistique et selon des indicateurs standard pour l’IA générative.
1. Sélectionnez **Créer** pour lancer le processus d’évaluation, puis attendez qu’il se termine. Cette opération peut prendre quelques minutes.

    > **Conseil** : si une erreur indiquant que les autorisations du projet sont en cours de définition s’affiche, attendez une minute puis sélectionnez de nouveau **Créer**. La propagation des autorisations pour un projet nouvellement créé peut prendre un certain temps.

1. Une fois l’évaluation terminée, faites défiler la page si nécessaire jusqu’à la zone **Tableau de bord des métriques** et consultez les indicateurs **Qualité de l’IA (assistée par IA)**  :

    ![Capture d’écran des indicateurs de qualité de l’IA dans le portail Azure AI Foundry.](./media/ai-quality-metrics.png)

    Utilisez les icônes **<sup>(i)</sup>** pour afficher la définition des métriques.

1. Ouvrez l’onglet **Risques et sécurité** pour consulter les indicateurs liés aux contenus potentiellement nuisibles.
1. Ouvrez l’onglet **Qualité de l’IA (TAL)** pour voir les indicateurs standard des modèles d’IA générative.
1. Remontez en haut de la page si nécessaire, puis sélectionnez l’onglet **Données** pour consulter les données brutes issues de l’évaluation. Les données comprennent les métriques associées à chaque entrée ainsi que les explications du raisonnement appliqué par le modèle gpt-4o lors de l’évaluation des réponses.

    ![Capture d’écran des données d’évaluation dans le portail Azure AI Foundry.](./media/evaluation-data.png)

## Nettoyage

Une fois l’exploration d’Azure AI Foundry terminée, supprimez les ressources créées afin d’éviter des coûts Azure superflus.

- Accédez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com`.
- Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
- Sélectionnez le groupe de ressources créé pour cet exercice.
- Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
- Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.
