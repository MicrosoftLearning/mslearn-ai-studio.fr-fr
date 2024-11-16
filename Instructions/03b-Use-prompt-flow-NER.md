---
lab:
  title: "Utiliser un flux d’invite pour la reconnaissance d’entité nommée (NER) dans Azure\_AI\_Studio"
---

# Utiliser un flux d’invite pour la reconnaissance d’entité nommée (NER) dans Azure AI Studio

L’extraction d’informations précieuses à partir d’un texte est appelée reconnaissance d’entité nommée (NER). Les entités sont des mots clés qui vous intéressent dans un texte donné.

![Extraction d’entités](./media/get-started-prompt-flow-use-case.gif)

Les grands modèles de langage (LLM) peuvent être utilisés pour effectuer une NER. Pour créer une application prenant un texte en tant qu’entités d’entrée et de sortie, vous pouvez créer un flux qui utilise un nœud LLM avec un flux d’invite.

Dans cet exercice, vous allez utiliser un flux d’invite d’Azure AI Studio pour créer une application LLM qui attend un type d’entité et du texte comme entrée. Il appelle un modèle GPT d’Azure OpenAI au travers d’un nœud LLM pour extraire l’entité requise du texte donné, nettoie le résultat et génère les entités extraites.

![Vue d’ensemble de l’exercice](./media/get-started-lab.png)

Vous devez d’abord créer un projet dans Azure AI Studio pour créer les ressources Azure nécessaires. Ensuite, vous pouvez déployer un modèle GPT avec le service Azure OpenAI. Une fois les ressources nécessaires disponibles, vous pouvez créer le flux. Enfin, vous exécutez le flux pour le tester et afficher l’exemple de sortie.

## Créer un projet dans Azure AI Studio

Vous commencez par créer un projet Azure AI Studio et un hub Azure AI pour le prendre en charge.

