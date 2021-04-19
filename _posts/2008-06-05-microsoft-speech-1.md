---
layout: post
title: "Microsoft Speech I: Getting your computer to talk"
date: 2008-06-05 13:10 -0400
tags: speech c#
---

This is the first of what will hopefully be a series of posts that detail my explorations with the Microsoft Speech recognition and synthesis libraries built into Vista. I am working on automating certain parts of my home and (after seeing Iron Man), realized that I needed to see if I could get my house to talk with me. Now I’m not trying to write an app that I can chat with in such a way that it would pass a the Turing test, but I would love something that could respond to simple voice commands. I imagine sitting in my living room reading, discovering a word that I’m not sure the definition of, and yelling at my house to look it up for me. We’ll see how far I get.


On my setup I am running Vista Ultimate and Visual Studio 2008 for the screen shots.


Lets start by creating a new project. Lets make it a windows WPF application and call it whatever you’d like.


Now lets first get this thing to talk to us. Start by adding a button and a text box to your window and making the button text say “Say It”. Next lets add a click handler to the button that will make it take what is in the text box and pass it to the Text to Speech engine in .NET 3.5


In order to have access to the Speech Synthesization engine we will need to add a reference to our project. Go to Project > Add Reference and then on the .NET tab add the System.Speech library.


Now lets put this to use. Make sure that you add a using System.Speech.Synthesis to the Window1.xaml.cs file so that the namespace is referenced. Next, lets double click the button on your WPF Form to create an event handler. In the event handler we will get the text in the text box and pass it to an instance of a SpeechSynthesizer object, at which point our PC should talk to us. Your event handler should look like the following:

```c#
private void button1_Click(object sender, RoutedEventArgs e)
{
    string textToSay = textBox1.Text;
    SpeechSynthesizer synth = new SpeechSynthesizer();
    Prompt sayThis = new Prompt(textToSay);
    synth.Speak(sayThis);
}
```

Now we can run our application and type some text into the text box. When we click the box (make sure your speakers are on and all that good stuff), your PC should read it back to you. Simple huh?