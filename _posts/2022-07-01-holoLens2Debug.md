---
layout: post
title: "Debugging HoloLens 2 Project"
date:   2022-07-01 12:08:54 +0200
categories: HoloLens2 Unity Debug
---


In this tutorial we will see how to debug efficiently project made with Unity for UWP (especially for HoloLens 2). Some solutions provided could work with any UWP project but it has only been tested with HoloLens 2.

## The virtual console 
The first solution I bring to you is the more simple but less elegant. It consists in **creating a virtual console debugger directly into your project**. Therefore, when you test you prototype you could have a visual access to the `Debug.Log()` and so on messages. All of this directly with the headset on. 

Actually, such method would also work with VR headset too since it is only a UI Canvas (only minor modification need to be done then). The one I have made is inspired from this [tutorial video](https://www.youtube.com/watch?v=Pi4SHO0IEQY&t=104s). 

How to do on your own ? You just have to deal with these steps : 
#### For the displaying part
* Setting up a canvas to draw the message on
* For displaying the message, two solutions : 
	* Use on Text game object to gather all the messages (this is presented in the video above)
	* Create a Text prefab that you would instantiate at each message
The second solution *might be less efficient* (need to instantiate many GameObject).

#### For the backend part
* You just have to gather the log message using /Application.logMessageReceived/ event which is triggered whenever a log message is fired.
* Depending on the UI, communicate the message received to it.

I share here my GitHub repo which contains my own solution (it still need to be perfected, clearly not final version). # AJOUTER LE REPO

## Remote Debugging
This solution would only work for HoloLens (and probably only HoloLens 2 but not sure). 

Actually, you could use Unity Debugger in Visual Studio while your project is playing on the HoloLens.
This is very well explained on the Microsoft documentation so I would rather redirect you directly to it : [Managed debugging with Unity - Mixed Reality | Microsoft Docs](https://docs.microsoft.com/en-us/windows/mixed-reality/develop/unity/managed-debugging-with-unity-il2cpp)

## Post execution debugging : using .log files
The last solution is present here is probably the easiest one but not the most practical one. It only consists in gathering all debug message in a .log file before the application quit and get it back through your HoloLens File Explorer.

I know that Unity create its own .log files such as /UnityPlayer.log/ which could be find here in the HoloLens  `…/[projectName]/TempAsset/UnityPlayer.log`.
However, it didn’t seem to get what I wanted so I’ve made my own .log file.
To do so, you would have to : 
* Register all log message during the execution of the project 
* Dump them all in a string
* Write a .log file just before the application quit
* Get this .log file afterwards

Here is the bunch of code I’ve used to create this log file 
``` csharp
public class Logger : MonoBehavior
{
	private string _logRegister;
	
	private void Start()
	{
		Application.logMessageReceived += (msg, cond, type) => _logRegister += msg + "\n"; 
	}

	private void OnApplicationQuit()
	{
		//Write file (TO FINISH)
	}
}
```

Just create a game object with this script and you’re done.

---


*Thank you for reading this article, contact me if you see some mistakes I may have done or if you find a better way to deal with debugging, I would be happy to improve this article.*





