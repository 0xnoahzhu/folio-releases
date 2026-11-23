# Folio

A Mac app for keeping your personal accounts in plain-text
[Beancount](https://beancount.github.io/) files, with Filio, an AI bookkeeper,
doing the busywork.

![The Folio dashboard](docs/screenshots/dashboard.png)

## Features

- **Tell Filio, or drop in a statement.** Type an expense, or add a bank
  export, PDF or receipt photo, and Filio drafts the entries.
- **Review before anything is written.** Every change is checked with
  `bean-check` and saved as a git commit you can undo.
- **Learns how you book things.** Categories come from your history and your
  choices, so Filio only asks about what's new.
- **Fix many entries at once.** One answer, such as "all MeiTuan orders are
  delivery", updates every matching entry.
- **Remembers what you tell it.** Accounts, people and preferences, per ledger,
  editable in Settings.
- **A whole month at once.** Add many statements together; Folio reads them in
  the background.
- **Private by design.** Your ledger stays in your folder and your git remote,
  optionally encrypted. Names and numbers are hidden before anything reaches a
  model.
- **Any model.** Anthropic, OpenAI, Azure OpenAI, or any OpenAI-compatible
  server, including one on your Mac.
- **Also:** dashboards, transactions, accounts, saved queries, voice input, a
  PIN lock, built-in [Fava](https://beancount.github.io/fava/), and automatic
  updates.

| Chat with Filio | Review every change |
|---|---|
| ![Filio drafting an entry next to the Accounts view](docs/screenshots/chat.png) | ![A change set in Review, showing the diff](docs/screenshots/review.png) |
| **Transactions by month** | **Light and dark** |
| ![Transactions for November](docs/screenshots/transactions.png) | ![The dashboard in dark mode](docs/screenshots/dashboard-dark.png) |

All data in these screenshots is made up.

## Download

For Macs with Apple silicon, macOS 13 or later. Get it from
[Releases](https://github.com/0xnoahzhu/folio-releases/releases).

## Uninstall

Removing Folio never touches your ledgers. Quit Folio (⌘Q), drag it from
Applications to the Trash, then paste this into Terminal to remove everything
it keeps on your Mac, including your API keys and settings, and the `.folio`
folder (Filio's rules) in each ledger Folio opened:

```sh
pkill -f "/Folio.app/Contents/Resources/"
[ -f ~/Library/"Application Support"/Folio/folio.db ] && sqlite3 ~/Library/"Application Support"/Folio/folio.db "SELECT path FROM ledgers;" | while IFS= read -r L; do [ -n "$L" ] && [ -d "$L/.folio" ] && rm -rf "$L/.folio" && echo "Removed $L/.folio"; done
rm -rf ~/Library/"Application Support"/Folio ~/Library/Logs/Folio
rm -rf ~/Library/Caches/folio-ocr ~/Library/Caches/folio-updater
rm -rf ~/Library/Caches/app.folio.desktop ~/Library/Caches/app.folio.desktop.ShipIt
rm -rf ~/Library/Caches/app.folio.desktop.names ~/Library/Caches/app.folio.desktop.speech ~/Library/Caches/app.folio.desktop.auth
rm -rf ~/Library/HTTPStorages/app.folio.desktop
rm -rf ~/Library/"Saved Application State"/app.folio.desktop.savedState
C="$(getconf DARWIN_USER_CACHE_DIR)" && [ -n "$C" ] && rm -rf "${C}app.folio.desktop" "${C}app.folio.desktop.helper" "${C}app.folio.desktop.helper.GPU" "${C}app.folio.desktop.helper.Renderer" "${C}app.folio.desktop.helper.Plugin"
[ -n "$TMPDIR" ] && find "$TMPDIR" -maxdepth 1 -name 'folio-askpass-*' -exec rm -rf {} +
find ~/Library/Logs/DiagnosticReports -maxdepth 1 \( -name 'Folio-20*' -o -name 'Folio Helper*-20*' -o -name 'folio-ocr-20*' -o -name 'folio-names-20*' -o -name 'folio-speech-20*' -o -name 'folio-auth-20*' \) -delete
defaults delete app.folio.desktop 2>/dev/null
rm -f ~/Library/Preferences/app.folio.desktop.plist
tccutil reset All app.folio.desktop
```

In a ledger kept in git, commit the removed `.folio` folder. If a ledger is
encrypted with git-crypt, keep its exported key file. What each
line removes, and how to keep using an encrypted ledger without Folio:
[Uninstall Folio](docs/uninstall.md).

## More

- [Move Folio to another Mac](docs/move-to-another-mac.md)
- [License and open-source software](docs/license.md)

© 2026 Noah Zhu. Free to download and use.
