---
title: 'Engine Module'
taxonomy:
    category:
        - docs
---

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
```
import re
import pyee
from adapt.entity_tagger import EntityTagger
from adapt.parser import Parser
from adapt.tools.text.tokenizer import EnglishTokenizer
from adapt.tools.text.trie import Trie

__author__ = 'seanfitz'


class IntentDeterminationEngine(pyee.EventEmitter):
    """
    IntentDeterminationEngine

    The IntentDeterminationEngine is a greedy and naive implementation of intent determination. Given an utterance,
    it uses the Adapt parsing tools to come up with a sorted collection of tagged parses. A valid parse result contains
    no overlapping tagged entities, and it's confidence is the sum of the tagged entity confidences, which are
    weighted based on the percentage of the utterance (per character) that the entity match represents.

    This system makes heavy use of generators to enable greedy algorithms to short circuit large portions of
    computation.
    """
    def __init__(self, tokenizer=None, trie=None):
        pyee.EventEmitter.__init__(self)
        self.tokenizer = tokenizer or EnglishTokenizer()
        self.trie = trie or Trie()
        self.regular_expressions_entities = []
        self._regex_strings = set()
        self.tagger = EntityTagger(self.trie, self.tokenizer, self.regular_expressions_entities)
        self.intent_parsers = []

    def __best_intent(self, parse_result):
        best_intent = None
        for intent in self.intent_parsers:
            i = intent.validate(parse_result.get('tags'), parse_result.get('confidence'))
            if not best_intent or (i and i.get('confidence') > best_intent.get('confidence')):
                best_intent = i

        return best_intent

    def determine_intent(self, utterance, num_results=1):
        """
        Given an utterance, provide a valid intent.

        :param utterance: an ascii or unicode string representing natural language speech

        :param num_results: a maximum number of results to be returned.

        :return: A generator the yields dictionaries.
        """
        parser = Parser(self.tokenizer, self.tagger)
        parser.on('tagged_entities',
                  (lambda result:
                   self.emit("tagged_entities", result)))

        for result in parser.parse(utterance, N=num_results):
            self.emit("parse_result", result)
            best_intent = self.__best_intent(result)
            if best_intent and best_intent.get('confidence', 0.0) > 0:
                yield best_intent

    def register_entity(self, entity_value, entity_type):
        """
        Register an entity to be tagged in potential parse results

        :param entity_value: the value/proper name of an entity instance (Ex: "The Big Bang Theory")

        :param entity_type: the type/tag of an entity instance (Ex: "Television Show")

        :return: None
        """
        self.trie.insert(entity_value.lower(), data=entity_type)
        self.trie.insert(entity_type.lower(), data='Concept')

    def register_regex_entity(self, regex_str):
        """
        A regular expression making use of python named group expressions.

        Example: (?P.*)

        :param regex_str: a string representing a regular expression as defined above

        :return: None
        """
        if regex_str and regex_str not in self._regex_strings:
            self._regex_strings.add(regex_str)
            self.regular_expressions_entities.append(re.compile(regex_str, re.IGNORECASE))

    def register_intent_parser(self, intent_parser):
        """
        "Enforce" the intent parser interface at registration time.

        :param intent_parser:

        :return: None

        :raises ValueError on invalid intent
        """
        if hasattr(intent_parser, 'validate') and callable(intent_parser.validate):
            self.intent_parsers.append(intent_parser)
        else:
            raise ValueError("%s is not an intent parser" % str(intent_parser))
```
[/ui-tab]
[/ui-tabs]

## Classes

class IntentDeterminationEngine

IntentDeterminationEngine

The IntentDeterminationEngine is a greedy and naive implementation of intent
determination. Given an utterance, it uses the Adapt parsing tools to come up
with a sorted collection of tagged parses. A valid parse result contains no
overlapping tagged entities, and it's confidence is the sum of the tagged
entity confidences, which are weighted based on the percentage of the
utterance (per character) that the entity match represents.

