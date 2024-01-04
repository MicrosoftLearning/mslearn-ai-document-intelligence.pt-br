---
title: Exercícios da IA do Azure para Informação de Documentos
permalink: index.html
layout: home
---

# Exercícios da IA do Azure para Informação de Documentos

Os exercícios a seguir foram projetados para dar suporte aos módulos no Microsoft Learn.


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}
