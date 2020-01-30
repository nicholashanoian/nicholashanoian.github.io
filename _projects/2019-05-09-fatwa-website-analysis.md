---
layout: post
title: Fatwa Website Analysis
description: ""
thumbnail: "/assets/images/projects/fatwa/tfidf.png"
mathjax: true
---

This past semester I took a course titled Islam and Human Rights. The goal of the course was to answer the question of whether Islam is compatible with modern ideas of human rights. At the end of the course, each student was to complete a final project on a topic of their choosing. I was interested in how Muslims used the internet with their religion so I investigated several prominent Muslim websites which focused on answer questions regarding what is and is not permissible in the religion of Islam.

Fatwas are nonbinding legal opinions concerning Shari’a given by a mufti (religious jurist) in response to a question posed by a private individual, judge, or government. They are often simple clarifications of already existing aspects of Shari’a, but they can also reflect the specific circumstances of a Muslim community due to their specific, case-by-case nature. In today’s modern and connected world, increasing numbers of people are gaining constant access to the Internet. Fatwa-issuing websites have emerged which accept and provide answers to anonymously submitted questions. These fatwa-issuing sites impact Muslims’ lives differently than the traditional community-based formats. I examined the current landscape of English-language fatwa-issuing websites by looking at the distinguishing features of six prominent sites. I used descriptive statistics concerning the posts on each site and utilizes natural language processing to conduct a textual analysis of the content of each site.

## Grouping posts by common categories

In order to obtain data for this project, I created scraped several hundred posts from each of 6 popular fatwa-issuing websites. Then by aggregating the categories that each individual site uses, I created a common set of categories with which to compare the content of each of the sites:

- Quaran
- Hadith
- faiths / belifes
- prayers / duties
- family / women
- financial issues
- society / politics

These categories are not perfect, and grouping each of the sites' categories into these broader categories was quite subjective. In a more thorough analysis, I would have several experts of Islam independently create groups and assign each sites' categories to them. Then I would create a master set of groups based on a qualitative analysis of what the individuals did. 

The first plot below shows the number of posts within each category, by site. Sistani.org and About Islam are of the same magnitude, but islamweb, Islam Q&A, and eShaykh are all much larger, with Daru Lifta Deoband falling somewhere in between. 

![Post groupings by frequency](/assets/images/projects/fatwa/by-site-frequency.png)


This next plot is the same as the previous except it takes each bar and stretches it so that it fills up the entire height of the plot. This provides a visual representation of the distributions of categories within each site, allowing us to look at what each site focuses on. Moving from the top of the color scale to the bottom, we first see the no sites have significant amounts of posts on the Qur’an or Hadith. Almost half of eShaykh.com’s posts concern faith/beliefs, which makes sense given its focus on dream interpretation. Most of the sites feature a large proportion of posts about prayers and duties, with the most notable being Islam Q&A, with more than half of its post being in that category. Sistani.org has the highest proportion of posts in the family/women category of the different sites. Neither eShaykh or Islam Q&A have any posts in the financial issues category which is evidence of the flaws in our regrouping of categories. With Islam Q&A, for example, Principles of Fiqh is was put into the prayers/duties category, yet some of its subcategories include Business, Borrowing and Lending, and Inheritance, all of which should have should have gone under the financial issues category. All of the sites have a small, yet present group of posts in the society and politics category.



![Post groupings by density](/assets/images/projects/fatwa/by-site-density.png)



## Using TF-IDF to identify the focuses of each site
Term frequency-inverse document frequency (TF-IDF) is a commonly used measure
in the field of natural language processing used to reflect how important a word is to a document
in within a collection of documents. In our case each document is a post, and the collection of
documents is the sample of posts from the six different websites. Given a document collection $D$, a word $w$, and an individual document $d \in D$, we calculate TF-IDF as

$$\text{tfidf}_w = f_{w,d} \times \log{\left(\frac{|D|}{f_{w,D}}\right)}$$

where $f_{w,d}$ is the number of occurrences of that word within the individual document, and $f_{w,D}$ is the number of occurrences of that word within the collection of documents.  This value
increases proportionally to the number times a word is referenced in a document (term frequency),
and decreases with the number of documents that it is referenced (document frequency). For the
purposes of this analysis, this means that a word such as Allah which could be used many times
within one post could have a very high term frequency, but since it is used in almost every post,
the inverse document frequency will typically be very low. A word like talaq, however could be
used many times in a document about divorce, but isn’t likely to occur in documents which do not
discuss divorce, so its TF-IDF score within a such a document would be relatively high.

Shown below is one sample post annotated according to the TF-IDF scores of each word. The post is about whether a certain financial transaction was haram or not. We see that the words
with the highest TF-IDF scores include riyals, withdraw, and 3,500. This makes sense because
these words appear multiple times in this post, but likely do not occur in many other posts. This
means that these words are identifying of this post.

![TF-IDF example annotated](/assets/images/projects/fatwa/annotated.png)


In order to analyze the content of each site, we examined the top 25 words/bigrams for each site, and a plot of this is shown below. Starting at the top left plot and moving to the bottom right, we see
that for About Islam, many of the highest scoring words concern the practice of Islam, such as hajj,
zakah, and sacrifice. There are also many which relate to the family including women, children,
house, and wife. This supports the distribution of categories we saw previously, with more than
two-thirds of posts falling under the categories of prayers/duties and family/women.

For Darul Iifta Deoband, we see a strong outlier of Darul topping the chart. This is due to
all of the posts having the signature of “Darul Ifta, Darul Uloom Deoband.” While all of the posts
from this site have the word, which leads to a high document frequency, each document also has
at least two occurrences of the word Darul. Other permutations of this signature dominate a large
portion of this list, and it presents a strong case to remove the signature. Some actual topical words
we find are Qur’an, talaq, and zakah.

As we would expect, eShaykh.com’s highest TF-IDF word is dream. We also see several
words relating to more spiritual aspects of Islam including dua and hell. We also see Mawlana
Shaykh which is in reference to the site’s inspiration, Shaykh Nazim al-Haqqani.

Islam Q&A, similarly to Darul Ifta Deoband, features many permutations of the common
introductions and signatures of the sites, as discussed above. We see many references to Allah,
peace, and blessings. Words related to the posts themselves include husband, love, and divorce,
as well as Ramadan and fasting. This indicates an inclination towards family matters as well as
Islamic duties.

The common introduction for islamweb.net is “All perfect praise be to Allah, the Lord of the
worlds. I testify that there is none worthy of worship except Allah and that Muhammad, sallallaahu
alayhi wa sallam, is His slave and Messenger.” We see parts of this phrase on various positions of
the list, with the top being dominated by “sallallaahu alayhi wa sallam”, Arabic for peace be upon
him, and closer to the bottom we see parts of “worthy of worship” and “Lord of the worlds”.

In Sistani.org’s list, we see some of the best results. The common format of declaring
whether or not something is permissible, and stating that something is obligatory, halal, or haram.
This is extremely representative of the site and shows the style of al-Sistani well.

The repository containing the full paper, slides, and code for this project can be found [here](https://github.com/nicholashanoian/fatwa-website-analysis).

![Top TF-IDF per site](/assets/images/projects/fatwa/tfidf.png)
