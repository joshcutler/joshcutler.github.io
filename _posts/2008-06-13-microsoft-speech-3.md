---
layout: post
title: "Microsoft Speech III: Setting up a pluggable infrastructure"
date: 2008-06-13 13:10 -0400
tags: speech c#
---
This post will build on the work done in [Microsoft Speech II]({% post_url 2008-06-11-microsoft-speech-2 %}), where we saw how to interact with our computer via voice. In this tutorial we will build a plugin infrastructure which we will use to add functionality to our Speech Project in the future. While we won’t be adding any new functionality in this tutorial, we will be refactoring what we had into a “Personality” plugin. Please note that these ideas aren’t uniquely mine and I borrow heavily from [Matthew Cochran’s excellent tutorial](http://www.c-sharpcorner.com/UploadFile/rmcochran/plug_in_architecture09092007111353AM/plug_in_architecture.aspx) on the subject of plugins – I just adapted them for the speech project’s purposes.


Lets start by opening the project as we left it in [Microsoft Speech II]({% post_url 2008-06-11-microsoft-speech-2 %}). At this point we should refactor our namespace to be something more useful than “Tutorial1″, so lets change that to “Speech_Project” for now (make sure to update the XAML as well).


Next we need to add some folders for building out our framework. Add two folders named “Core” and “Plugins” to your project.


Now lets move the Say method out of the Window1 class and into its own class called Talker. We can use this later when we add more options to the output (SMS, IM, Email, etc.). So create a Talker class in the Speech_Project.Core namespace and move the say method into it:

```c#
using System.Speech.Synthesis;
namespace Speech_Project.Core
{
    public class Talker
    {
        public static void Say(string input)
        {
            var synth = new SpeechSynthesizer();
            var sayThis = new Prompt(input);
            synth.Speak(sayThis);
        }
    }
}
```

Next we need to define the interface to which all of our plugins will conform. Plugins for now will need to tell the engine which grammars they support and then have an event handler for when their grammar is triggered (see [Tutorial 2]({% post_url 2008-06-11-microsoft-speech-2 %})). Finally, they’ll need to tell the engine which grammars are theirs by providing a method for getting the grammar name. Lets create an ISpeechPlugin interface that looks like the following:

```c#
using System.Speech.Recognition;
namespace Speech_Project.Core
{
    public interface ISpeechPlugin
    {
        Grammar getGrammar();
        void handleSpeechInput(string input);
        string getGrammarName();
    }
}
```

In addition to the plugin interface we should add a plugin attribute class. This will allow us to get some meta data about the plugins when we load them, look for version collisions, and other useful things down the road. So lets add a SpeechPluginAttribute:

```c#
using System;
namespace Speech_Project.Core
{
    [AttributeUsage(AttributeTargets.Class)]
    class SpeechPluginAttribute : Attribute
    {
        public SpeechPluginAttribute(string name, string description, string version)
        {
            _description = description;
            _name = name;
            _version = version;
        }
        private string _description;
        private string _name;
        private string _version;
        public string Description
        {
            get { return _description; }
            set { _description = value; }
        }
        public string Name
        {
            get { return _name; }
            set { _name = value; }
        }
        public string Version
        {
            get { return _version; }
            set { _version = value; }
        }
    }
}
```

Now that we have our Interface defined, lets create our first plugin. Start by creating a new Project in your solution for your Personality plugin, of the type class library. Change its Build properties to output to the Plugins directory of the main Project (to get this working in VS debug mode make sure it is /bin/debug/Plugins/). Next make sure you reference the main project and set the Main class to be an instance of the ISpeechPlugin Module (and make sure you add our custom attribute).


Now we can take the code that was in the Window1 class and move it into this Plugin. So lets take the grammar construction code and the event handler and put it in the Personality class like so:

```c#
using Speech_Project.Core;
using System.Speech.Recognition;
namespace Speech_Project.Plugins
{
    [SpeechPluginAttribute("Personality Plugin",
    "This plugin handles all of the personality like responses for the plugin",
    "1.0")]
    public class PersonalityPlugin : ISpeechPlugin
    {
        private string _grammarName = "PersonalityPlugin";
        #region ISpeechPlugin Members
        public Grammar getGrammar()
        {
            //Create a set of choices
            Choices thisChoices = new Choices("Computer",
            "What is your name?",
            "What is your favorite blog?"
            );
            //Create a grammar based on these choices
            Grammar thisGrammar = new Grammar(thisChoices.ToGrammarBuilder());
            // Set the Grammar name
            thisGrammar.Name = _grammarName;
            return thisGrammar;
        }

        public void handleSpeechInput(string input)
        {
            switch (input)
            {
            case "Computer":
                Talker.Say("Yes master?");
                break;
            case "What is your name?":
                Talker.Say("My name is Anna");
                break;
            case "What is your favorite blog?":
                Talker.Say("Qurbit of course");
                break;
            }
        }

        public string getGrammarName()
        {
            return _grammarName;
        }
        #endregion
    }
}
```

Now that we’ve done a little refactoring (yes you should do more: abstract out the strings, etc.), lets get to the fun part–dynamic binding. Lets start by creating an Engine class that will do the heavy lifting. The engine class will look in the plugins folder for any .dll files and if they implement the ISpeechPlugin will instantiate these into a List via Reflection. After loading all of the plugins, we can get our engine to load all of their grammars. Then when the engine recognizes any of the Choices in the loaded grammars we can trigger the event handler for the correct ISpeechPlugin.

```c#
using System.Collections;
using System.IO;
using System;
using System.Collections.Generic;
using System.Reflection;
using System.Speech.Recognition;
namespace Speech_Project.Core
{
    class Engine
    {
        public static List _plugins;
        public static void LoadPlugins()
        {
            //Get all the assemblies in the Plugins folder
            List assemblies = LoadAssemblies();
            //Load the plugins
            _plugins = GetPlugIns(assemblies);
        }
        private static List LoadAssemblies()
        {
            //Look in the plugin dir for .dlls
            DirectoryInfo dInfo = new DirectoryInfo(Path.Combine(Environment.CurrentDirectory, "Plugins"));
            FileInfo[] files = dInfo.GetFiles("*.dll");
            List plugInAssemblyList = new List();
            if (null != files)
            {
                foreach (FileInfo file in files)
                {
                plugInAssemblyList.Add(Assembly.LoadFile(file.FullName));
                }
            }
            return plugInAssemblyList;
        }
        private static List GetPlugIns(List assemblies)
        {
            List availableTypes = new List();
            foreach (Assembly currentAssembly in assemblies)
            availableTypes.AddRange(currentAssembly.GetTypes());
            // get a list of objects that implement the ICalculator interface AND
            // have the CalculationPlugInAttribute
            List pluginList = availableTypes.FindAll(delegate(Type t)
            {
                List interfaceTypes = new List(t.GetInterfaces());
                object[] arr = t.GetCustomAttributes(typeof(SpeechPluginAttribute), true);
                return !(arr == null || arr.Length == 0) &&  interfaceTypes.Contains(typeof(ISpeechPlugin));
            });
            // convert the list of Objects to an instantiated list of ICalculators
            return pluginList.ConvertAll(delegate(Type t) { return Activator.CreateInstance(t) as ISpeechPlugin; });
        }
    }
}
```

At this point, we have an infrastructure that allows us to abstract out the functionality in a given plugin, and so long as there is a .dll in the plugins folder that implements the ISpeechModule interface we can get an instance of it. Now all we need to do is get the engine to route the events to the correct plugins when it recognizes input. So lets add a SpeechRecognized Event handler to the engine that will look through the plugins to see if any have a handler for the recognized grammar name and if so then call the handler in that plugin

```c#
public static void SpeechRecognized(object sender, SpeechRecognizedEventArgs e)
{
    foreach (ISpeechPlugin plugin in _plugins)
    {
        //This is the correct handler
        if (e.Result.Grammar.Name == plugin.getGrammarName())
        {
            plugin.handleSpeechInput(e.Result.Text);
        }
    }
}
```

Finally, lets add an InitializeSRE method to the engine that will create the SpeechRecognitionEngine object and load the grammars from the plugins as well as attach the SpeechRecognized handler:

```c#
public static SpeechRecognitionEngine InitializeSRE()
{
    //Create the speech recognition engine
    SpeechRecognitionEngine sre = new SpeechRecognitionEngine();
    //Set the audio device to the OS default
    sre.SetInputToDefaultAudioDevice();
    //Reset the Grammars
    sre.UnloadAllGrammars();
    //Load the plugins
    LoadPlugins();
    //Load all of the grammars
    foreach (ISpeechPlugin plugin in _plugins)
    {
        sre.LoadGrammar(plugin.getGrammar());
    }
    //Set the recognition mode
    sre.RecognizeAsync(RecognizeMode.Multiple);
    //Add an event Handler
    sre.SpeechRecognized +=
    new EventHandler(Engine.SpeechRecognized);
    return sre;
}
```

Now we can just call Engine.InitializeSRE in our window constructor and it will load all the plugins and handle all of the event routing. Build your plugin and run the WPF application and give it a try. Pretty simple huh? Now we can just keep building plugins to extend the functionality of our Speech Project. Stay tuned for more plugins.