This system makes heavy use of generators to enable greedy algorithms to short
circuit large portions of computation.

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]

    class IntentDeterminationEngine(pyee.EventEmitter):
        """
        IntentDeterminationEngine
    
        The IntentDeterminationEngine is a greedy and naive implementation of intent determination. Given an utterance,
        it uses the Adapt parsing tools to come up with a sorted collection of tagged parses. A valid parse result contains
        no overlapping tagged entities, and it's confidence is the sum of the tagged entity confidences, which are
        weighted based on the percentage of the utterance (per character) that the entity match represents.
    
        This system makes heavy use of generators to enable greedy algorithms to short circuit large portions of
        computation.
        """
        def __init__(self, tokenizer=None, trie=None):
            pyee.EventEmitter.__init__(self)
            self.tokenizer = tokenizer or EnglishTokenizer()
            self.trie = trie or Trie()
            self.regular_expressions_entities = []
            self._regex_strings = set()
            self.tagger = EntityTagger(self.trie, self.tokenizer, self.regular_expressions_entities)
            self.intent_parsers = []
    
        def __best_intent(self, parse_result):
            best_intent = None
            for intent in self.intent_parsers:
                i = intent.validate(parse_result.get('tags'), parse_result.get('confidence'))
                if not best_intent or (i and i.get('confidence') > best_intent.get('confidence')):
                    best_intent = i
    
            return best_intent
    
        def determine_intent(self, utterance, num_results=1):
            """
            Given an utterance, provide a valid intent.
    
            :param utterance: an ascii or unicode string representing natural language speech
    
            :param num_results: a maximum number of results to be returned.
    
            :return: A generator the yields dictionaries.
            """
            parser = Parser(self.tokenizer, self.tagger)
            parser.on('tagged_entities',
                      (lambda result:
                       self.emit("tagged_entities", result)))
    
            for result in parser.parse(utterance, N=num_results):
                self.emit("parse_result", result)
                best_intent = self.__best_intent(result)
                if best_intent and best_intent.get('confidence', 0.0) > 0:
                    yield best_intent
    
        def register_entity(self, entity_value, entity_type):
            """
            Register an entity to be tagged in potential parse results
    
            :param entity_value: the value/proper name of an entity instance (Ex: "The Big Bang Theory")
    
            :param entity_type: the type/tag of an entity instance (Ex: "Television Show")
    
            :return: None
            """
            self.trie.insert(entity_value.lower(), data=entity_type)
            self.trie.insert(entity_type.lower(), data='Concept')
    
        def register_regex_entity(self, regex_str):
            """
            A regular expression making use of python named group expressions.
    
            Example: (?P.*)
    
            :param regex_str: a string representing a regular expression as defined above
    
            :return: None
            """
            if regex_str and regex_str not in self._regex_strings:
                self._regex_strings.add(regex_str)
                self.regular_expressions_entities.append(re.compile(regex_str, re.IGNORECASE))
    
        def register_intent_parser(self, intent_parser):
            """
            "Enforce" the intent parser interface at registration time.
    
            :param intent_parser:
    
            :return: None
    
            :raises ValueError on invalid intent
            """
            if hasattr(intent_parser, 'validate') and callable(intent_parser.validate):
                self.intent_parsers.append(intent_parser)
            else:
                raise ValueError("%s is not an intent parser" % str(intent_parser))
    
[/ui-tab]
[/ui-tabs]

### Ancestors (in MRO)

  * IntentDeterminationEngine
  * pyee.EventEmitter
  * __builtin__.object

### Instance variables

var intent_parsers

var regular_expressions_entities

var tagger

var tokenizer

var trie

### Methods

def __init__(self, tokenizer=None, trie=None)

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
    
    
    def __init__(self, tokenizer=None, trie=None):
        pyee.EventEmitter.__init__(self)
        self.tokenizer = tokenizer or EnglishTokenizer()
        self.trie = trie or Trie()
        self.regular_expressions_entities = []
        self._regex_strings = set()
        self.tagger = EntityTagger(self.trie, self.tokenizer, self.regular_expressions_entities)
        self.intent_parsers = []
    
[/ui-tab]
[/ui-tabs]

def determine_intent(self, utterance, num_results=1)

Given an utterance, provide a valid intent.

:param utterance: an ascii or unicode string representing natural language speech

:param num_results: a maximum number of results to be returned.

