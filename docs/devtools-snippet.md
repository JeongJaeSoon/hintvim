# DevTools snippet

The same hint mode, run from Claude Desktop's DevTools instead of installed. It labels the web page inside the window, not the sidebar or title bar the [app](../README.md) reaches, and it adds a settings and help panel. It needs no permissions.

A DevTools snippet **persists across app restarts**: you save it once and run it in three keystrokes afterwards.

**One-time setup**

1. In Claude Desktop, press `Cmd+Alt+I` to open DevTools
2. Go to **Sources → Snippets → New snippet**
3. Paste the contents of [`src/hintvim.js`](../src/hintvim.js) and name it `hintvim`
4. Press `Cmd+Enter` to run it

**After each app restart**

`Cmd+Alt+I` → Snippets → `Cmd+Enter`.

The script is re-runnable: running it again tears the previous instance down first, so you can never end up with duplicate listeners.

## Keys

| key | action |
|---|---|
| `Ctrl+;` | enter / leave hint mode (configurable) |
| `a s f g q w e r t z x c v` | type a hint label |
| `h` `j` `k` `l` | scroll left / down / up / right |
| `↑` `↓` `←` `→` | same |
| `d` / `u` | half page down / up |
| `Ctrl+d` / `Ctrl+u` | same |
| `Home` / `End` | top / bottom |
| `Backspace` | undo one label character — or leave, if nothing is typed |
| `,` | settings |
| `?` | help, showing your actual bindings |
| `Esc` | leave |

No key ever carries two meanings — `h j k l d u , ?` are reserved for navigation and commands, and the hint alphabet can't contain any of them. That's enforced by validation, not convention: the settings panel refuses any alphabet containing a reserved key.

## Settings

Press `,` while hints are showing. You can change the leader key, the hint alphabet, and the scroll step. Settings persist in `localStorage`.

Two rules the settings panel enforces, both of which exist to keep you from locking yourself out:

- **The leader key needs a modifier** (Ctrl, Alt, or Cmd). A bare letter would fire hint mode every time you typed it, and the settings panel is only reachable *through* hint mode — you would have no way back except clearing `localStorage` by hand. Shift does not count: `Shift+a` is just a capital A to anyone typing.
- **The hint alphabet cannot contain a reserved key** (`h j k l d u , ?`). Those are consumed before label matching, so an element labeled with one could never be clicked.

## Non-latin keyboards

The snippet works with a Korean or Japanese input source on. Labels match the letter a key types, so Dvorak and AZERTY work as printed; when that letter isn't latin, the snippet reads the key's US position instead. Hint mode moves the focus away from the prompt box while labels show, so the input method can't turn hint keys into composed text, and gives it back when you leave.

## Why the snippet has no installer

The window renders remote `claude.ai`, so there is no file on disk that the page loads and a `<script>` tag could go into. Claude Desktop does ship a local copy of the web app, but loads it only in a third-party deployment mode, which would also stop your claude.ai account from working.

Injecting from outside the app is closed too. The app is signed with Hardened Runtime and refuses debuggers and injected libraries, and it quits when started with remote-debugging flags. Patching its bundle to add a preload script breaks its code signature and has to be redone after every update.

The [app](../README.md#how-it-works) works from outside the page, through the Accessibility API, instead of injecting into it.
