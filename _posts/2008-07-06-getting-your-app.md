---
layout: post
title: "Getting your C# app to run in the System Tray"
date: 2008-07-06 13:10 -0400
tags: c# systray
---
While making some additions to the [Speech Framework]({% post_url 2008-06-05-microsoft-speech-1 %}), I needed to figure out how to get my WPF application to run in the system tray. For the speech framework, the sys-tray is a natural place for the application to run, as we ideally don’t want to see any UI and the application should be always on. I opted out of a service, because I did want the rich UI of an application and for it to show up in task manager, etc. It was very straightforward, but I thought I’d write up the steps for future reference.

We will start by creating a [NotifyIcon](http://msdn.microsoft.com/en-us/library/system.windows.forms.notifyicon.aspx) object in our Window’s Constructor. Note that it is in the System.Windows.Forms namespace, and you may need to add a reference to your project in order to access it (it is not there by default in a new WPF project). You may also need to add a System.Drawing reference to your project as well. Once your references are in order, lets write some code to create the icon for us in the Window Constructor:

```c#
//Set up the system tray icon
_ni = new NotifyIcon();
_ni.BalloonTipTitle = "Qurbit Home Automation Engine";
_ni.BalloonTipText = "The engine is still running, it has just been minimized.  Double click to restore to normal size";
_ni.Text = "Qurbit Home Automation Engine";
//Add a double click handler
_ni.MouseDoubleClick += new System.Windows.Forms.MouseEventHandler(_ni_MouseDoubleClick);
try
{
    _ni.Icon = new System.Drawing.Icon("Images\\flag.ico");
}
catch (Exception e)
{
    Debug.Print(e.StackTrace);
}
```

The above code will create the necessary NotifyIcon object and give it an icon, text, etc. Customize it as is appropriate.


Next we want to make sure that when a user minimizes the window we show the icon and do not show our application on the task bar. To do so, we need to trap the Window’s OnStateChangedEvent. For more info on how window events work [start here](http://msdn.microsoft.com/en-us/library/ms748948.aspx). In our xaml we need to add this attribute to the window element:

```
StateChanged=”Window_StateChanged”
```

Then in C#, we can add a handler for this. First however, let’s write a helper function for toggling the visibility of our window. It will take a boolean that indicates whether the window should live in the system tray or not.

```c#
private void windowInSysTray(bool inTray)
{
    if (inTray)
    {
        //Hide the window
        this.ShowInTaskbar = false;
        this.WindowState = WindowState.Minimized;
        if (_ni != null)
        {
            //Show the icon
            _ni.Visible = true;
            //Show the balloon tip
            //_ni.ShowBalloonTip(2000);
        }
    } else {
        //Show the window
        this.ShowInTaskbar = true;
        //Restore it
        this.WindowState = WindowState.Normal;
        //Bring it to the front
        this.Activate();
        //Remove the sys tray icon
        if (_ni != null)
        {
            _ni.Visible = false;
        }
    }
}
```

Finally, the event handler looks like the following:

```c#
private void Window_StateChanged(object sender, EventArgs e)
{
    //If the state is minimized then change its display type
    if (this.WindowState == WindowState.Minimized)
    {
        windowInSysTray(true);
    } else {
        windowInSysTray(false);
    }
}
```

Next we need to implement the double click handler for the notify icon to restore the application:

```c#
private void _ni_MouseDoubleClick(object sender, System.Windows.Forms.MouseEventArgs e)
{
    windowInSysTray(false);
}
```

At this point you should be able to minimize and restore your application as you would expect. There is on final bit of cleanup that we are going to want to include, and that is to handle what happens when the window is closed. If we allow the user to close the window with the close button, then we need to explicitly remove the notify icon or it will stay down there until the area is redrawn (the user mouses over it). For my app however I am going to make the close button also minimize the app to the sys tray and then you really close via a sys tray menu. So lets grab onto the OnClosing Method by adding the following attribute to our window object in the xaml code:

```
Closing=”Window_Closing”
```

Then lets write an event handler:

```c#
private void Window_Closing(object sender, System.ComponentModel.CancelEventArgs e)
{
    //Trap the event
    e.Cancel = true;
    //Put it in the system tray
    windowInSysTray(true);`
}
```

Now, when we close the application, it minimizes itself to the system tray as well. Unfortunately, if you run it at this point there is no way to close the application smoothly, so lets add a context menu to the system tray icon that has a close option.


In order to get started, lets add a click handler to the NotifyIcon in the window constructor:

```c#
_ni.MouseClick += new System.Windows.Forms.MouseEventHandler(_ni_MouseClick);
```

Then we need to implement the click handler. It will be very simple, and just check for a right mouse click. If it detects one, then it will show the context menu:

```c#
void _ni_MouseClick(object sender, System.Windows.Forms.MouseEventArgs e)
{
    if (e.Button == MouseButtons.Right)
    {
        //Show the context menu
        System.Windows.Controls.ContextMenu menu = (System.Windows.Controls.ContextMenu)this.FindResource("SysTrayContextMenu");
        menu.IsOpen = true;
    }
}
```

In order for that to work we now need to add a context menu. There a couple of ways we could do this, but lets build ours in XAML so that down the road we can have some fun with it. To do this, lets add a context menu to our XAML file, as the first element within the window.


Now we have a context menu, with two options. All that remains is to implement their click handlers. The open option is will just open up the window by calling our windowInSysTray method, and the exit one will remove our NotifyIcon (cleanup so that it doesn’t linger around) and then shutdown the application.

```c#
private void MenuOpen_Click(object sender, RoutedEventArgs e)
{
    windowInSysTray(false);
}

private void MenuExit_Click(object sender, RoutedEventArgs e)
{
    //Remove the system tray icon
    _ni.Dispose();
    //Shut it down
    System.Windows.Application.Current.Shutdown();
}
```

There you have it. Pretty simple stuff, and if you want to add more options or add fancier effects just edit the xaml file.