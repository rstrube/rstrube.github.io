---
title: 'Wayland Cheatsheet'
tags: ['linux','wayland','cheatsheet','electron']
---
# Wayland Cheatsheet
## Listing Applications Running in XWyland
You can use the `xlsclients` program to view any applications that are running under XWayland.  This can be an excellent way to determine if you're running a given application natively in Wayland or via XWayland.

For example:

```shell
robert@mini ~> xlsclients  
mini  electron
```

Here we can see that I have an electron application running in XWayland as opposed to natively.

## Checking your Current Desktop Session
You can check your current session by executing: `echo $XDG_SESSION_TYPE`.  You'll see something like:

```shell
robert@mini ~> echo $XDG_SESSION_TYPE  
wayland
```

## Electron Applications in Wayland
By default electron applications will run in XWayland.  You can override this by passing in some additional command line parameters:

*Note: the application needs to be built against Electron 12+ for this to work!*

```--enable-features=UseOzonePlatform --ozone-platform=wayland```

Often times you can copy and edit a `.desktop` file and supply these additional parameters.  For example here is the process for setting up Visual Studio Code to run as a native wayland application:

```shell
cp /usr/share/applications/visual-studio-code.desktop ~/.local/share/applications/.

sed -i 's/\/usr\/bin\/code/& --enable-features=UseOzonePlatform --ozone-platform=wayland/' ~/.local/share/applications/visual-studio-code.desktop
```

This copies the default `.desktop` file from `/usr/share/application` to a user's home directory and then updates it to include the additional command line parameters.

The end result looks like:

```ini
[Desktop Entry]
Name=Visual Studio Code
Comment=Code Editing. Refined.
GenericName=Text Editor
Exec=/usr/bin/code --enable-features=UseOzonePlatform --ozone-platform=wayland --no-sandbox --unity-launch %F
Icon=visual-studio-code
Type=Application
StartupNotify=false
StartupWMClass=Code
Categories=Utility;TextEditor;Development;IDE;
MimeType=text/plain;inode/directory;application/x-visual-studio-code-workspace;
Actions=new-empty-window;
Keywords=vscode;

[Desktop Action new-empty-window]
Name=New Empty Window
Exec=/usr/bin/code --enable-features=UseOzonePlatform --ozone-platform=wayland --no-sandbox --new-window %F
Icon=visual-studio-code
```