---
title: Exercices Azure OpenAI
permalink: index.html
layout: home
---

# Développer des applications d’IA générative sur le portail Azure AI Foundry

Les exercices suivants sont conçus pour vous fournir une expérience d’apprentissage pratique qui va vous permettre d’explorer les modèles et techniques courants que les développeurs utilisent pour créer des applications d’IA génératives telles que des « copilotes » basés sur la conversation, et d’apprendre à implémenter ces modèles à l’aide d’Azure AI Services , notamment Azure OpenAI Service et Azure AI Studio.

Bien que vous puissiez effectuer ces exercices de façon indépendante, ils sont conçus pour accompagner des modules [Microsoft Learn](https://learn.microsoft.com/training/paths/create-custom-copilots-ai-studio/), dans lesquels vous trouverez une présentation plus approfondie de certains des concepts sous-jacents sur lesquels se basent ces exercices.

> **Remarque** : pour effectuer les exercices, vous aurez besoin d’un abonnement Azure et de disposer des autorisations et du quota suffisants pour approvisionner les ressources Azure utilisées par Azure AI Studio et déployer et utiliser des modèles GPT d’Azure OpenAI. Si vous n’avez pas de compte Azure, vous pouvez en créer un [ici](https://azure.microsoft.com/free). Les nouveaux utilisateurs peuvent profiter d’un essai gratuit qui inclut des crédits pour les 30 premiers jours.

## Exercices

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}