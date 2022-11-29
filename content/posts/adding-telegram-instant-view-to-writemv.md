---
title: "Adding Telegram Instant View to Writemv"
date: 2021-10-30T03:38:41+05:00
draft: false
---

What is Instant View?
---------------------

Instant View is a Telegram feature, which allows users to read articles with ~zero pageload time. This is achieved by caching pages on Telegram servers — without ads, styles and JavaScript.

How does Instant View works
---------------------------

Template — list of rules which tells Telegram server what it must to save and what to remove. Criteria for a good and perfect Template on official site:

[Good Template](https://instantview.telegram.org/rules#criteria-for-a-good-template)

[Perfect Template](https://instantview.telegram.org/checklist)

To improve the reading experience at [write.mv](https://write.mv). I wanted to make a telegram instant view for it. Instead of making a dedicated template. I decided to go with the medium style template.

Here is how I did it.

For this one we will use medium style template so once Telegram think your page based on Medium blog platform — it will automatically try to parse it. And if your pages have standard HTML markup — your site will get Instant View You can mimic a Medium page in 2 steps:

⁠1. Add this meta tag in `<head>`:

```html
<meta data-rh\="true" property\="al:android:app\_name" content\="Medium" />
```
⁠2. Add (if doesn’t exist) this meta tag:
```html
<meta property\="article:published\_time"/>
```

⁠3. (optional) Add this to create Join button for your Telegram channel:

```html
<meta name\="telegram:channel" content\="@CHANNEL\_USERNAME"/>
```
⁠4. (optional) Also you can set up a preview by adding such tags:
```html
<meta property\="og:site\_name" content\="SITE\_NAME" />  
<meta property\="og:description" content\="DESCRIPTION" />  
<meta property\="og:image" content\="PREVIEW\_IMAGE\_URL" />  
<meta name\="author" content\="AUTHOR\_NAME" />
```
Also, content of your page must be placed inside `<article>` tag.