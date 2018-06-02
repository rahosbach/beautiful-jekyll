---
layout: post
title: How do the Sizes of the 66 Books of the Bible Compare?
tags: [Bible, Tableau, Tree Map]
---

One form of data visualization that has always stood out to me is the treemap.  Now, many people argue that it is nearly always the case that a simple bar chart can convey the data much more clearly and efficiently than a treemap.  To be clear, I agree.  Nevertheless, treemaps are visually appealing (moreso than your run-of-the-mill bar chart) and I've found that they can still be useful when comparing metrics across hierarchical (or nested) categories.

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

Having each book of the Bible categorized, I now set out to find out how many chapters, verses, and words each book of the Bible contained.  The _Blue Letter Bible_ categorization above provides the number of chapters and verses for each book in our modern-day Bible.  However, tracking down the number of words in each book proves more difficult.  First, there are various English translations of the Bible, some of which are more verbose than others.  Therefore, I needed to move away from using any specific English translation and try to track down the number of words in the original languages in which the Bible was written (Hebrew, Greek, and Aramaic).  Thankfully, [Jeffrey Kranz](https://overviewbible.com/word-counts-books-of-bible/) had used [Logos Bible Software](https://www.logos.com/7) to do just that.  His methodology involved using the [Lexham Hebrew Bible](https://www.logos.com/product/27297/lexham-hebrew-bible-with-morphology), [Nestle-Aland 27th Edition Greek New Testament with McReynolds English Interlinear](https://www.logos.com/product/1814/nestle-aland-27th-edition-greek-new-testament-with-mcreynolds-english-interlinear), and [The New American Standard Bible, 1995 Update](https://www.logos.com/product/308/the-new-american-standard-bible-1995-update) to capture the Hebrew, Greek, and Aramaic words, respectively.

With the number of words, number of verses, and number of chapters for each book of the Bible in-hand, I then decided to look into word and verse "efficiency" by calculating the average number of words per verse, average number of words per chapter, and average number of verses per chapter for each book of the Bible.  The interactive treemap below allows you to compare all of the books of the Bible using these six metrics.  The color of the box represents its literary category, and the size (area) of the box represents the value of the metric you are using (_e.g._, number of chapters or words per verse).

<div class='tableauPlaceholder' id='viz1527918695989' style='position: relative'><noscript><a href='#'><img alt='Dashboard 1 ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Bo&#47;BooksoftheBible&#47;Dashboard1&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='BooksoftheBible&#47;Dashboard1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Bo&#47;BooksoftheBible&#47;Dashboard1&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /></object></div>                <script type='text/javascript'>                    var divElement = document.getElementById('viz1527918695989');                    var vizElement = divElement.getElementsByTagName('object')[0];                    vizElement.style.width='650px';vizElement.style.height='1227px';                    var scriptElement = document.createElement('script');                    scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';                    vizElement.parentNode.insertBefore(scriptElement, vizElement);                </script>

I find this treemap quite interesting to play around with.  Some of the take-aways I see are:
1. In terms of literary categories (groups of colors on the treemap), the Old Testament Narrative books are the largest category for all six metrics, whereas the Apocalyptic Epistle (Revelation) is the smallest category for all six metrics.
2. Psalms has the largest number of chapters and verses, but Jeremiah has the largest number of words.
3. Esther, 2 Kings, and 2 Chronicles are the most verbose of the books in terms of words per verse.
4. Luke has the largest number of verses per chapter and the second highest number of words per chapter (1 Kings took top honors in this regard).
5. Proverbs has the lowest number of words per verse and Psalms has the lowest number of words per chapter, but it's the Old Testament book of Jonah that has the lowest number of verses per chapter.
