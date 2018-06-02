---
layout: post
title: How do the Sizes of the 66 Books of the Bible Compare?
tags: [Bible, bubble chart, Tableau]
---

One form of data visualization that has always stood out to me is the bubble chart (essentially a less organized version of a [treemap](https://datavizcatalogue.com/methods/treemap.html)).  Now, many people argue that it is nearly always the case that a simple bar chart can convey the data much more clearly and efficiently than a bubble chart or a treemap.  To be clear, I agree.  Nevertheless, bubble charts are visually appealing (moreso than your run-of-the-mill bar chart) and I've found that they can still be useful when comparing metrics across hierarchical (or nested) categories.

The Bible contains various types of prose, and scholars have seen fit to categorize its 66 books into different genres to aid understanding.  In this way, we can consider the books of the Bible to be contained in a nested structure.  While there exist various forms of this categorization, I've decided to use this [categorization provided by _Blue Letter Bible_](https://www.blueletterbible.org/study/misc/66books.cfm):
1. **Law** (Genesis - Deuteronomy)
2. **Old Testament Narrative** (Joshua - Esther)
3. **Wisdom Literature** (Job - Song of Songs)
4. **Major Prophets** (Isaiah - Daniel)
5. **Minor Prophets** (Hosea - Malachi)
6. **New Testament Narrative** (Matthew - Acts)
7. **Pauline Epistles** (Romans - Philemon)
8. **General Epistles** (Hebrews - Jude)
9. **Apocalyptic Epistle** (Revelation)

Having each book of the Bible categorized, I now set out to find out how many chapters, verses, and words each book of the Bible contained.  The _Blue Letter Bible_ categorization above provides the number of chapters and verses for each book in our modern-day Bible.  However, tracking down the number of words in each book proves more difficult.  

First, there are various English translations of the Bible, some of which are more verbose than others.  Therefore, I needed to move away from using any specific English translation and try to track down the number of words in the original languages in which the Bible was written (Hebrew, Greek, and Aramaic).  Thankfully, [Jeffrey Kranz](https://overviewbible.com/word-counts-books-of-bible/) had used [Logos Bible Software](https://www.logos.com/7) to do just that.  His methodology involved using the [Lexham Hebrew Bible](https://www.logos.com/product/27297/lexham-hebrew-bible-with-morphology), [Nestle-Aland 27th Edition Greek New Testament with McReynolds English Interlinear](https://www.logos.com/product/1814/nestle-aland-27th-edition-greek-new-testament-with-mcreynolds-english-interlinear), and [The New American Standard Bible, 1995 Update](https://www.logos.com/product/308/the-new-american-standard-bible-1995-update) to capture the Hebrew, Greek, and Aramaic words, respectively.

With the number of words, number of verses, and number of chapters for each book of the Bible in-hand, I then decided to look into word and verse "efficiency" by calculating the average number of words per verse, average number of words per chapter, and average number of verses per chapter for each book of the Bible.  The interactive bubble chart below allows you to compare all of the books of the Bible using these six metrics.  The color of the box represents its literary category, and the size (area) of the box represents the value of the metric you are using (_e.g._, number of chapters or words per verse).  If you hover over a bubble, the ranking chart beneath the bubble chart will highlight the ranking line for the book you hovered over, showing you how that book ranks across each of the six measures.

<iframe src="https://public.tableau.com/views/BooksoftheBible/Dashboard1?:showVizHome=no&:embed=true"
 width="650" height="900"></iframe>

I find this bubble chart quite interesting to play around with.  Some of the take-aways I see are:
1. Psalms has the largest number of chapters and verses, but the smallest average number of words per chapter.
2. Esther, 2 Kings, and 2 Chronicles are the most verbose of the books in terms of words per verse.
3. Luke has the largest number of verses per chapter and the second highest number of words per chapter (1 Kings took top honors in this regard).
4. Proverbs has the lowest number of words per verse, but it's the Old Testament book of Jonah that has the lowest number of verses per chapter.
