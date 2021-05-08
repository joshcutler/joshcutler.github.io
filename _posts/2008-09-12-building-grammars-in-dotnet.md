---
layout: post
title: "Building Grammars in .NET"
date: 2008-09-12 13:10 -0400
tags: c# systray
---

As I’ve shown before ([here]({% post_url 2008-06-05-microsoft-speech-1 %}), [here]({% post_url 2008-06-11-microsoft-speech-2 %}), and [here]({% post_url 2008-06-13-microsoft-speech-3 %})), the [Microsoft .NET Speech API](http://msdn.microsoft.com/en-us/library/ms723627(VS.85).aspx) allows you to quickly and easily build applications that take advantage of the good folks at [Microsoft Research](http://research.microsoft.com/)‘s work on speech recognition. The general process is that you can construct a grammar that the engine will recognize for you, and then event handlers for those recognized events will be triggered. This process then, will only be as effective as the grammars that you can construct for the the [SpeechRecognitionEngine](http://msdn.microsoft.com/en-us/library/system.speech.recognition.speechrecognitionengine.aspx). In this post I’ll show you some of the things that you can do to construct Grammars in C#.

The SpeechRecognitionEngine class can load one or many Grammar objects. The MSDN page describes these Grammar objects in the following way:

> The System.Speech.Recognition..::.Grammar class provides run time objects that allow an application to specify a specific combination of words, choices of words, and other speech elements that the Speech platform uses to identify meaningful phrases.

> The Grammar object fully supports the W3C Speech Recognition Grammar Specification (SRGS) and Context Free Grammar (CFG) specifications. For more information, see [SpeechRecognitionGrammar Specification](http://www.w3.org/TR/speech-grammar/).

> Grammars may be precise word phrases, such as “Turn the computer off,” or provide choices, semantic lookup tables or wildcards, such as “Change color to” and a look up table of acceptable values.

> An application’s recognition engine, as managed by instances of SpeechRecognizer or [T:System.Speech.Recognition.SpeechRecognitionEngine,] may create and load one or more instances Grammar, independently enabling or disabling particular grammars instance, and set Grammar properties such as priorities (Priority and weight (Weight)

So you can see that we can construct Grammars that will recognize certain types of input and then load these into the SpeechRecognitionEngine. These can be [prioritized](http://msdn.microsoft.com/en-us/library/system.speech.recognition.grammar.priority.aspx) to help with conflict resolution as well as toggled on and off if you know that only certain grammars are appropriate for a certain context.


So how do we construct these grammars? Grammars can be constructed in a variety of ways, but we are going to examine the [GrammarBuilder](http://msdn.microsoft.com/en-us/library/system.speech.recognition.grammarbuilder.aspx) Class as this is the easiest way to programmatically construct a Grammar.


The simplest example is simple string matching. If you have a discrete atomic command (e.g. shutdown) that takes no parameters, you can [initialize a GrammarBuilder with a string](http://msdn.microsoft.com/en-us/library/ms554229.aspx) and it will fire its recognize event when that string is heard.

```c#
GrammarBuilder stringGb = new GrammarBuilder("shutdown");
```

If this was added to a Grammar and loaded in the Recognizer it would fire a recognized event for this Grammar whenever the word “shutdown” was heard. This is easy enough, but somewhat limited, because what if you wanted to also allow them to say “turn off” or “quit”? At this point you would have to add 3 Grammars, one with each of those strings. Enter the [Choices](http://msdn.microsoft.com/en-us/library/system.speech.recognition.choices.aspx) class.


The Choices class allows us to create a phrase where one part of the phrase may have many different acceptable values. In our above example, we want the verbs “shutdown”, “turn off” and “quit” to be interchangeable. To show how this can be used with a phrase, lets make the user ask nicely by preceding either of those with “please”.

```c#
GrammarBuilder choicesGB = new GrammarBuilder("please");
Choices verbChoices = new Choices("shutdown", "turn off", "quit");
choicesGB.Append(verbChoices);
```

Now our grammar will trigger an event for all of the following phrases:
- please shutdown
- please turn off
- please quit

You can probably now imagine how you could combine strings and choices to come up with a variety of different recognizable phrases. However, there is a lot more that this engine can handle, but before we get into that lets review what happens when a phrase is recognized by the SpeechRecognitionEngine.


Remember that when a phrase is recognized, all that the engine will trigger is a [SpeechRecognizedEvent](http://msdn.microsoft.com/en-us/library/system.speech.recognition.speechrecognizedeventargs.aspx) to which you can assign a handler. This same event is fired for all phrases in all grammars that are loaded, so the problem becomes figuring out which phrase triggered the event. There are numerous strategies for dealing with this so I won’t go into detail here, but note that the [SpeechRecognizedEventArgs](http://msdn.microsoft.com/en-us/library/system.speech.recognition.recognitioneventargs.result.aspx) object has a [RecognitionResult](http://msdn.microsoft.com/en-us/library/system.speech.recognition.recognitionresult.aspx) that contains a pointer to the Grammar as well as the Text of the phrase in addition to any Semantic Values that you have assigned (more on that later).


Why take that little detour? Because once you’ve tried to actually do something with the result of a recognition event you’ll realize the utility of [SemanticValues](http://msdn.microsoft.com/en-us/library/system.speech.recognition.semanticvalue.aspx). Semantic Values allows you to assign values to certain responses so that you do not have to do annoying string parsing in the event handler.


Our first basic example will show how we can simply get the appropriate text in the event handler without having to do string parsing. Lets say that we know that the user is either going to say ‘yes’ or ‘no’ to a request, but we want to recognize synonyms of those phrases. We can do something like the following:

```c#
GrammarBuilder yesChoices = new Choices("yes", "yeah", "ok").ToGrammarBuilder();
yesChoices.Append(new SemanticResultValue(true));
GrammarBuilder noChoices = new Choices("no", "nope").ToGrammarBuilder();
noChoices.Append(new SemanticResultValue(false));
Choices allChoices = new Choices(yesChoices, noChoices);
SemanticResultKey choiceKey = new SemanticResultKey("YesNoBool", allChoices);
GrammarBuilder choiceGB = new GrammarBuilder(choiceKey);
```

What does this do for us? It creates a GrammarBuilder that has 2 choices. Each of these Choices has a SemanticValue that can be obtained in the recognition handler. Semantically there is a Yes choice (which is actually itself a choice of “yes”, “yeah” and “ok”) and a No choice (“no” and “nope”). The Recognition engine, since we have assigned semantic values to these, will set the “YesNoBool” variable to either true or false depending on which Semantic choice the user makes. This is great for us. No string parsing, and thus we can easily build up grammars with many synonyms and never worry about adding more complexity in the event handler.


So far we have looked at inputs that are just combinations of predefined words. This is great for many scenarios, but what if we want to allow more free form input? Lets say that our grammar is built to allow users to get a current stock price. You would probably want to match on any of the following phrases:
- Show me the stock price of foo
- Let me see the stock price of foo
- What is the price of foo


Here foo may be any stock ticker and lets say we’re lazy so we don’t want to hard code all of them. The solution lies in the [DictationGrammar](http://msdn.microsoft.com/en-us/library/system.speech.recognition.dictationgrammar.aspx) class. This class is exactly what it sounds like–a class for dealing with understand user dictation. Now, extracting the ‘foo’ from any of those in the event handler could be error prone, especially as you add more and more recognized ways of asking for a stock price. This is where semantic values come into play again. So lets see how this might work:

```c#
GrammarBuilder stockGB = new GrammarBuilder();
GrammarBuilder dictation = new GrammarBuilder();
dictation.AppendDictation("spelling");
stockGB.Append(new Choices("Show me the stock price of", "Let me see the stock price of", "What is the stock price of"));
stockGB.Append(new SemanticResultKey("DictationInput", dictation));
```

This code creates a choice of antecendents and then appends a SemanticResultKey whose name is DictationInput. Note that since we assigned no semantic value, the value that is assigned to DictationInput will be the actual text of the dictation. Finally, note that when we [created the Dictation we passed the parameter “spelling”](http://msdn.microsoft.com/en-us/library/ms576566.aspx). This was to let the Recognizer know that we wanted the user to spell out the ticker name and that it should not interpret the input as words. If we had not passed that parameter it would have tried to match the input to words. That type of dictation might be useful if you were trying to build a voice IM client for example.


That’s all for now. Good luck and let me know if you build anything cool.