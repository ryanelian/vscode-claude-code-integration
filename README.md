# vscode-claude-code-integration

Keyboard bindings that make **Claude Code feel native in VS Code on macOS**, for people whose
fingers came from Visual Studio, Cursor, or GitHub Copilot Chat.

The whole thing is one file: [`keybindings.json`](./keybindings.json). It is heavily commented, so
the file itself is the real documentation. This README covers what it does and how to install it.

## The problem it solves

`cmd+i` is muscle memory. VS Code's built-in chat (Copilot) and Cursor both use it to pull the
current file or the selected code into the AI conversation. After enough hours, the fingers
already mean *"send this code to the assistant"* when they hit that key.

Claude Code is genuinely awkward to use when `cmd+i` does something else: the reflex fires, the
wrong panel opens, and the selection is lost. So this config makes the trade explicit. Claude Code
gets `cmd+i`, and every built-in feature competing for the key gives it up.

A second, smaller theme: a few Windows and Visual Studio habits are reproduced on purpose, because
macOS and VS Code both default elsewhere. Where the platform default is harmless it is kept, so
nothing has to be unlearned to sit at someone else's machine.

## Requirements

- macOS
- VS Code
- The [**Claude Code** extension](https://marketplace.visualstudio.com/items?itemName=anthropic.claude-code)
  (`anthropic.claude-code`), which provides every `claude-vscode.*` command referenced below.
  From a terminal: `code --install-extension anthropic.claude-code`
- **Spotlight's `cmd+space` shortcut disabled or remapped**, if you want the IntelliSense binding.
  See [Gotchas](#gotchas).

## Install

### 1. From the CLI

Backs up whatever is there now, then overwrites it:

```sh
DEST="$HOME/Library/Application Support/Code/User/keybindings.json"
cp "$DEST" "$DEST.bak" 2>/dev/null
curl -fsSL https://github.com/ryanelian/vscode-claude-code-integration/raw/refs/heads/main/keybindings.json -o "$DEST"
```

The `-f` matters: without it, curl happily writes an HTML error page into your keybindings on a
bad URL or a network hiccup. If something goes wrong, `mv "$DEST.bak" "$DEST"` puts it back.

VS Code picks the file up on write, so there is nothing to reload.

### 2. Via the Command Palette

1. Open the Command Palette with **`cmd+shift+p`**.
   (If you prefer `cmd+p`, type a leading `>` to switch it into command mode.)
2. Run **`Preferences: Open Keyboard Shortcuts (JSON)`**.
   Note the **(JSON)** part. The entry without it opens the graphical editor, which is not what
   you want here.
3. Replace the contents of the file that opens with all of [`keybindings.json`](./keybindings.json).
4. Save. VS Code applies keybindings on save, so there is no need to reload the window.

## What you get

### Claude Code

| Key | Does |
| --- | --- |
| `cmd+i` | With a selection, sends `@src/foo.ts#L10-L20` to Claude's input. With no selection, just opens or focuses Claude. |
| `cmd+shift+i` | Opens a **fresh** Claude Code session as a tab in the current editor group. |

### Visual Studio and Windows reflexes

| Key | Does | Native key that still works |
| --- | --- | --- |
| `cmd+y` | Redo | `shift+cmd+z` |
| `cmd+space` | Trigger Suggest (IntelliSense) | `option+escape`, and `ctrl+space` once macOS lets go of it |
| `ctrl+k ctrl+d` | Format Document | `shift+option+f` |

## What it takes away

Deliberate losses, so none of these surprise you later:

- **Built-in VS Code chat has no keyboard shortcut at all.** The config removes
  `workbench.action.chat.open` from `ctrl+cmd+i` (its real macOS default) and
  `workbench.action.chat.openAgent` from `cmd+shift+i`. Delete those two entries to get them back.
- **Inline Chat, Ask in Chat, notebook cell chat, and the Settings editor's AI search** all lose
  `cmd+i`.
- **`cmd+i` no longer triggers IntelliSense.** It is an undocumented secondary binding for Trigger
  Suggest on macOS, which surprises most people. `cmd+space` and `option+escape` cover it.
- **`ctrl+k` alone stops being Delete All Right** in editors that have a formatter, because
  `ctrl+k ctrl+d` turns it into a chord prefix. `cmd+fn+delete` still runs that command, and the
  kill still works in the terminal and other inputs.
- **The `@`-mention command loses its own keys** (`alt+k` and the legacy `cmd+alt+k`), since
  `cmd+i` is the single way in.

## Gotchas

**`cmd+space` will not work until Spotlight gives it up.** The OS wins before VS Code ever sees
the key. Free it under *System Settings > Keyboard > Keyboard Shortcuts > Spotlight*, or skip that
binding and use `option+escape`, which needs no system changes at all.

### Why not just use `ctrl+space`? 

Fair question, and it really is bound out of the box:
Trigger Suggest registers `mac: { primary: ctrl|Space }`, making `ctrl+space`
the macOS primary. No entry in this file is needed for it.

The catch is that macOS claims the same key. Per Apple, `Control+Space` selects the **previous
input source**, the language switcher, with `Control+Option+Space` for the next one. Both live
under *System Settings > Keyboard > Keyboard Shortcuts > Input Sources* as **"Select the previous
input source"**.

**You may need to log out or restart for it to take effect.**
Testing it immediately after unchecking may look like the change did nothing.
If a restart still does not free it, the reported fix is *Input Sources > Restore Defaults*,
then reboot, after which the key works whether or not the box is ticked.

Worth knowing before you spend time on it: this is a long-standing mess. There is a [VS Code
issue](https://github.com/microsoft/vscode/issues/198504) where `ctrl+space` reaches the OS fine,
registering in System Settings' own shortcut recorder, yet never arrives in VS Code.
In that case, no local config will help you.

## Reverting

Keybindings are a single file with no other state. To undo everything, empty it:

```json
// Place your key bindings in this file to override the defaults
[]
```

Save, and every VS Code default is back.

## License

[Apache License 2.0](./LICENSE).
