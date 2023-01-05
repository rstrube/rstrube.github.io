---
title: 'Wayland Cheatsheet'
tags: ['linux','wayland','cheatsheet','electron']
date: 2023-01-04
---
# Wayland Cheatsheet {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

## Listing Applications Running in XWyland
You can use the `xlsclients` program to view any applications that are running under XWayland.  This can be an excellent way to determine if you're running a given application natively in Wayland or via XWayland.

!!! tip

    For Arch Linux install the `xorg-xlsclients` package to have access to the `xlsclients` utility.

```shell
# list all applications running under xwayland
robert@host ~> xlsclients  
host  electron
```

Here we can see that there is electron application running in XWayland as opposed to natively.

## Checking your Current Desktop Session
You can check your current session by executing: `echo $XDG_SESSION_TYPE`.

```shell
# explicitly output if a wayland session is running
robert@host ~> echo $XDG_SESSION_TYPE  
wayland
```

## Electron Applications in Wayland
By default electron applications will run in XWayland.  You can override this by passing in some additional command line parameters:

```shell
# older electron applications
--enable-features=UseOzonePlatform,WebRTCPipeWireCapturer --ozone-platform=wayland

# newer electron applications (electron 20+)
--enable-features=WebRTCPipeWireCapturer --ozone-platform-hint=auto
```

!!! warning

    The application needs to be built against Electron 12+ for this to work!

Often times you can copy and edit a `.desktop` file and supply these additional parameters.

!!! example "Copying .desktop file and updating it with additional parameters"

    !!! warning

        VSCode now uses a dedicated configuration file (`~/.config/code-flags.conf`) to specify these parameters. The original process is still outlined below as a reference for older electron applications.

        ```config title="~/.config/code-flags.conf"
        --enable-features=UseOzonePlatform,WebRTCPipeWireCapturer
        --ozone-platform=wayland
        ```

    !!! warning

        Chrome uses a dedicated configuration file (`~/.config/chrome-flags.conf`) to specify these parameters.

        ```config title="~/.config/chrome-flags.conf"
        --enable-features=WebRTCPipeWireCapturer
        --ozone-platform-hint=auto
        ```

    !!! warning

        More modern electron applications (20+) will read from `~/.config/electron-flags.conf` but the original process is still outlined below as a reference for older electron applications.

        ```config title="~/.config/electron-flags.conf"
        --enable-features=WebRTCPipeWireCapturer
        --ozone-platform-hint=auto
        ```

    ```shell
    # copy the .desktop file to ~/.local/share/applications
    cp /usr/share/applications/visual-studio-code.desktop ~/.local/share/applications/.

    # use sed to update the local .desktop file
    sed -i 's/\/usr\/bin\/code/& --enable-features=UseOzonePlatform,WebRTCPipeWireCapturer --ozone-platform=wayland/' ~/.local/share/applications/visual-studio-code.desktop
    ```

    This copies the default `.desktop` file from `/usr/share/application` to a user's home directory and then updates it to include the additional command line parameters.

    The end result looks like:

    ```ini
    [Desktop Entry]
    Name=Visual Studio Code
    Comment=Code Editing. Refined.
    GenericName=Text Editor
    Exec=/usr/bin/code --enable-features=UseOzonePlatform,WebRTCPipeWireCapturer --ozone-platform=wayland --no-sandbox --unity-launch %F
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
    Exec=/usr/bin/code --enable-features=UseOzonePlatform,WebRTCPipeWireCapturer --ozone-platform=wayland --no-sandbox --new-window %F
    Icon=visual-studio-code
    ```