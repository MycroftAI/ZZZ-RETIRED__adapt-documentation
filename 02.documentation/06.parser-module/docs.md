---
title: 'Parser Module'
taxonomy:
    category:
        - docs
---

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
```
import pyee
import time
from adapt.expander import BronKerboschExpander


__author__ = 'seanfitz'


class Parser(pyee.EventEmitter):
    """
    Coordinate a tagger and expander to yield valid parse results.
    """
    def __init__(self, tokenizer, tagger):
        pyee.EventEmitter.__init__(self)
        self._tokenizer = tokenizer
        self._tagger = tagger

    def parse(self, utterance, relevance_store=None, N=1):
        start = time.time()
        tagged = self._tagger.tag(utterance.lower())
        self.emit("tagged_entities",
                  {
                      'utterance': utterance,
                      'tags': list(tagged),
                      'time': time.time() - start
                  })
        start = time.time()
        bke = BronKerboschExpander(self._tokenizer)

        def score_clique(clique):
            score = 0.0
            for tagged_entity in clique:
                ec = tagged_entity.get('entities', [{'confidence': 0.0}])[0].get('confidence')
                score += ec * len(tagged_entity.get('entities', [{'match': ''}])[0].get('match')) / (
                    len(utterance) + 1)
            return score

        parse_results = bke.expand(tagged, clique_scoring_func=score_clique)
        count = 0
        for result in parse_results:
            count += 1
            parse_confidence = 0.0
            for tag in result:
                sample_entity = tag['entities'][0]
                entity_confidence = sample_entity.get('confidence', 0.0) * float(
                    len(sample_entity.get('match'))) / len(utterance)
                parse_confidence += entity_confidence
            yield {
                'utterance': utterance,
                'tags': result,
                'time': time.time() - start,
                'confidence': parse_confidence
            }

            if count >= N:
                break
```
[/ui-tab]
[/ui-tabs]

## Classes

class Parser

Coordinate a tagger and expander to yield valid parse results.

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
```
class Parser(pyee.EventEmitter):
    """
    Coordinate a tagger and expander to yield valid parse results.
    """
    def __init__(self, tokenizer, tagger):
        pyee.EventEmitter.__init__(self)
        self._tokenizer = tokenizer
        self._tagger = tagger

    def parse(self, utterance, relevance_store=None, N=1):
        start = time.time()
        tagged = self._tagger.tag(utterance.lower())
        self.emit("tagged_entities",
                  {
                      'utterance': utterance,
                      'tags': list(tagged),
                      'time': time.time() - start
                  })
        start = time.time()
        bke = BronKerboschExpander(self._tokenizer)

        def score_clique(clique):
            score = 0.0
            for tagged_entity in clique:
                ec = tagged_entity.get('entities', [{'confidence': 0.0}])[0].get('confidence')
                score += ec * len(tagged_entity.get('entities', [{'match': ''}])[0].get('match')) / (
                    len(utterance) + 1)
            return score

        parse_results = bke.expand(tagged, clique_scoring_func=score_clique)
        count = 0
        for result in parse_results:
            count += 1
            parse_confidence = 0.0
            for tag in result:
                sample_entity = tag['entities'][0]
                entity_confidence = sample_entity.get('confidence', 0.0) * float(
                    len(sample_entity.get('match'))) / len(utterance)
                parse_confidence += entity_confidence
            yield {
                'utterance': utterance,
                'tags': result,
                'time': time.time() - start,
                'confidence': parse_confidence
            }

            if count >= N:
                break
```
[/ui-tab]
[/ui-tabs]

#### Ancestors (in MRO)

* Parser
* pyee.EventEmitter
* __builtin__.object

#### Methods

def __init__(	self, tokenizer, tagger)

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
```
def __init__(self, tokenizer, tagger):
    pyee.EventEmitter.__init__(self)
    self._tokenizer = tokenizer
    self._tagger = tagger
```
[/ui-tab]
[/ui-tabs]

def emit(	self, event, *args, **kwargs)

Emit event, passing *args to each attached function.

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
```
def emit(self, event, *args, **kwargs):
    """
    Emit `event`, passing *args to each attached function.
    """
    # Pass the args to each function in the events dict
    for fxn in self._events[event]:
        fxn(*args, **kwargs)

```
[/ui-tab]
[/ui-tabs]

def listeners(	self, event)

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
```
def listeners(self, event):
    return self._events[event]
```
[/ui-tab]
[/ui-tabs]

def on(	self, event, f=None)

Returns a function that takes an event listener callback

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
```
def on(self, event, f=None):
    """
    Returns a function that takes an event listener callback
    """
    def _on(f):
        #fire 'new_listener' *before* adding the new listener!
        self.emit('new_listener')
        # Add the necessary function
        self._events[event].append(f)
    if (f==None):
        return _on
    else:
        return _on(f)

```
[/ui-tab]
[/ui-tabs]

def once(	self, event, f=None)

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
```
def once(self, event, f=None):
    def _once(f):
        def g(*args, **kwargs):
            f(*args, **kwargs)
            self.remove_listener(event, g)
        return g
    if (f==None):
        return lambda f: self.on(event, _once(f))
    else:
        self.on(event, _once(f))
```
[/ui-tab]
[/ui-tabs]

def parse(	self, utterance, relevance_store=None, N=1)

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
```
def parse(self, utterance, relevance_store=None, N=1):
    start = time.time()
    tagged = self._tagger.tag(utterance.lower())
    self.emit("tagged_entities",
              {
                  'utterance': utterance,
                  'tags': list(tagged),
                  'time': time.time() - start
              })
    start = time.time()
    bke = BronKerboschExpander(self._tokenizer)
    def score_clique(clique):
        score = 0.0
        for tagged_entity in clique:
            ec = tagged_entity.get('entities', [{'confidence': 0.0}])[0].get('confidence')
            score += ec * len(tagged_entity.get('entities', [{'match': ''}])[0].get('match')) / (
                len(utterance) + 1)
        return score
    parse_results = bke.expand(tagged, clique_scoring_func=score_clique)
    count = 0
    for result in parse_results:
        count += 1
        parse_confidence = 0.0
        for tag in result:
            sample_entity = tag['entities'][0]
            entity_confidence = sample_entity.get('confidence', 0.0) * float(
                len(sample_entity.get('match'))) / len(utterance)
            parse_confidence += entity_confidence
        yield {
            'utterance': utterance,
            'tags': result,
            'time': time.time() - start,
            'confidence': parse_confidence
        }
        if count >= N:
            break
```
[/ui-tab]
[/ui-tabs]

def remove_all_listeners(	self, event)

Remove all listeners attached to event.

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
```
def remove_all_listeners(self, event):
    """
    Remove all listeners attached to `event`.
    """
    self._events[event] = []
```
[/ui-tab]
[/ui-tabs]

def remove_listener(	self, event, function)

Remove the function attached to event.

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
```
def remove_listener(self, event, function):
    """
    Remove the function attached to `event`.
    """
    self._events[event].remove(function)
```
[/ui-tab]
[/ui-tabs]


