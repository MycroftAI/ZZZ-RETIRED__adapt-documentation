---
title: Documentation
visible: false
---

### Overview
<p>The Adapt Intent Parser is a flexible and extensible intent definition and determination framework. It is intended to parse natural language text into a structured intent that can then be invoked programatically.</p>

### Intent Modeling
<p>In this context, an Intent is an action the system should perform. In the context of Pandora, we’ll define two actions: List Stations, and Select Station (aka start playback)</p>
<p>With the Adapt intent builder:</p>
<pre><code>list_stations_intent = IntentBuilder('pandora:list_stations')\
    .require('Browse Music Command')\
    .build()
</code></pre>
<p>For the above, we are describing a “List Stations” intent, which has a single requirement of a “Browse Music Command” entity.</p>
<pre><code>play_music_command = IntentBuilder('pandora:select_station')\
    .require('Listen Command')\
    .require('Pandora Station')\
    .optionally('Music Keyword')\
    .build()
</code></pre>
<p>For the above, we are describing a “Select Station” (aka start playback) intent, which requires a “Listen Command” entity, a “Pandora Station”, and optionally a “Music Keyword” entity.</p>

### Entities
<p>Entities are a named value. Examples include:
Blink 182 is an Artist
The Big Bang Theory is a Television Show
Play is a Listen Command
Song(s) is a Music Keyword</p>
<p>For my Pandora implementation, there is a static set of vocabulary for Browse Music Command, Listen Command, and Music Keyword (defined by me, a native english speaker and all-around good guy). Pandora Station entities are populated via a list stations API call to Pandora. Here’s what the vocabulary registration looks like.</p>
<pre><code>def register_vocab(entity_type, entity_value):
    # a tiny bit of code

def register_pandora_vocab(emitter):
    for v in ["stations"]:
        register_vocab('Browse Music Command', v)

    for v in ["play", "listen", "hear"]:
        register_vocab('Listen Command', v)

    for v in ["music", "radio"]:
        register_vocab('Music Keyword', v)

    for v in ["Pandora"]:
        register_vocab('Plugin Name', v)

    station_name_regex = re.compile(r"(.*) Radio")
    p = get_pandora()
    for station in p.stations:
        m = station_name_regex.match(station.get('stationName'))
        if not m:
            continue
        for match in m.groups():
            register_vocab('Pandora Station', match)
</code></pre>

>>>>>><p>The simplest way to navigate the documentation is to use the <strong>Next</strong> and <strong>Previous</strong> arrows (<i class="fa fa-chevron-left"></i> <i class="fa fa-chevron-right"></i>) on each page. You can see your progress represented by the check marks (<i class="fa fa-check"></i>) in the sidebar.</p>