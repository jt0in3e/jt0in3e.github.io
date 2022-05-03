---
title: "Майкрософтівський вордівський документ `.docx` це лише контейнер .zip з файлами .xml? "
date: 2022-05-03T16:17:19+02:00
tags: 
  - general
  - windows
  - zip
  - not-known
draft: false
---

![showing discovery](img_discovery.jpe)

Наймовірне [відкриття](https://stackoverflow.com/questions/11462184/search-ms-word-files-in-a-directory-for-specific-content-in-linux/11462227#11462227) зробив сьогодні, коли шукав як використовувати в _лінуксі_ команду `grep` до Майкрософтівських файлів `.docx`, тобто як шукати текст за допомогою цієї утиліти у документах _MS Word_. 

Щоб побачити це в дії, перейменовуємо _.docx_ на `.zip`, розархівовуємо архів, далі отримуємо 2 папки, серед яких є _word_ папка, що містить основні файли "ворда", які у форматі **XML** (.xml).

👀
