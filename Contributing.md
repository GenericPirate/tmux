## Source code and development process

tmux is part of the OpenBSD base system and OpenBSD CVS is the primary source
repository:

https://cvsweb.openbsd.org/cgi-bin/cvsweb/src/usr.bin/tmux/

GitHub holds the portable tmux version. There are a few minor differences,
mostly for portability.

Code changes to the main tmux code are committed to OpenBSD and then a script
automatically applies any new commits to the GitHub repository every few hours.

It is fine to develop and submit code changes with a GitHub PR, but the final
form will be a patch file which is applied to OpenBSD CVS.

## Releases

tmux currently sees a new release approximately every six months - the same
schedule as OpenBSD, around May and October. This means that the GitHub
releases contain roughly the same code as OpenBSD releases (but not necessarily
exactly the same).

The release process consists of a branch from which one or more release
candidates are produced for testing, until the final release is published and
the branch removed.

## Code contributions

Here is a list of outstanding feature requests or notes for future development.
They are sorted into three sections approximately by difficulty of
implementation. If there is a GitHub issue then its number is shown in
brackets.

It is worth getting in touch before starting work, particularly on larger
items, to avoid any duplication of effort.

### Documentation

- The getting started guide on the wiki need to be updated for 3.2 and later:

  * popups

  * using marked panes with tree mode

- The advanced use guide is unfinished and also needs these added for 3.2 and
  later:

  * %local

  * terminal-features

### Small things

