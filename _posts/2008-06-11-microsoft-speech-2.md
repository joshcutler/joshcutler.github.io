---
layout: post
title: "Microsoft Speech II: Getting your computer to listen"
date: 2008-06-11 13:10 -0400
tags: speech c#
---
In this post I’ll show you how to take our simple project from the first example, [Microsoft Speech I]({% post_url 2008-06-05-microsoft-speech-1 %}), and get it to understand input spoken into a microphone. Note that everything I’m showing can be found in the [MSDN pages for the Speech API](http://msdn.microsoft.com/en-us/library/ms723627%28VS.85%29.aspx). Before we get started, make sure you have a microphone and it’s hooked up to your PC and all that other obvious stuff….


Lets begin by creating an instance of the [SpeechRecognitionEngine](http://msdn.microsoft.com/en-us/library/system.speech.recognition.speechrecognitionengine.aspx) class in the WPF window’s constructor. When doing this we have two options, to either run the Speech Recognizer in-process (your application’s process) or in Vista to use the OS’s Speech Recognizer out of process. In this tutorial we will be using it in-process. In order to get started make sure that you add the namespace:

```c#
using System.Speech.Recognition;
```

Now lets add some code to the Window’s constructor to create the object and get it ready to start listening:

```c#
public Window1()
{
    InitializeComponent();
    //Create the speech recognition engine
    SpeechRecognitionEngine sre = new SpeechRecognitionEngine();
    //Set the audio device to the OS default
    sre.SetInputToDefaultAudioDevice();
    //Reset the Grammars
    sre.UnloadAllGrammars();
}
```

At this point, the engine is listening to input, but doesn’t know what it should be listening for, nor what to do if it heard something interesting. The speech recognition engine uses [Grammars](http://msdn.microsoft.com/en-us/library/system.speech.recognition.grammar.aspx) (composed of [Choices](http://msdn.microsoft.com/en-us/library/system.speech.recognition.grammar.aspx)) to help parse the audio input. As a developer you can construct these grammars in a [variety of ways](http://gotspeech.net/blogs/michaeldunn/archive/2007/02/28/vista-sapi-5-3-speech-recognition.aspx), including XML that conforms to the [W3C grammar spec](http://www.w3.org/TR/speech-grammar/), but we will go the route of using a [GrammarBuilder](http://msdn.microsoft.com/en-us/library/system.speech.recognition.grammarbuilder.aspx) in C#.


Lets add some code to the constructor to load a grammar that recognizes some basic sentences in the audio input. Start by creating a Choices object with a few commands in it and then loading it as a grammar in the Speech Recognition Engine:

```c#
//Create a set of choices
Choices thisChoices = new Choices("Computer",
"What is your name?",
"What is your favorite blog?"
);
//Create a grammar based on these choices
Grammar thisGrammar = new Grammar(thisChoices.ToGrammarBuilder());
// Set the Grammar name
thisGrammar.Name = "Tutorial2";
//Load the grammar
sre.LoadGrammar(thisGrammar);
```

Now if we run the program, the SRE will be listening for choices that are in the loaded grammar to occur in the audio input stream. When it hears one of these it will trigger an event that we can react to. Lets add an event handler for a SpeechRecognizedEvent

```c#
//Set the eventing model
sre.RecognizeAsync(RecognizeMode.Multiple);
//Add an event handler
sre.SpeechRecognized += new EventHandler(sre_SpeechRecognized);
```

We now need to implement the event handler. The SpeechRecognizedEventArgs contains in its result a variety of interesting properties about the speech recognized, but the ones we’ll be using right now are the the Result.Grammar.Name and Result.Text. The grammar name is useful for knowing which grammar contained the choices triggering the event (if you have multiple grammars, as we will later), and the Text contains the text that triggered the event. Before we do this, lets implement a Say method that will contain the code from [Tutorial 1]({% post_url 2008-06-05-microsoft-speech-1 %}) in a Say method to help make the computer talk:

```c#
public void Say(string input)
{
    var synth = new SpeechSynthesizer();
    var sayThis = new Prompt(input);
    synth.Speak(sayThis);
}
```

Now, lets implement a handler for the Grammar that we created and its Choices. We’ll use the Say method to output responses:

```c#
private void sre_SpeechRecognized(object sender, SpeechRecognizedEventArgs e)
{
    if (e.Result.Grammar.Name == "Tutorial2")
    {
        switch (e.Result.Text)
        {
        case "Computer":
            Say("Yes master?");
            break;
        case "What is your name?":
            Say("My name is Anna");
            break;
        case "What is your favorite blog?":
            Say("Qurbit of course");
            break;
        }
    }
}
```

Now run your program and ask it the questions we just created. Pretty simple huh? You can do a lot with just these simple commands. If you are interested in other examples, James Ashley has a [fun example](http://www.codeproject.com/KB/vista/Vista_Speech_Recognition.aspx) of getting the speech recognition engine to work in Textpad over at CodeProject.


In our next installment, we’ll see how we can use Web Services to get our computer to automatically look things up on the internet for us and begin building a plugin based framework for extending our Engine.