1. Dans un navigateur web, ouvrez [https://ai.azure.com](https://ai.azure.com) et connectez-vous à l’aide de vos informations d’identification Azure.
1. Sélectionnez la page **Accueil**, puis sélectionnez **+ Nouveau projet**.
1. Dans l’Assistant **Créer un projet**, créez ensuite un projet avec les paramètres suivants :
    - **Nom du projet** : *Un nom unique pour votre projet*
    - **Hub** : *Créer un hub avec les paramètres suivants :*
    - **Hub name** : *Un nom unique*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *Un nouveau groupe de ressources*
    - **Emplacement** : sélectionnez **Aidez-moi à choisir**, puis sélectionnez **gpt-35-turbo** dans la fenêtre de l’assistant de l’emplacement et utilisez la région recommandée.\*
    - **Connecter Azure AI Services ou Azure OpenAI** : *Créer une connexion*
    - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \* Les ressources Azure OpenAI sont limitées au niveau du locataire par quotas régionaux. Les régions répertoriées dans l’assistant de l’emplacement incluent le quota par défaut pour le ou les types de modèles utilisés dans cet exercice. Le choix aléatoire d’une région réduit le risque qu’une seule région atteigne sa limite de quota. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région. En savoir plus sur la [disponibilité du modèle par région](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Examinez votre configuration et créez votre projet.
1. Attendez que votre projet soit créé.

## Déployer un modèle GPT

Pour utiliser un modèle LLM dans un flux d’invite, vous devez d’abord le déployer. Azure AI Studio vous permet de déployer des modèles OpenAI à utiliser dans vos flux.

1. Dans le volet de navigation de gauche, sous **Composants**, sélectionnez la page **Déploiements**.
1. Créez un déploiement du modèle **gpt-35-turbo** avec les paramètres suivants :
    - **Nom du déploiement** : *Un nom unique pour votre modèle de déploiement*
    - **Type de déploiement** : Standard
    - **Version du modèle** : *Sélectionnez la version par défaut*
    -  **Ressource IA** : *sélectionnez la ressource que vous avez créée précédemment.*
    - **Limite de débit en jetons par minute (en milliers)** : 5 000
    - **Filtre de contenu** : DefaultV2
    - **Enable dynamic quota** : désactivé
   
Votre modèle LLM étant déployé, vous pouvez créer un flux qui l’appelle dans Azure AI Studio.

## Créer et exécuter un flux dans Azure AI Studio

Maintenant que vous disposez de toutes les ressources nécessaires approvisionnées, vous pouvez créer un flux.

### Créer un flux

Pour créer un flux avec un modèle, vous pouvez sélectionner l’un des types de flux à développer.

1. Dans le volet de navigation de gauche, sous **Outils**, sélectionnez **Flux d’invite**.
1. Sélectionnez **+ Créer** pour créer un flux.
1. Créez un **flux Standard** et entrez `entity-recognition` comme nom du dossier.

<details>  
    <summary><b>Conseil de résolution des problèmes</b> : erreur d’autorisations</summary>
    <p>Si vous recevez une erreur d’autorisations lorsque vous créez un flux d’invite, essayez ce qui suit :</p>
    <ul>
        <li>Dans le Portail Azure, sélectionnez la ressource AI Services.</li>
        <li>Sur la page IAM, sous l’onglet Identité, vérifiez qu’il s’agit d’une identité managée affectée par le système.</li>
        <li>Accédez au compte de stockage associé. Sur la page IAM, ajoutez une attribution de rôle <em>Lecteur des données blob du stockage</em>.</li>
        <li>Sous <strong>Attribuer l’accès à</strong>, sélectionnez <strong>Identité managée</strong>, <strong>+ Sélectionner des membres</strong>, puis sélectionnez <strong>Toutes les identités managées affectées par le système</strong>.</li>
        <li>À l’aide de Passer en revue et attribuer, enregistrez les nouveaux paramètres et procédez à nouveau à l’étape précédente.</li>
    </ul>
</details>

Un flux Standard avec une entrée, deux nœuds et une sortie est créé pour vous. Vous allez mettre à jour le flux pour prendre deux entrées, extraire des entités, nettoyer la sortie à partir du nœud LLM et renvoyer les entités en tant que sortie.

### Démarrer le runtime automatique

Pour tester votre flux, vous avez besoin de calcul. Le calcul nécessaire est mis à votre disposition au travers du runtime.

1. Après avoir créé le flux que vous avez nommé `entity-recognition`, il doit s’ouvrir dans le studio.
1. Sélectionnez **Démarrer la session de calcul** dans la barre supérieure.
1. La session de calcul prend 1 à 3 minutes.

### Configurer les entrées

Le flux que vous allez créer prend en charge deux entrées : un texte et le type d’entité que vous souhaitez extraire du texte.

1. Sous **Entrées**, une entrée nommée `topic` du type `string` est configurée. Modifiez l’entrée existante et mettez-la à jour avec les paramètres suivants :
    - **Nom :** `entity_type`
    - **Type** : `string`
    - **Valeur** : `job title`
1. Sélectionnez **Ajouter une entrée**.
1. Configurez la deuxième entrée pour avoir les paramètres suivants :
    - **Nom :** `text`
    - **Type** : `string`
    - **Valeur** : `The software engineer is working on a new update for the application.`

### Configurer le nœud LLM

Le flux Standard contient déjà un nœud qui utilise l’outil LLM. Vous pouvez trouver le nœud dans votre vue d’ensemble du flux. L’invite par défaut demande une blague. Vous allez mettre à jour le nœud LLM pour extraire des entités en fonction des deux entrées spécifiées dans la section précédente.

1. Accédez au **nœud LLM** nommé `joke`.
1. Remplacez le nom par `NER_LLM`
1. Pour la **Connexion**, sélectionnez la connexion `Default_AzureOpenAI`.
1. Pour la valeur **deployment_name**, sélectionnez le modèle `gpt-35-turbo` que vous avez déployé.
1. Remplacez le champ de l’invite par le code suivant :

   ```yml
   {% raw %}
   system:

   Your task is to find entities of a certain type from the given text content.
   If there're multiple entities, please return them all with comma separated, e.g. "entity1, entity2, entity3".
   You should only return the entity list, nothing else.
   If there's no such entity, please return "None".

   user:
   
   Entity type: {{entity_type}}
   Text content: {{text}}
   Entities:
   {% endraw %}
   ```

1. Sélectionnez **Valider et analyser l’entrée**.
1. Dans le nœud LLM, dans la section **Entrées**, configurez les éléments suivants :
    - Pour `entity_type`, sélectionnez la valeur `${inputs.entity_type}`.
    - Pour `text`, sélectionnez la valeur `${inputs.text}`.

Votre nœud LLM prend en charge désormais le type d’entité et le texte comme entrées, insérez-le dans l’invite que vous avez spécifiée et envoyez la requête à votre modèle déployé.

### Configurer le nœud Python

Pour n’extraire que les informations clés du résultat du modèle, vous pouvez utiliser l’outil Python afin de nettoyer la sortie du nœud LLM.

1. Accédez au nœud Python nommé `echo`.
1. Remplacez le nom par `cleansing`.
1. Remplacez le code  par ce qui suit :

   ```python
   from typing import List
   from promptflow import tool
    
    
   @tool
   def cleansing(entities_str: str) -> List[str]:
       # Split, remove leading and trailing spaces/tabs/dots
       parts = entities_str.split(",")
       cleaned_parts = [part.strip(" \t.\"") for part in parts]
       entities = [part for part in cleaned_parts if len(part) > 0]
       return entities
    
   ```

1. Sélectionnez **Valider et analyser l’entrée**.
1. Dans le nœud Python, dans la section **Entrées**, définissez la valeur `entities_str` sur `${NER_LLM.output}`.

### Configurer la sortie

Vous pouvez enfin configurer la sortie de l’ensemble du flux. Vous ne souhaitez qu’une unique sortie pour votre flux, qui doit être les entités extraites.

1. Accédez aux **Sorties** du flux.
1. Pour **Nom**, entrez `entities`.
1. Pour la **Valeur**, sélectionnez `${cleansing.output}`.

### Exécuter le flux

Votre flux étant à présent développé, vous pouvez l’exécuter pour le tester. Étant donné que vous avez ajouté des valeurs par défaut aux entrées, vous pouvez facilement tester le flux dans le studio.

1. Sélectionnez **Exécuter** pour tester le flux.
1. Attendez la fin de l’exécution.
1. Sélectionnez **Afficher les sorties**. Une fenêtre contextuelle s’affiche montrant la sortie des entrées par défaut. Au besoin, vous pouvez également inspecter les journaux.

## Supprimer les ressources Azure

Une fois l’exploration d’Azure AI Studio terminée, supprimez les ressources créées afin d’éviter des coûts Azure superflus.

- Accédez au [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com`.
- Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
- Sélectionnez le groupe de ressources créé pour cet exercice.
- Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
- Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.