- ([#2766](https://github.com/tmux/tmux/issues/2766)) A flag to select-window
  to create the window, or to new-window to select.

- ([#2601](https://github.com/tmux/tmux/issues/2601)) A way to handle duplicate
  window targets (choose MRU?). Note sure if should be default or optional.

- ([#2346](https://github.com/tmux/tmux/issues/2346)) Add a flag to
  kill-session to kill a session group, and perhaps rename-session to rename
  one.

- ([#2671](https://github.com/tmux/tmux/issues/2671)) Add a `window-size
  manual-or-smallest` which uses manual size only if there are no smaller
  clients.

- It is annoying that -t= is still needed for select-window on the status line
  when it is not needed for panes.

- "After" hooks are missing for many commands that do not use CMD_AFTERHOOK.

- A command in copy mode to toggle the selection.

- It would be nice to have some more preset layouts.

- In emacs cursor movement cancels incremental search, tmux should work the
  same way.

- ([#2205](https://github.com/tmux/tmux/issues/2205)) In copy mode, should add
  incremental search with regex (new commands).

- When queueing notifications for control mode, there is no need to queue
  session notifications for sessions other than the attached one. Similarly
  for some window notifications.

### Medium things

- ([#2484](https://github.com/tmux/tmux/issues/2797)) Highlight incoming text.

- ([#2484](https://github.com/tmux/tmux/issues/2484)) &
  ([#2776](https://github.com/tmux/tmux/issues/2776)) Popup improvements:

  - A way to remove the border for a popup.

  - A menu to do things like paste into the popup. A key to open the menu would
    let it handle key bindings also. This is complicated to do with the
    existing menu code because menus are overlays same as popups and there can
    only be one, so either need to handle the menu in the popup code (and
    forward to the menu code?) or a way to have multiple overlays.

  - `-e` flag to set environment for popup and `-c` for working directory.

  - A way to convert a popup into a pane (needs menu first I think).

  - Allow focus to be put back to pane while leaving popup open, and back to
    popup later. Problem: what if a pane is completely obscured by popup?

- A key table timeout and a key binding fired when timeout happens with the
  previous key in a format or something, would allow binding C-b 1 0 to window
  10 while keeping C-b 1 for window 1.

- ([#2537](https://github.com/tmux/tmux/issues/2537)) Copy mode should try to
  let the terminal wrap naturally if possible when redrawing so that terminal
  line copy works better. This could only happen when the terminal is the same
  width as it was when copy mode was entered. Redrawing a region would be
  difficult - have to redraw lines before in case it wraps? Maybe writing the
  copy mode screen could use grid_reader?

- ([#2495](https://github.com/tmux/tmux/issues/2495)) Per-session window
  options.

- ([#2499](https://github.com/tmux/tmux/issues/2499)) &
  ([#2512](https://github.com/tmux/tmux/issues/2512)) Add a way for
  command-prompt, confirm-before and possibly choose-tree and similar to print
  choice to stdout. Options are 1) to add flags to do it (like display-message
  -p) or 2) permit display-message -p to work when used as the choice command.

- Per line grid time tracking and use of it in copy mode and elsewhere?

- ([#2354](https://github.com/tmux/tmux/issues/2354)) Copy mode styles for the
  word and line.

- Extend the active-pane flag to windows so a client can have an independent
  current window. This can be similar to how it works for panes but is probably
  more complicated. The idea would be to get rid of session groups.

- Customize mode:
  1) way to export option or tagged options to a file;
  2) `e` key like in buffer mode to edit option value or key command in an editor popup;
  3) way to add new user options;
  4) way to add new key bindings;
  5) 'd' on a header should restore entire key table to default.

- unbind -d flag to restore key bindings to default, with -a to restore all.

- In copy mode - should the bottom be the last used line? It can be annoying to
  have to move the cursor through a load of empty space. It might be better to
  draw a line at the bottom (already have code for this) and prevent the cursor
  moving below it.

- Completion at the command prompt could be more clever: it could recognise
  commands and have some way to describe their arguments so for example only
  complete options for set-option and layouts for select-layout; it could
  complete -t with a space after it as well as without; it could complete
  special targets like {left}; it could complete panes as well as windows.
  Probably lots more things.

- Support DECSLRM margins within tmux itself.

- list-keys should be able to show long commands with {} and newlines more
  nicely. Could store knowledge of command arguments in cmd_entry like a string
  with a character per argument - so if-shell might be "sTT" for shell-command
  and two tmux command. Would need syntax to express the different forms -
  new-window, bind-key, run-shell, if-shell, display-menu. Also what about
  stuff like popup -R and detach -E?

- Should remember the last layout before select-layout was used and
  select-layout without an argument should include it, so C-Space could cycle
  through it with the preset layouts. Also a separate flag or layout name to
  restore it directly. Or how about a layout history and a way to move back? A menu?

- Should last pane be a stack like windows?

- wait-for could do more, for example being able to wait for a pane to exit or
  close (could use the existing notify code in some way). Also a flag for a
  timeout or to stop waiting on a signal.

- ([#1784](https://github.com/tmux/tmux/issues/1784)) A way to disable line
  wrap but preserve any trimmed content (so it can be viewed if the pane is
  made bigger).

- Moving, joining and otherwise reorganizing panes, windows and session should
  be easier in tree mode. This should use the marked pane rather than mixing it
  up with tagging. Maybe keys to break/join/move without leaving tree mode?
  Also dragging would be nice.

- Marked positions in history. Could use the same prompt-detection escape
  sequences as iTerm2. Could be listed by capture-pane and also a menu to jump
  to marks in copy mode. ([#1042](https://github.com/tmux/tmux/issues/1042)) is
  related and also has some code to display a marker line.

- Make the commmand prompt able to take up multiple lines.

- ([#918](https://github.com/tmux/tmux/issues/918)) A way to specify how panes
  are merged when one is killed. Could be an option to kill-pane.

- Allow multiple targets either with multiple -t or by giving a pattern or both.

- It would be nice to be able to remember the position in copy mode and go back
  to the same place when entering it again. How would this work if the pane
  scrolls? What about entering with the mouse?

- It would be nice to have commands to build a paste buffer in copy mode by
  doing multiple copies. It would need to display the work in progress
  somewhere (bottom left?) and have a command to add a chunk of text and a
  command to remove the last chunk and a command to clear.

- ([#1868](https://github.com/tmux/tmux/issues/1868)) Vertical-only zoom.

- ([#1774](https://github.com/tmux/tmux/issues/1774)) Resizing panes should
  move to the parent cell and resize it if this would allow the pane to
  become closer to what is requested.

### Large things

- Pass command arguments around as commands instead of as strings. Would need a
  custom getopt and argv/argc type (or perhaps better to parse directly into
  struct args).

- Better layouts. For example it would be good if they were driven by hints
  rather than fixed positions and could be automatically reapplied after
  resize/split/kill. Pane options can be used.

- ([#1269](https://github.com/tmux/tmux/issues/1269)) Store grids in
  blocks. Can be used to reflow on demand. Would be nice to revisit how
  history-limit works - would it be better as a global limit rather than per
  pane?

- ([#2449](https://github.com/tmux/tmux/issues/2449)) Link panes into multiple
  windows.

- ([#44](https://github.com/tmux/tmux/issues/44)) &
  ([#1613](https://github.com/tmux/tmux/issues/1613)) Support for SIXEL.
