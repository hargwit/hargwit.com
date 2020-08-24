---
template: post
title: Editing VS code settings
slug: editing-vs-code-settings
draft: false
date: 2019-10-14T07:00:00.000Z
description: A quick tutorial showing the three different ways of maintaining
  your VS code settings.
category: Devops
tags:
	- vscode
	- visual studio code
---

Vscode offers three levels for editing settings:

- Globally
- Folder
- Workspace

Each level has a couple of methods.

# Globally

Any changes you make this way will take global effect - i.e. they will affect
all your vscode projects.

You can edit your settings at this level in two ways. The first is via the
settings menu, accessed by clicking the gear icon on the bottom left of the page
followed by selecting the user tab at the top of the page. See below.

![Global settings menu location](/media/vscode-settings-menu-location.png "Global settings menu location")

You can also alter your global settings by editing the global `settings.json`
file. This file is usually in one of these locations:

- Windows: `%APPDATA%\Code\User\settings.json`
- MacOs: `$HOME/Library/Application Support/Code/User/settings.json`
- Linux: `$HOME/.config/Code/User/settings.json`

A quicker way of editing this is to use the vscode command palette which can be
accessed using `Ctrl Shift P` (or `Cmd Shift P` on a Mac). Type in
`Open settings json` and select the option `Preferences: Open Settings (JSON)`.
You can now edit your settings at the global level in `settings.json`.

# Folder

If you want to alter settings but only have them apply at the folder level then
create a `.vscode` directory in the target folder. Next create a `settings.json`
file in that folder. Add any and all folder level settings to this file.

![Settings.json location](/media/vscode-folder-settings.png "Location of the VSCode settings.json file")

Remember that this file needs to have json format:

```jsx
{
  "key":"value"
}
```

# Workspace

Finally, you can edit the settings of a vscode workspace (create a workspace by
hitting `File > Save Workspace As...`). This one is a little different as it
does not use a `settings.json` file, instead when vscode creates a workspace it
adds a file called `your_workspace.code-workspace`.

Open that file and you will see something like

```jsx
{
	"folders": [
		{
			"path": "/Path/to/project"
		}
	]
}
```

You can add settings to this file by adding a `"settings"` property to the
object:

```jsx
{
	"folders": [
		{
			"path": "/Path/to/project"
		}
  ],
  "settings": {
    "key": "value"
  }
}
```

And that's it, now you know how to alter the settings in vscode at 3 different
levels.

Thanks for reading!
