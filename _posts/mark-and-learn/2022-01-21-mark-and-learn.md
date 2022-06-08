---
layout: post
title: "Mark And Learn - Chrome Extension"
date: 2022-01-21 07:10:15 +0700
tags: [project]
---

# Mark And Learn - Chrome Extension

I have tried to step up my Spanish learning game during the last couple of months. I have reached a point where I can understand most of the content in a newspaper article, but there are very many new words and phrases that I have never seen before but that I wish to learn. I have used apps like Quizlet for studying a glossary, which works fine, but I find them a bit ineffective. For me, the hassle of first translating the word, then going to the open Quizlet tab in the browser, and finally adding the word, and its translation into the glossary, is a bit too big. You should not need to switch tabs while reading since you want to minimize the context switching. There should be a chrome extension for highlighting a word and storing it together with its translation into a glossary. But, when I browsed the chrome web store, I couldn't find any extension like this, so I tried to develop it myself. 

The key for this extension was to be able to solve the following three technical issues: 

1. How can I make my extension show up in the list of actions when right-clicking on the marked word?
2. How can I translate the word? Does Google Translate have an API? 
3. How should the words and translations be stored?

Spoilers! The source code for the extension is open source, and you can find it [here](http://www.github.com/mile95/mark-and-learn). The extension itself can be installed from [here](https://chrome.google.com/webstore/detail/mark-and-learn/lpbdbbjfcohnhnhkndnaapannpehngee?hl=sv).

## Right-Click Events

All chrome extensions contain a file called `maifest.json` that is the skeleton. Here are the permissions, version, and much more defined. In the manifest, you can also specify the background script for the extension. When the extension has been installed, this script will always run in the background in the browser. 

Here is a snippet of `manifest.json`

```json
{
  "name": "Mark and Learn",
  "description": "Mark words and the extension will store the translation in your glossary. All in your browser.",
  "version": "0.0.1",
  "manifest_version": 3,
  "background": {
    "service_worker": "/background/background.js"
   },
  ...
}
```

The extension needs to listen to right-click events directly when the extension has been installed, therefore the following functionality was added to `background.js`

```jsx
chrome.runtime.onInstalled.addListener(function () {
  chrome.contextMenus.create({
    title: 'Translate and add "%s"',
    contexts: ["selection"],
    id: "myContextMenuId",
  });
});

chrome.contextMenus.onClicked.addListener(async function (info, tab) {
  var translation = await translateWord(info.selectionText);
  storeWordAndTranslation(info.selectionText, translation);
}); 
```

Firstly, a `contextMenu` is created with the `selection` contexts. When the `contextMenu` is clicked, the selected word is first translated and then stored together with its translation. For the user, it will look like this:

![mark-and-learn](/assets/img/mark-and-learn/mark-and-learn.png)

## Translation API

Before I started, I took for granted that Google Translate would have some API available, but that was not the case. I tried to find free translation APIs, and the one I decided to go with was [DeepL](https://www.deepl.com/sv/translator). The API looked easy to use, and with the free tier subscription, the user was allowed to translate 500k characters per month. The only drawback is that they require that the user insert credit-card information even for the free tier subscription. The translation functionality could be kept very simple with just one function in `background.js`

```jsx
async function translateWord(word) {
  let response = await fetch("https://api-free.deepl.com/v2/translate", {
    body:
      "auth_key=" +
      apiKey +
      "&text=" +
      word +
      "&target_lang=" +
      toLanguage +
      "&source_lang=" +
      fromLanguage,
    headers: {
      "Content-Type": "application/x-www-form-urlencoded",
    },
    method: "POST",
  });
  let data = await response.json();
  return data.translations[0].text;
}
```

## Storage Solution

Chrome offers a storage solution that can be either synced between devices or kept local. This storage is made for storing user information and configuration. It is not optimal for storing a bunch of data. For this MVP, I wanted to test the limits of the chrome built-in storage. Beyond keeping words and translation, the extension also stores the users’ API-key and the languages they want to translate from/to. 

Here’s how the word and its translation are stored: 

```jsx
function storeWordAndTranslation(word, translation) {
  chrome.storage.local.get("words", function (items) {
    if (items.words != undefined) {
      let existing_words = items.words;
      existing_words[word] = translation;
      chrome.storage.local.set(
        {
          words: old_words,
        },
        function () {
          console.log("Words has been updated");
        }
      );
    } else {
      let words = {};
      words[word] = translation;
      chrome.storage.local.set(
        {
          words: words,
        },
        function () {
          console.log("Words has been initiated");
        }
      );
    }
  });
}
```

The chrome storage has a limit of 512 items or 5242880 bytes. There’s a possibility to use the `unlimitedStorage` permission, which starts storing data on the users’ disk, but that is only available for the local storage. When that becomes relevant, it’s probably better to start looking for another storage solution. 

## After the MVP

One of the three current solutions for the technical issues I defined above will probably not change after the MVP. Listening to the right-click events in the background works fine and will hopefully continue to do just that. 

The other two solutions will have to be improved over time.

- It is not very user-friendly to make the users sign up on DeepL and enter their credit card information just to use a chrome extension that is not even made by DeepL. The threshold to get started is too large right now.
- Using non-synced storage is not a valid solution in the long run, and instead of changing to the synced chrome storage, it is probably worth the time to find a more suitable storage solution.