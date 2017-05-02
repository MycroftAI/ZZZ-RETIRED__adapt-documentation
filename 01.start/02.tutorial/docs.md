---
title: Tutorial
taxonomy:
    category:
        - docs
---

A step by step explanation of Adapt, using the single_intent_parser.py example as a basis.


    """
    A sample intent that uses a fixed vocabulary to extract entities for an intent

    try with the following:
    PYTHONPATH=. python examples/single_intent_parser.py "what's the weather like in tokyo"
    """

Import json for serializing the adapt output, and sys reading command line arguments.

    import json
    import sys
    
Import the IntentBuilder and IntentDeterminationEngine

    from adapt.intent import IntentBuilder
	from adapt.engine import IntentDeterminationEngine

Instantiate an IntentDeterminationEngine

	engine = IntentDeterminationEngine()

Declare a collection of weather keywords. These keywords act as hints to adapt about which intent context is being referenced by an utterance.

	weather_keyword = [
	    "weather"
	]

Register each weather keyword with the engine

	for wk in weather_keyword:
	    engine.register_entity(wk, "WeatherKeyword")

Declare a collection of weather types. These act as a query parameter on a weather intent. Ex: Will it rain in Seattle tomorrow? (Hint: probably)

	weather_types = [
	    "snow",
	    "rain",
	    "wind",
	    "sleet",
	    "sun"
	]

Register each weather type with the engine

	for wt in weather_types:
	    engine.register_entity(wt, "WeatherType")

Declare a collection of locations. 

	locations = [
	    "Seattle",
	    "San Francisco",
	    "Tokyo"
	]

Register each location with the engine

	for loc in locations:
	    engine.register_entity(loc, "Location")

Construct an intent parser. It is named WeatherIntent, requires a WeatherKeyword and Location, and can optionally include WeatherType

	weather_intent = IntentBuilder("WeatherIntent")\
	    .require("WeatherKeyword")\
	    .optionally("WeatherType")\
	    .require("Location")\
	    .build()

Register the intent parser with the engine

	engine.register_intent_parser(weather_intent)


Declare an entry point for the script.

	if __name__ == "__main__":
    
Pass the command line arguments to this script as an utterance into engine.determine_intent. determine_intent returns a generator, so iterate through the results.

	    for intent in engine.determine_intent(' '.join(sys.argv[1:])):

If the confidence is > 0, this is a valid intent.

	        if intent.get('confidence') > 0:
            
Serialize the intent and print it to stdout.

	            print(json.dumps(intent, indent=4))