---
lab:
  title: Ajuster un modèle de langage
  description: Apprenez à utiliser vos propres données d’entraînement pour ajuster un modèle et personnaliser son comportement.
---

# Ajuster un modèle de langage

Lorsque vous souhaitez qu’un modèle de langage se comporte d’une certaine façon, vous pouvez utiliser l’ingénierie d’invite pour définir le comportement souhaité. Lorsque vous souhaitez améliorer la cohérence du comportement souhaité, vous pouvez choisir d’ajuster un modèle, en le comparant à votre approche d’ingénierie d’invite pour évaluer la méthode la mieux adaptée à vos besoins.

Dans cet exercice, vous allez ajuster un modèle de langage avec Azure AI Foundry afin de l’utiliser pour un scénario d’application de conversation personnalisée. Vous allez comparer le modèle ajusté à un modèle de base pour déterminer si le modèle ajusté correspond mieux à vos besoins.

Imaginez que vous travaillez pour une agence de voyage et que vous développez une application de conversation pour aider les gens à planifier leurs vacances. L’objectif est de créer une conversation simple et inspirante, qui suggère des destinations et des activités avec un ton conversationnel cohérent et chaleureux.

Cet exercice prend environ **60 minutes**\*.

> \***Note** : cette durée est une estimation basée sur une moyenne des expériences. L’ajustement dépend des ressources de l’infrastructure cloud, celles-ci pouvant nécessiter un certain temps d’approvisionnement en fonction de la capacité du centre de données et de la demande simultanée. Certaines activités de cet exercice peuvent prendre <u>beaucoup</u> de temps et nécessiter de la patience. Si des éléments prennent beaucoup de temps, envisagez de consulter la [documentation relative à l’ajustement d’Azure AI Foundry](https://learn.microsoft.com/azure/ai-studio/concepts/fine-tuning-overview) ou de prendre une pause.

## Créer un projet et un hub IA dans le portail Azure AI Foundry

Commençons par créer un projet dans le portail Azure AI Foundry au sein d’un hub Azure AI :

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les conseils ou les volets de démarrage rapide ouverts la première fois que vous vous connectez et, si nécessaire, utilisez le logo **Azure AI Foundry** en haut à gauche pour accéder à la page d’accueil, qui ressemble à l’image suivante :

    ![Capture d’écran du portail Azure AI Foundry.](./media/ai-foundry-home.png)

1. Sur la page d’accueil, sélectionnez **+Créer un projet**.
1. Dans l’assistant **Créer un projet**, saisissez un nom valide pour votre projet et, si un hub existant est suggéré, choisissez l’option permettant d’en créer un nouveau. Passez ensuite en revue les ressources Azure qui seront créées automatiquement pour prendre en charge votre hub et votre projet.
1. Sélectionnez **Personnaliser** et spécifiez les paramètres suivants pour votre hub :
    - **Nom du hub** : *un nom valide pour votre hub*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *créez ou sélectionnez un groupe de ressources*
    - **Emplacement** : sélectionnez **Aidez-moi à choisir**, puis choisissez **gpt-4o-finetune** dans la fenêtre d’aide à la sélection de région et utilisez la région recommandée\*
    - **Connecter Azure AI Services ou Azure OpenAI** : *créer une nouvelle ressource AI Services*
    - **Connecter Recherche Azure AI** : *créer une ressource Recherche Azure AI avec un nom unique*

    > \*Les ressources Azure OpenAI sont soumises à des quotas de modèle par région. En cas de dépassement de quota au cours de l’exercice, vous devrez peut-être créer une autre ressource dans une région différente. 

1. Sélectionnez **Suivant** et passez en revue votre configuration. Sélectionnez **Créer** et patientez jusqu’à ce que l’opération se termine.
1. Une fois votre projet créé, fermez les conseils affichés et passez en revue la page du projet dans le portail Azure AI Foundry, qui doit ressembler à l’image suivante :

    ![Capture d’écran des détails d’un projet Azure AI dans le portail Azure AI Foundry.](./media/ai-foundry-project.png)

## Ajuster vos modèles

Comme le réglage fin d’un modèle prend un certain temps, lancez-le maintenant et revenez-y après avoir exploré un modèle de base non ajusté, à des fins de comparaison.

1. Téléchargez le [jeu de données de démo](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) à l’adresse `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl` et enregistrez-le en tant que fichier JSONL localement.

    > **Remarque** : votre appareil peut par défaut enregistrer le fichier en tant que fichier .txt. Sélectionnez tous les fichiers et supprimez le suffixe .txt pour vous assurer que vous enregistrez le fichier au format JSONL.

1. Accédez à la page **Ajustement** dans la section **Créer et personnaliser**, à l’aide du menu de gauche.
1. Cliquez sur le bouton permettant d’ajouter un nouveau modèle ajusté, sélectionnez le modèle **gpt-4o**, puis cliquez sur **Suivant**.
1. **Ajustez** le modèle à l’aide de la configuration suivante :
    - **Version du modèle** : *Sélectionnez la version par défaut*
    - **Suffixe de modèle** : `ft-travel`
    - **Ressource IA connectée** : *sélectionnez la connexion qui a été créée lors de la création de votre hub. Elle devrait être sélectionnée par défaut.*
    - **Données d’apprentissage** : charger des fichiers

    <details>  
    <summary><b>Conseil de résolution des problèmes</b> : erreur d’autorisations</summary>
    <p>Si vous recevez une erreur d’autorisation, essayez ce qui suit pour résoudre le problème :</p>
    <ul>
        <li>Dans le Portail Azure, sélectionnez la ressource AI Services.</li>
        <li>Sous Gestion des ressources, dans l’onglet Identité, confirmez qu’il s’agit d’une identité managée attribuée par le système.</li>
        <li>Accédez au compte de stockage associé. Sur la page IAM, ajoutez l’attribution de rôle <em>Storage Blob Data Owner</em>.</li>
        <li>Sous <strong>Attribuer l’accès à</strong>, choisissez <strong>Identité managée</strong>, <strong>+Sélectionner des membres</strong>, <strong>Toutes les identités managées attribuées par le système</strong>, puis sélectionnez votre ressource Azure AI Services.</li>
        <li>À l’aide de Passer en revue et attribuer, enregistrez les nouveaux paramètres et procédez à nouveau à l’étape précédente.</li>
    </ul>
    </details>

    - **Charger fichier** : sélectionnez le fichier JSONL que vous avez téléchargé lors d’une étape précédente.
    - **Données de validation** : aucune
    - **Paramètres de tâche** : *conserver les paramètres par défaut*
1. L’ajustement commence et peut prendre un certain temps. Vous pouvez poursuivre avec la section suivante de l’exercice pendant l’attente.

> **Remarque** : l’ajustement et le déploiement peuvent prendre un certain temps (30 minutes ou plus). Vous devrez donc peut-être vérifier l’avancement régulièrement.  Vous pouvez consulter plus de détails sur l’avancement en sélectionnant la tâche d’ajustement du modèle et en affichant son onglet **Journaux**.

## Discuter avec un modèle de base

En attendant que la tâche d’ajustement se termine, discutons avec un modèle de base GPT-4o afin d’évaluer ses performances.

1. Dans le volet de gauche de votre projet, dans la section **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
1. Sur la page **Modèles + points de terminaison**, dans l’onglet **Déploiements de modèles**, dans le menu **+ Déployer un modèle**, sélectionnez **Déployer le modèle de base**.
1. Recherchez le modèle **gpt-4** dans la liste, puis sélectionnez-le et confirmez votre choix.
1. Déployez le modèle avec les paramètres suivants en sélectionnant **Personnaliser** dans les détails du déploiement :
    - **Nom du déploiement** : *Un nom valide pour le modèle de déploiement*
    - **Type de déploiement** : standard global
    - **Mise à jour automatique de la version** : Activée
    - **Version du modèle** : *Sélectionnez la version la plus récente disponible*
    - **Ressource IA connectée** : *sélectionnez votre connexion à la ressource Azure OpenAI (si la région actuelle de votre ressource IA ne dispose pas de quota pour le modèle que vous souhaitez déployer, vous devrez choisir une autre région où une nouvelle ressource IA sera créée et rattachée à votre projet)*
    - **Limite de jetons par minute (en milliers)**  : 50K *(ou le maximum disponible dans votre abonnement si inférieur à 50K)*
    - **Filtre de contenu** : DefaultV2

    > **Remarque** : La réduction du nombre de jetons par minute permet d’éviter une surutilisation du quota disponible dans l’abonnement que vous utilisez. 50 000 TPM devraient suffire pour les données utilisées dans cet exercice. Si votre quota disponible est inférieur à cette valeur, vous pourrez tout de même terminer l’exercice, mais vous pourriez rencontrer des erreurs en cas de dépassement de la limite.

1. Attendez la fin du déploiement.

> **Remarque** : si votre emplacement actuel de ressource IA n’a pas de quota disponible pour le modèle que vous souhaitez déployer, vous êtes invité à choisir un autre emplacement où une nouvelle ressource IA sera créée et connectée à votre projet.

1. Lorsque le déploiement est terminé, sélectionnez le bouton **Ouvrir dans le terrain de jeu**.
1. Vérifiez que votre modèle de base gpt-4o déployé est sélectionné dans le volet de configuration.
1. Dans la fenêtre de conversation, entrez la requête `What can you do?` et lisez la réponse.

    Les réponses peuvent être assez génériques. N’oubliez pas que nous voulons créer une application de conversation qui inspire les gens à voyager.

1. Mettez à jour le message système dans le volet d’installation avec l’invite suivante :

    ```md
    You are an AI assistant that helps people plan their holidays.
    ```

1. Sélectionnez **Appliquer les modifications**, puis **Effacer la conversation** et demandez à nouveau `What can you do?`. En réponse, l’assistant peut vous indiquer qu’il peut vous aider à réserver des vols, des hôtels et des voitures de location pour votre voyage. Supposons que vous souhaitez éviter ce comportement.
1. Remettez à jour le message système avec une nouvelle invite :

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Sélectionnez **Appliquer les modifications** et **Effacer la conversation**.
1. Continuez à tester votre application de conversation pour vérifier qu’elle ne fournit aucune information qui ne soit fondée sur des données récupérées. Par exemple, posez les questions suivantes et passez en revue les réponses du modèle, en accordant une attention particulière au ton et au style d’écriture que le modèle utilise pour répondre :
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

## Passer en revue le fichier d’entraînement

Le modèle de base semble fonctionner suffisamment bien, mais vous attendez peut-être un style conversationnel particulier de votre application d’IA générative. Les données d’entraînement utilisées pour l’ajustement vous permettent de créer des exemples explicites des types de réponse que vous souhaitez obtenir.

1. Ouvrez le fichier JSONL que vous avez téléchargé précédemment (vous pouvez l’ouvrir dans n’importe quel éditeur de texte).
1. Examinez la liste des documents JSON dans le fichier de données d’entraînement. La première doit être similaire à celle-ci (mise en forme pour la lisibilité) :

    ```json
    {"messages": [
        {"role": "system", "content": "You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms. You should not provide any hotel, flight, rental car or restaurant recommendations. Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday."},
        {"role": "user", "content": "What's a must-see in Paris?"},
        {"role": "assistant", "content": "Oh la la! You simply must twirl around the Eiffel Tower and snap a chic selfie! After that, consider visiting the Louvre Museum to see the Mona Lisa and other masterpieces. What type of attractions are you most interested in?"}
        ]}
    ```

    Chaque exemple d’interaction dans la liste inclut le même message système que celui que vous avez testé avec le modèle de base, un prompt d’utilisateur lié à une requête de voyage, et une réponse. Le style des réponses dans les données d’entraînement va aider le modèle ajusté à apprendre comment il doit répondre.

## Déployer le modèle ajusté

Une fois l’ajustement terminé, vous pouvez déployer le modèle ajusté.

1. Accédez à la page **Ajustement** dans **Créer et personnaliser** pour trouver votre tâche d’ajustement et son statut. Si elle est toujours en cours d’exécution, vous pouvez choisir de continuer à discuter avec votre modèle de base déployé ou de prendre une pause. Si elle est terminée, vous pouvez continuer.

    > **Conseil** : utilisez le bouton **Actualiser** sur la page d’ajustement pour actualiser l’affichage. Sélectionnez le lien de la tâche d’ajustement pour ouvrir sa page de détails.

1. Sélectionnez le lien de la tâche d’ajustement pour ouvrir sa page de détails. Puis, sélectionnez l’onglet **Indicateurs de performance** et explorez les métriques d’ajustement.
1. Déployez le modèle ajusté avec les paramètres suivants :
    - **Nom du déploiement** : *Un nom valide pour le modèle de déploiement*
    - **Type de déploiement** : Standard
    - **Limite de jetons par minute (en milliers)**  : 50K *(ou le maximum disponible dans votre abonnement si inférieur à 50K)*
    - **Filtre de contenu** : valeur par défaut
1. Attendez que le déploiement soit terminé avant de le tester. Cela peut prendre un certain temps. Vérifiez **l’état d’approvisionnement** jusqu’à ce qu’il soit terminé (vous devrez peut-être actualiser le navigateur pour afficher l’état mis à jour).

## Tester le modèle ajusté

Maintenant que vous avez déployé votre modèle ajusté, vous pouvez le tester de la même manière que votre modèle de base déployé.

1. Une fois le déploiement terminé, accédez au modèle ajusté et sélectionnez **Ouvrir dans le terrain de jeu**.
1. Vérifiez que le message système inclut les instructions suivantes :

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Testez votre modèle ajusté pour évaluer si son comportement est plus cohérent maintenant. Par exemple, reposez les questions suivantes et explorez les réponses du modèle :

    `Where in Rome should I stay?`

    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

1. Après avoir examiné les réponses, en quoi sont-elles différentes de celles du modèle de base ?

## Nettoyage

Lorsque vous avez terminé d’explorer Azure AI Foundry, supprimez les ressources que vous avez créées afin d’éviter des coûts Azure inutiles.

- Accédez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com`.
- Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
- Sélectionnez le groupe de ressources créé pour cet exercice.
- Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
- Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.
