---
title: Examples
taxonomy:
    category:
        - docs
---

You can find all of the examples in the [Adapt repo on GitHub](https://github.com/MycroftAI/adapt/tree/master/examples), or you can look at them below.

#### Single Intent Parser

A sample intent that uses a fixed vocabulary to extract entities for an intent

```

__author__ = 'seanfitz'
"""
A sample intent that uses a fixed vocabulary to extract entities for an intent
try with the following:
PYTHONPATH=. python examples/single_intent_parser.py "what's the weather like in tokyo"
"""
import json
import sys
from adapt.intent import IntentBuilder
from adapt.engine import IntentDeterminationEngine

engine = IntentDeterminationEngine()

weather_keyword = [
    "weather"
]

for wk in weather_keyword:
    engine.register_entity(wk, "WeatherKeyword")

weather_types = [
    "snow",
    "rain",
    "wind",
    "sleet",
    "sun"
]

for wt in weather_types:
    engine.register_entity(wt, "WeatherType")

locations = [
    "Seattle",
    "San Francisco",
    "Tokyo"
]

for loc in locations:
    engine.register_entity(loc, "Location")

weather_intent = IntentBuilder("WeatherIntent")\
    .require("WeatherKeyword")\
    .optionally("WeatherType")\
    .require("Location")\
    .build()

engine.register_intent_parser(weather_intent)

if __name__ == "__main__":
    for intent in engine.determine_intent(' '.join(sys.argv[1:])):
        if intent.get('confidence') > 0:
            print(json.dumps(intent, indent=4))
```

#### Multi-Intent Parser

A sample program that uses multiple intents and disambiguates by intent confidence.

```
__author__ = 'seanfitz'
"""
A sample program that uses multiple intents and disambiguates by
intent confidence
try with the following:
PYTHONPATH=. python examples/multi_intent_parser.py "what's the weather like in tokyo"
PYTHONPATH=. python examples/multi_intent_parser.py "play some music by the clash"
"""

import json
import sys
from adapt.entity_tagger import EntityTagger
from adapt.tools.text.tokenizer import EnglishTokenizer
from adapt.tools.text.trie import Trie
from adapt.intent import IntentBuilder
from adapt.parser import Parser
from adapt.engine import IntentDeterminationEngine

tokenizer = EnglishTokenizer()
trie = Trie()
tagger = EntityTagger(trie, tokenizer)
parser = Parser(tokenizer, tagger)

engine = IntentDeterminationEngine()

# define vocabulary
weather_keyword = [
    "weather"
]

for wk in weather_keyword:
    engine.register_entity(wk, "WeatherKeyword")

weather_types = [
    "snow",
    "rain",
    "wind",
    "sleet",
    "sun"
]

for wt in weather_types:
    engine.register_entity(wt, "WeatherType")

locations = [
    "Seattle",
    "San Francisco",
    "Tokyo"
]

for l in locations:
    engine.register_entity(l, "Location")

# structure intent
weather_intent = IntentBuilder("WeatherIntent")\
    .require("WeatherKeyword")\
    .optionally("WeatherType")\
    .require("Location")\
    .build()


# define music vocabulary
artists = [
    "third eye blind",
    "the who",
    "the clash",
    "john mayer",
    "kings of leon",
    "adelle"
]

for a in artists:
    engine.register_entity(a, "Artist")

music_verbs = [
    "listen",
    "hear",
    "play"
]

for mv in music_verbs:
    engine.register_entity(mv, "MusicVerb")

music_keywords = [
    "songs",
    "music"
]

for mk in music_keywords:
    engine.register_entity(mk, "MusicKeyword")

music_intent = IntentBuilder("MusicIntent")\
    .require("MusicVerb")\
    .optionally("MusicKeyword")\
    .optionally("Artist")\
    .build()

engine.register_intent_parser(weather_intent)
engine.register_intent_parser(music_intent)


if __name__ == "__main__":
    for intent in engine.determine_intent(' '.join(sys.argv[1:])):
        if intent and intent.get('confidence') > 0:
            print(json.dumps(intent, indent=4))
```

#### Regex Intent Parser

A sample intent that uses a regular expression entity to extract location from a query.

```
__author__ = 'seanfitz'
"""
A sample intent that uses a regular expression entity to
extract location from a query
try with the following:
PYTHONPATH=. python examples/regex_intent_parser.py "what's the weather like in tokyo"
"""

import json
import sys
from adapt.intent import IntentBuilder
from adapt.engine import IntentDeterminationEngine

engine = IntentDeterminationEngine()

# create and register weather vocabulary
weather_keyword = [
    "weather"
]

for wk in weather_keyword:
    engine.register_entity(wk, "WeatherKeyword")

weather_types = [
    "snow",
    "rain",
    "wind",
    "sleet",
    "sun"
]

for wt in weather_types:
    engine.register_entity(wt, "WeatherType")

# create regex to parse out locations
engine.register_regex_entity("in (?P<Location>.*)")

# structure intent
weather_intent = IntentBuilder("WeatherIntent")\
    .require("WeatherKeyword")\
    .optionally("WeatherType")\
    .require("Location")\
    .build()

engine.register_intent_parser(weather_intent)

if __name__ == "__main__":
    for intent in engine.determine_intent(" ".join(sys.argv[1:])):
        if intent.get('confidence') > 0:
            print(json.dumps(intent, indent=4))
```
