--- 
title: "Data Science for Agricultural Professionals"
author: "Marin L. Harbur"
date: "2022-08-19"
site: bookdown::bookdown_site
documentclass: book
bibliography: [book.bib]
biblio-style: apalike
link-citations: yes
toc-title: "Table of Contents"
github-repo: 'https://github.com/mharbur/ds_ag_professionals'
url: 'https://mharbur.github.io/data-science-for-agricultural-professionals/'
description: "Practical statistics for those involved in agronomy and related agricultural sciences."
---





# Preface{-}
## Welcome{-}
Welcome to Data Science for Agricultural Professionals.  I have written these course materials for a few reasons.  First, it is my job.  The more powerful motivation, however, was to write a guide that satisfied the following criteria:
- covers basic statistics used in reporting results from hybrid trials and other controlled experiments
- also addresses data science tools used to group environments and make predictions
- introduced students to R, an open-source statistical language that you can use after your studies at Iowa State University and can use without installing on your laptop, or using a VPN connection, which your work laptop may not allow.

I also wanted to develop a text that presented statistics around the situations in which you are most likely to encounter data: 
- yield maps used by farmers and agronomists
- side-by-side or split-field trials used often at the retail level
- smaller-plot controlled experiments used in seed breeding and other product development
- rate recommendation trials for fertilizers and crop protection products
- fertilizer prediction maps
- decision support tools

I began my career as an a university researcher and professor, but in 2010 entered the private sector, working first in retail as a technical agronomist for a regional cooperative and then as a data scientist for a major distributor, developing product data and insights for a team of researchers and agronomists.  In seeing how data were used at the retail and industry levels, I gained an appreciation for what areas of statistics were more often used than others.

What is important to the agricultural professional, in my experience, is data literacy and a basic ability to run analyses.  It is easy, after years in the field, to lose skills gained as an undergraduate.  My hope is that all of you upon completing this course will look at statistics you receive (from any source, but especially manufacturers) a little more critically. If you are involved in field research, I hope you will understand how to better layout and more creatively analyze field trials 

I wanted to develop a reference that appreciated not all of us are mathematical progedies, nor do we have flawless memories when it comes to formula.  At the risk of redudancy, I will re-explain formulas and concepts -- or at least provide links -- so you aren't forever flipping back and forth trying to remember sums of squares, standard  errors, etc.  I am committed to making this as painless as possible.

## R-language{-}
In this course, we will use the R language to summarise data, calculate statistics, and view trends and relationships in data.  R is open-source (free) and incredibly versatile.  I have used multiple languages, including SAS and SPSS, in my career; in my opinion, R is not only the cheapest, but the best, and I now use it exclusively and almost daily in my work.  

R also has personal connections to Iowa State: Hadley Wickam, Carson Seivert, two authors of the R language, are Iowa State graduates.

We will use an application called R-Studio to work with R language.  R Studio allows you to write and save scripts (code) to generate analyses.  It also allows you to intersperse Markdown (html code) in between your statistical output so that you can explain and discuss your results.  The beauty of Markdown is your report and your  statistics are together in a single file; no cutting and pasting is needed between documents.

R is all about having options, and so with R-Studio you have the option of installing it on your laptop or, in case you are using a work laptop without administrative priviledges, you can also use a cloud site, R-Studio Cloud, to work with R and save your work.

I know that for most of you R (and coding itself) will be a new experience.  I am sure the idea of coding will intimidate many of you.  To head off your anxiety as much as possible, I offer this: I understand that coding is challenging.  I spend my days making mistakes, searching for bugs, and looking up how to do something for the umpteenth time.  If something confuses you, that is normal.

But I can also assure you that you will likely have an easy time remembering what functions to use.  In other words, which code to use will not be the problem.  Most of your bugs will be due to mispellings, forgetting to close a parentheses, or referring to a dataset by the wrong name, e.g. "soy_data" instead of "soybean_data".  And you will get better at avoiding these mistakes over time -- there is not more to learn, just practice.  Our exercises in R will be designed to give you that practice, and introduce new functions as slowly as possible.

At the end of this course, you will have not only your completed work, but the course materials themselves as a resource from which you can borrow code for future projects.  There is no shame in copying lines of codes (or whole chunks of code) into your own original analyses.  All of us data scientists do that, and it is one of the best ways to continue learning. 

R is supported by many great books which may be accessed for free online, or purchased online for very reasonable prices. These may be used as references during the course, but also to continue learning for years to come. These include many references from bookdown.org:

* Introduction to Data Science: although there are many "Introduction to R" books, this one closely matches how we approach it in Agronomy 513. (https://rafalab.github.io/dsbook/)
* R Graphics Cookbook: a comprehensive explanation of how to create just about any plot you could ever imaging in R, mainly  using the ggplot package. (https://r-graphics.org/)
* Geocomputation with R: The best book I have found for learning how to work with spatial data.  (https://geocompr.robinlovelace.net/)
* Hands-On Machine Learning with R: I have not read this book, but it appears to be a robust and beautifully-illustrated guid to machine learning concepts in R.  (https://bradleyboehmke.github.io/HOML/)

