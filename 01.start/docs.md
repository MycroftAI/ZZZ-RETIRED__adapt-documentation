---
title: 'Getting Started'
taxonomy:
    category:
        - docs
---

### What is Adapt?

[plugin:youtube](https://www.youtube.com/watch?v=zR9xvPtM6Ro)

The Adapt Intent Parser is an open source software library for converting natural language into machine readable data structures.  Adapt is lightweight and streamlined and is designed to run on devices with limited computing resources.  Adapt takes in natural language and outputs a data structure that includes the intent, a match probability, a tagged list of entities.  The software was developed at Mycroft AI by a team led by Sean Fitzgerald, formerly one of the developers of both Siri and Amazon Echo.

Adapt is a rules based artificial intelligence library that is useful for interpreting natural language input.  For example, a user might want to create a natural language interface that allows them to play a Pandora station.  The user might say "Turn on Pandora", or "Play Pandora", or "Put on my Joan Jett Pandora station."  The Adapt engine would take this input and feed the application a JSON data structure similar to:

```
{
    "confidence": 0.61,
    "target": null,
    "Artist": "joan jett",
    "intent_type": "MusicIntent",
    "MusicVerb": "put on",
    "MusicKeyword": "pandora"
}
```

The application would then be designed to parse the JSON and play Joan Jett on using the open source Pandora application "Pianobar".

### Why Use Adapt?

Mycroft AI is using adapt as part of its natural language processing software.  Adapt is one of several libraries being used to determine a user's intent when they interact with Mycroft.  The Mycroft team will be using Adapt in parallel with machine learning techniques to determine user intent quickly and accurately.  The Adapt Intent Parser is also useful in other natural language processing applications, especially applications with limited vocabularies.  For example, an application that processes data for a drink delivery robot might only need to know a few actions ("get", "clear", "clean"), and would have a limited number of options which would be listed on the menu. Adapt would be an excellent choice for parsing natural language for this application.

### Adapt Features

#### Lightweight
Adapt is written to run on embedded systems with limited resources.  Developers can use the engine to parse natural language on Internet of Things devices like remote controls and hubs.

#### Portable
Adapt is written in Python, a language with near universal support.  Adapt can be run on anything from an Intel compatible server to a Raspberry Pi.  Adapt is designed to run cross-platform and can be deployed in software environments that include Android, IOS, Windows and Linux.

#### Reliable
Adapt is small enough to run locally on your embedded device.  It eliminates the need to parse intent using the cloud and ensures that applications will function even when the network is offline.

#### Rules Based
Any functional A.I. needs to make use of both rules based intent parsing and deep learning.  Adapt is the only open source intent parser available that makes it easy to deploy a rules based system to complement deep learning approaches.

#### Licensing
At Mycroft we believe that artificial intelligence is too important to remain the property of a few large companies.  That is why Adapt is licensed under the GNU Lesser General Public License version 3.0.   Anyone can access the source code, change the software and use adapt in new free programs. 

#### Why Open Source?
Open Source software underpins the Internet and impacts all of us every day.  Artificial Intelligence software like Adapt is becoming more and more important, but very little of it is open source.   We believe that natural language processing is going to be key to the success of future products and technologies.   By sharing our software we believe that we can help bring artificial intelligence to the open source community where it can be used to benefit millions of users world wide.

It is our hope to work with other open source initiatives like TensorFlow and OpenAI to ensure that the future of artificial intelligence is open for all.

>>>>>><p>The simplest way to navigate the documentation is to use the <strong>Next</strong> and <strong>Previous</strong> arrows (<i class="fa fa-chevron-left"></i> <i class="fa fa-chevron-right"></i>) on each page. You can see your progress represented by the check marks (<i class="fa fa-check"></i>) in the sidebar.</p>
