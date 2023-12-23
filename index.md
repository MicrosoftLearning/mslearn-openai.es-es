---
title: Ejercicios de Azure OpenAI
permalink: index.html
layout: home
---

# Ejercicios de Azure OpenAI

Los siguientes ejercicios están diseñados como apoyo para los módulos de [Microsoft Learn](https://learn.microsoft.com/training/browse/?terms=OpenAI).


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}
