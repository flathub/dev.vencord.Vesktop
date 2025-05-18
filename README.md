# Vesktop Flatpak

<!-- This flatpak is loosely based on xyz.armcord.ArmCord @ https://github.com/flathub/xyz.armcord.ArmCord -->

This is the flatpak for [Vesktop](https://github.com/Vencord/Vesktop).

## Wayland

Vesktop will run through X11 / XWayland by default, as this is the most compatible option.
Everything should work out of the box, including screen sharing and hardware acceleration.

If you wish to run it natively on Wayland instead, you can do so by removing the `--socket=x11` permission with [Flatseal](https://flathub.org/apps/com.github.tchx84.Flatseal) or by running the following command:

```sh
flatpak override --nosocket=x11 dev.vencord.Vesktop
```

## File access

Due to the Flatpak sandbox, Vesktop only has access to a very limited set of files, which messes with file Drag & Drop and Copy Paste.

As a workaround, you can either use solely the built-in file picker, or you can give Vesktop
access to your home directory (& other desired directories) using [Flatseal](https://flathub.org/apps/com.github.tchx84.Flatseal) or by running the following command:

```sh
flatpak override --filesystem=home dev.vencord.Vesktop
```

## Tray icons

To get a working Tray Icon on GNOME, install the [appindicator-support](https://extensions.gnome.org/extension/615/appindicator-support/) extension.

## Discord Rich Presence

Game Activity on the flatpak is very limited, as the sandbox does not allow Vesktop to scan running processes.
This means that Rich Presence will only work for games that explicitly support it.

Follow the instructions below to enable Rich Presence for such applications.

### Native applications
A solution that works short-term is to run `ln -sf $XDG_RUNTIME_DIR/{.flatpak/dev.vencord.Vesktop/xdg-run,}/discord-ipc-0`.
For something longer lasting, run the following:

```sh
mkdir -p ~/.config/user-tmpfiles.d
echo 'L %t/discord-ipc-0 - - - - .flatpak/dev.vencord.Vesktop/xdg-run/discord-ipc-0' > ~/.config/user-tmpfiles.d/discord-rpc.conf
systemctl --user enable --now systemd-tmpfiles-setup.service
```
Now, native applications will be able to use Rich Presence on every system start.

### Flatpak applications
<!-- TAKEN FROM https://github.com/flathub/com.discordapp.Discord/wiki/Rich-Precense-(discord-rpc) -->

Flatpak applications need certain changes inside of the flatpak environment to connect properly:

1. Permission to access `$XDG_RUNTIME_DIR/.flatpak/dev.vencord.Vesktop/`
2. A symlink at `$XDG_RUNTIME_DIR/discord-ipc-0` pointing to `$XDG_RUNTIME_DIR/.flatpak/dev.vencord.Vesktop/xdg-run/discord-ipc-0`

Suggested changes to accomplish these needs :

1. Add `--filesystem=xdg-run/.flatpak/dev.vencord.Vesktop:create` and `--filesystem=xdg-run/discord-ipc-0` to the global Flatpak permissions
2. Restart