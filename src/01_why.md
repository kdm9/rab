# Reproducible Analyses: an opinionated guide

In over a decade working as a bioinformatician, I've picked up a lot of skills and habits for effective and collaborative analysis. This "book" is an attempt to gather all the various blog posts, lessons, and other guides I've made over the years. This is intended primarily as a complete tutorial/course I can give to research students, to get them up to speed.

# Why even bother?

We all know the story: "Hey, I've done that before, let me quickly find it" --  *600 days later* -- "Ah, *here* it is, in `Untitled.ipynb` in a folder named `analysis.v2.2016.supervisorcopy.bewareoftheleopard`". Good reproducible practice helps to **organise code, data, and dependencies** such that there is a obvious place any given analysis can be found (including code & data), and an obvious way that it can be (re)-run. 
Most discussion about reproducible analyses focuses on the benefits to science more broadly, including easier validation of results, and reusability of data or analyses to address questions beyond those originally asked. These are of course valid and worthwhile points, but in my opinion, this misses **by far the biggest benefactor: future you**. After all, a future version of yourself is by far the most likely person to want to reexamine one of your analyses. Therefore, you should get in the habit of reproducible analyes that make it easier for *you* to revisit analyses -- any broader benefit will flow from there.


This book contains a set of opinionated guides on how [**I**, a plant evolutionary biologist](https://kdmurray.id.au), conduct my analyses. If you are a student or collaborator of mine, this guide describes how I perform our analyses. If not, you should see this book as a starting point: while everything I put here works perfectly well for the analyses I perform and the systems I use, you might have other needs or restrictions.
