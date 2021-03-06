# Shortcut-Launcher

An [AutoHotkey](https://www.autohotkey.com/) script for Microsoft Windows to
show a GUI that lets you incremental search and open shortcuts.

The following shortcuts are included:

- shortcuts (\*.lnk) stored in your shortcuts folder and its subfolders
- web page links (\*.url) stored in your shortcuts folder and its subfolders
- the recent files/folders automatically stored in `%AppData%\Microsoft\Windows\Recent\`

## Contents

- [Profile](#profile)
- [Incremental search](#incremental-search)
- [Text used for entries](#text-used-for-entries)
- [Key bindings](#key-bindings)
- [Compiled version](#compiled-version)
- [TODO](#todo)
- [Changelog](#changelog)

## Profile

You have to choose a profile by placing a shorcut to a profile folder in the
shortcut-launcher script directory (the folder this file is in). It will
assume that the first file found with a `.lnk` extensions points to the
profile folder. If the shortcut found is named `default-profile.lnk` it will
scan for other `.lnk` files and if it finds any use that profile instead of
the default one. This is needed to make sure you can pull updates. The
`default-profile.lnk` should not be modified.

By default there is a link `default-profile.lnk` that points to the default
profile in `%userprofile%\desktop\shortcut-launcher\profiles\default`. This
link only works without modification if you clone this repository to your
desktop. If you have cloned it somewhere else please update the shortcut.

A profile consist of two files:

- Profile options in `config.ini`.
- A link to the shortcuts folder called `shortcuts.lnk`.

Just copy the default profile somewhere else to make a new profile and change
the link in the script directory to that folder. It is **not recommended** to
place your profile inside the repository.

### Profile Options

You can configure the follow options in `config.ini` inside the profile:

- `Name` (str) The name of the profile.
- `ShowRecent` (True/False) If true (default) recent files/folders are shown.
- `DeleteDuplicatesInRecent` (True/False) If true (default) duplicates found
  in recent files/folders are deleted.
- `DeleteNonExistingInRecent` (True/False) If true non-existing files/folders
  in recent files/folders are deleted.

### Profile Link to Shortcuts Folders

Add one or multiple shortcuts in the profile pointing to a folder that
holds shortcuts. The shortcut in the default profile points to
`%userprofile%\desktop\shortcuts` (folder called shortcuts on the current
user desktop).

So if you want to use the default profile add the `shortcuts` folder on your
desktop and start adding shortcuts to it.

### Profile Example

This is the default profile.

```
├── default-profile.lnk
└── profiles
    └── default
        └── config.ini
        └── shortcuts.lnk
```

- `default-profile.lnk` is a shortcut with target `%userprofile%\desktop\shortcut-launcher\profiles\default`.
- `config.ini`
  ```ini
  [Config]
  Name=default
  ShowRecent=True
  DeleteDuplicatesInRecent=True
  DeleteNonExistingInRecent=True
  ```
- `shorcuts.lnk` is a shortcut to `%userprofile%\desktop\shortcuts` folder.

## Incremental search

As soon as you enter any key the list is filtered accordingly.

A space in the search string is a wildcard for matching any character 0 or
more times. Example: `auto k or um` matches `autohotkey forum`

If the search string contains only lowercase letters the search is performed
case insensitive. If the search string contains any uppercase letter the
search is done case sensitive.

It combines the text from the 1st column (folder and shortcut name) & 2nd
column (target) for the search. Matches in the 1st column have a higher
priority and are placed at the top of the list view.

## Text used for entries

The text for each entry in the list view consists of two columns:

- **Path and file base name** - The directory where the shortcut is placed and the name of the shortcut. Path separators are replaced with `>`.
- **Target** - The target of the shortcut. This can be anything you can link to (file, folder, appliation, url).

Example:  
A shortcut named `forum` (forum.lnk) filed in your folder in the subfolder `coding/ahk` which has the target `https://www.autohotkey.com/boards/` will be listed as:

```text
| coding > ahk > forum | https://www.autohotkey.com/boards/ |
```

### Special cases

- **Shortcuts with browser profile** - Shortcuts to open an url with a specific browser profile are recognized and transformed.

  Example:  
  If the target of the shortcut is `%ProgramFiles(x86)%\Google\Chrome\Application\chrome.exe --profile-directory="Default" https://www.google.com` the target text is transformed into `(chrome) https://www.google.com`

The profile name is not yet shown, see TODOs.

## Key bindings

For convenience there are multiple bindings per command:

Changing selected shortcut (entry):

| Key                                  | Description            |
| :----------------------------------- | :--------------------- |
| <kbd>Ctrl-n</kbd> or <kbd>Down</kbd> | Select next entry      |
| <kbd>Ctrl-p</kbd> or <kbd>Up</kbd>   | Select previous entry  |
| <kbd>Ctrl-f</kbd>                    | Scroll down 20 entries |
| <kbd>Ctrl-b</kbd>                    | Scroll up 20 entries   |

Changing the search string:

| Key                                                  | Description                      |
| :--------------------------------------------------- | :------------------------------- |
| <kbd>Ctrl-u</kbd>                                    | Clear the input field            |
| <kbd>Alt-a</kbd>                                     | Select all text in input field   |
| <kbd>Ctrl-a</kbd>                                    | Move to beginning of input field |
| <kbd>Ctrl-e</kbd>                                    | Move to end of input field       |
| <kbd>Backspace</kbd> or <kbd>Ctrl-h</kbd>            | Backspace                        |
| <kbd>Ctrl-Backspace</kbd> or <kbd>Ctrl-Shift-h</kbd> | Delete last word                 |
| <kbd>Alt-d</kbd>                                     | Delete next word                 |

Open the selected shortcut:

| Key                                   | Description                                |
| :------------------------------------ | :----------------------------------------- |
| <kbd>Ctrl-m</kbd> or <kbd>Enter</kbd> | Open the currently selected shortcut       |
| <kbd>Alt-o</kbd>                      | If it is a file open its directory instead |

App:

| Key                                 | Description                         |
| :---------------------------------- | :---------------------------------- |
| <kbd>Ctrl-i</kbd>                   | Focus input field                   |
| <kbd>Ctrl-r</kbd>                   | Toggle showing recent files/folders |
| <kbd>Ctrl-g</kbd> or <kbd>Esc</kbd> | Minimize                            |
| <kbd>Ctrl-x</kbd>                   | Exit (kill the app)                 |

Remark: <kbd>Ctrl-h</kbd> seems to work by default as Backspace in the edit box.

## Compiled version

The compiled script (.exe, 64-bit) is included in this repository. You can compile it
yourself: Use the compiler provided by ahk. Press the win key and then search
for convert .ahk to .exe

## TODO

- [ ] Feature: Sort the shortcuts (exclude the recent files as they should be show in order).  
      This is needed now when using multiple shortcut folders.
- [ ] Feature: Use a hydra for the action bound to <kbd>Ctrl-o</kbd>.
- [ ] Feature: Snap windows to half screen or full screen after running a shortcut.  
      Experimental implementation bound to <kbd>Ctrl-q</kbd>.
- [ ] Style: Change subroutines into functions and clean the script. Or
      rather use a class to create a namespace. See the hydra code.
- [ ] Feature: Add prefixes like U for url, P for path/folder, F for file.
- [ ] Feature: Extract the profile if one is used for an url. Example:
      `%ProgramFiles(x86)%\Google\Chrome\Application\chrome.exe --profile-directory="Default" https://www.tenforums.com`
      The profiles in Firefox are not named, only numbered or so.
- [ ] Feature: Add a possibility to ignore a path in recent.
- [ ] Feature: Add Workspace feature to links. In the target description (is
      usually not used) one can add a JSON or so to configure how the shorcut is
      opened. Fullscreen ...  
      This should be bound to a separate key. The profile name is now no
      problem anymore as this is already implemented.

## Changelog

### No stable release yet

- 2020-09-16 Added focus input (edit box) feature (Ctrl-i)
- 2020-09-07 Added feature to have multiple shortcut folders.
- 2020-09-07 Added mechanism to have profiles.
- 2020-09-03 Automatically remove non-existing targets and duplicates in recent. There are two variables at the top to turn this off if you don't want this (`DeleteDuplicatesInRecent`, `DeleteNonExistingInRecent`). It's turned on in the compiled version.
- 2020-08-18 Added open folder hotkey for files with <kbd>M-o</kbd>
- 2020-06-24 Stop flickering on updating the list view  
  Added +LV0x10000 to the list view to avoid flickering when the list view is updated.  
  https://autohotkey.com/board/topic/89323-listview-and-flashing-on-redrawing-or-editing/
