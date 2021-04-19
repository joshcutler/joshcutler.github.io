---
layout: post
title: "Speech Plugins: Weather"
date: 2008-06-15 13:10 -0400
tags: speech c# plugins
---
In this entry we’ll look at building a basic plugin for a speech engine ([Microsoft Speech III]({% post_url 2008-06-13-microsoft-speech-3 %})), that will respond to simple queries about the weather. For this tutorial we will be using the [Yahoo Weather API](http://developer.yahoo.com/weather/). Note that if you are just interested in how to use their API, it should be easy enough to extract what you’re interested without doing the [previous tutorials]({% post_url 2008-06-13-microsoft-speech-3 %}).


We begin by creating a new project in our Speech Engine solution, and setting it up as described in the previous tutorial. One this is setup, we can begin by creating a WeatherPlugin class that implements the ISpeechPlugin interface:

```c#
using Speech_Project.Core;
using System.Speech.Recognition;
using System.Xml.XPath;
using System.Xml;
namespace Speech_Project.Plugins
{
    [SpeechPluginAttribute("Weather",
    "This retrieves weather data from the Yahoo weather services API",
    "1.0")]
    public class WeatherPlugin : ISpeechPlugin
    {
        private string _grammarName = "Weather Plugin";
        #region ISpeechPlugin Members
        public Grammar getGrammar()
        {
        ...
        }
        public void handleSpeechInput(string input)
        {
        ...
        }
        public string getGrammarName()
        {
            return _grammarName;
        }
        #endregion
    }
}
```

Now we just need to build our grammar and implement the handlers. In order to make it easier to standardize the language that we’ll be using, lets create a helper class called WeatherSpeechChoices that will contain the strings that we recognize in our grammar. We will give it two commands to start; one will get today’s forecast and the other, tomorrow’s.

```c#
public static class WeatherSpeechChoices
{
    //Inputs
    public const string forecastTomorrow = "What is tomorrow's forecast";
    public const string forecastToday = "What is today's forecast";
}
```

Now that we have this defined we can implement the grammar building method, as before we just need to add these choices to the grammar and the engine will compile all of the various plugin grammars.

```c#
public Grammar getGrammar()
{
    //Create a set of choices
    Choices thisChoices = new Choices(WeatherSpeechChoices.forecastTomorrow, WeatherSpeechChoices.forecastToday);
    //Create a grammar based on these choices
    Grammar thisGrammar = new Grammar(thisChoices.ToGrammarBuilder());
    // Set the Grammar name
    thisGrammar.Name = _grammarName;
    return thisGrammar;
}
```

Halfway there, now we just need to implement the event handler. This is the fun part, where we call the Weather service. Lets implement this by having the event handler call a getForecastString method that takes the number of days out as an integer parameter. Once we know we will build that we can write our event handler:

```c#
public void handleSpeechInput(string input)
{
    switch (input)
    {
        case WeatherSpeechChoices.forecastToday:
            string forecast = this.getForecastString(0);
            Talker.Say(forecast);
            break;
        case WeatherSpeechChoices.forecastTomorrow:
            string forecast1 = this.getForecastString(1);
            Talker.Say(forecast1);
            break;
    }
}
```

Now to implement the getForecastString method. Take a look at the [API](http://developer.yahoo.com/weather/) and you will see that we need to be issuing requests to http://weather.yahooapis.com/forecastrss. This URL takes two types of parameters, US zipcodes OR Yahoo location IDs. In order to get the Yahoo location ID, their page states that:

> The location parameter can be a US Zip code or a location ID. To find your location ID, browse or search for your city from the Yahoo! Weather home page. The weather ID is in the URL for the forecast page for that city. You can also get the location ID by entering your zip code on the home page. For example, if you search for Los Angeles on the Yahoo! Weather home page, the forecast page for that city is http://weather.yahoo.com/forecast/USCA0638.html. The location ID is USCA0638.


However in this tutorial we will be using a zipcode (I live in the US). You may additionally change the units (celsius or farenheit) by appending a “u=c” or “u=f” to the URL. For this example lets, add two private members to our WeatherPlugin class that will help us create the URL:

```c#
private string _zipCode = "55407";
private const string _serviceURL = "http://xml.weather.yahoo.com/forecastrss?p=";
```

Now that we know what the URL is, we need to know how to parse the response. This service will respond with an XML document that is [RSS 2.0](http://cyber.law.harvard.edu/rss/rss.html) compliant. We will make use the XPathNavigator .NET class to parse the response. So lets see what our method looks like:

```c#
public string getForecastString(int daysFromNow)
{
    // Create a new XmlDocument
    XPathDocument doc = new XPathDocument(_serviceURL + _zipCode);
    // Create navigator
    XPathNavigator navigator = doc.CreateNavigator();
    // Set up namespace manager for XPath
    XmlNamespaceManager ns = new XmlNamespaceManager(navigator.NameTable);
    ns.AddNamespace("yweather", "http://xml.weather.yahoo.com/ns/rss/1.0");
    //Get the forecast with XPath
    XPathNodeIterator nodes = navigator.Select("//rss/channel/item/yweather:forecast", ns);
    string day = "";
    string text = "";
    string low = "";
    string high = "";
    nodes.MoveNext();
    XPathNavigator node = nodes.Current;
    if (daysFromNow == 1)
    {
        nodes.MoveNext();
        node = nodes.Current;
    }
    day = node.GetAttribute("day", ns.DefaultNamespace);
    text = node.GetAttribute("text", ns.DefaultNamespace);
    low = node.GetAttribute("low", ns.DefaultNamespace);
    high = node.GetAttribute("high", ns.DefaultNamespace);
    string forecast = "It will be " + text + " with a high of " + high + ", and a low of " + low;
    return forecast;
}
```

Now lets break this method down a little further. In order to parse the document correctly we need to load the yweather namespace (http://xml.weather.yahoo.com/ns/rss/1.0). We select our first node for parsing by jumping to the first yweather:forecast item. This particular service only returns a 1 day forecast, so we know that if the daysFromNow variable is either 1 or 0 (you should implement something to enforce this). Once we have retrieved the correct node we get the attributes as described by the API, construct an English sounding sentence out of them, and return that to our Engine class. Note that if we are worried about lag or connection overhead we could cache the navigator object and use that for subsequent calls. In this example though, we hit the webpage every time you ask for the weather.


This service returns much more than the temperature in its response including windchill, speed and direction, atmospheric conditions, sunrise, sunset and others. It also returns the current conditions in the yweather:condition node. For an example rss feed, here is a [link to the output for Minneapolis](http://weather.yahooapis.com/forecastrss?p=55407). As you can see its pretty easy to extend the plugin and get it to tell you this other information if you want. Until next time, have at it and let me know what you come up with.