:return: A generator the yields dictionaries.

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
    
    def determine_intent(self, utterance, num_results=1):
        """
        Given an utterance, provide a valid intent.
        :param utterance: an ascii or unicode string representing natural language speech
        :param num_results: a maximum number of results to be returned.
        :return: A generator the yields dictionaries.
        """
        parser = Parser(self.tokenizer, self.tagger)
        parser.on('tagged_entities',
                  (lambda result:
                   self.emit("tagged_entities", result)))
        for result in parser.parse(utterance, N=num_results):
            self.emit("parse_result", result)
            best_intent = self.__best_intent(result)
            if best_intent and best_intent.get('confidence', 0.0) > 0:
                yield best_intent
    

[/ui-tab]
[/ui-tabs]

def emit(self, event, *args, **kwargs)

Emit `event`, passing *args to each attached function.

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
    
    def emit(self, event, *args, **kwargs):
        """
        Emit `event`, passing *args to each attached function.
        """
        # Pass the args to each function in the events dict
        for fxn in self._events[event]:
            fxn(*args, **kwargs)
    
[/ui-tab]
[/ui-tabs]

def listeners(self, event)

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
    
    def listeners(self, event):
        return self._events[event]
    

[/ui-tab]
[/ui-tabs]

def on ( self, event, f=None)

Returns a function that takes an event listener callback

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
    
    
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
    

[/ui-tab]
[/ui-tabs]

def once(self, event, f=None)

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
    
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
    
[/ui-tab]
[/ui-tabs]

def register_entity(self, entity_value, entity_type)

Register an entity to be tagged in potential parse results

:param entity_value: the value/proper name of an entity instance (Ex: "The Big
Bang Theory")

:param entity_type: the type/tag of an entity instance (Ex: "Television Show")

:return: None

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]

    def register_entity(self, entity_value, entity_type):
        """
        Register an entity to be tagged in potential parse results
        :param entity_value: the value/proper name of an entity instance (Ex: "The Big Bang Theory")
        :param entity_type: the type/tag of an entity instance (Ex: "Television Show")
        :return: None
        """
        self.trie.insert(entity_value.lower(), data=entity_type)
        self.trie.insert(entity_type.lower(), data='Concept')
    
[/ui-tab]
[/ui-tabs]

def register_intent_parser(self, intent_parser)

"Enforce" the intent parser interface at registration time.

:param intent_parser:

:return: None

:raises ValueError on invalid intent

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]

    def register_intent_parser(self, intent_parser):
        """
        "Enforce" the intent parser interface at registration time.
        :param intent_parser:
        :return: None
        :raises ValueError on invalid intent
        """
        if hasattr(intent_parser, 'validate') and callable(intent_parser.validate):
            self.intent_parsers.append(intent_parser)
        else:
            raise ValueError("%s is not an intent parser" % str(intent_parser))
    


[/ui-tab]
[/ui-tabs]

def register_regex_entity(self, regex_str)

A regular expression making use of python named group expressions.

Example: (?P.*)

:param regex_str: a string representing a regular expression as defined above

:return: None

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
    
    def register_regex_entity(self, regex_str):
        """
        A regular expression making use of python named group expressions.
        Example: (?P.*)
        :param regex_str: a string representing a regular expression as defined above
        :return: None
        """
        if regex_str and regex_str not in self._regex_strings:
            self._regex_strings.add(regex_str)
            self.regular_expressions_entities.append(re.compile(regex_str, re.IGNORECASE))
    
[/ui-tab]
[/ui-tabs]

def remove_all_listeners(self, event)

Remove all listeners attached to event.

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]
    
    def remove_all_listeners(self, event):
        """
        Remove all listeners attached to `event`.
        """
        self._events[event] = []
    
[/ui-tab]
[/ui-tabs]

def remove_listener(self, event, function)

Remove the function attached to event.

[ui-tabs position="top-left" active="0" theme="badges"]
[ui-tab title="Hide"]
[/ui-tab]

[ui-tab title="Show Code"]

    def remove_listener(self, event, function):
        """
        Remove the function attached to `event`.
        """
        self._events[event].remove(function)
    
[/ui-tab]
[/ui-tabs]


