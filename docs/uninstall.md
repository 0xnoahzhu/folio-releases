# Uninstall Folio

Removing Folio does not touch your ledgers: the folders, their git history and
any remote (for example on GitHub) stay exactly as they are.

## 1. Before you remove the app

- Turn off **Open Folio in the background at login** (Settings →
  Notifications) if it is on.
- If a ledger is encrypted (git-crypt), make sure you still have its key file,
  by default `~/Documents/folio-git-crypt.key` (or `~/Documents/folio-ledger.key`).
  It is yours: Folio never deletes it, and neither does this page. Without it an
  encrypted remote cannot be read.
- Clear Folio's notifications in Notification Center: with **Show amounts and
  payees** on, they can show amounts and accounts, and macOS keeps them.
- Quit Folio (⌘Q).

Then, while Folio.app is still in Applications, clear the permissions you gave
it (microphone, folders) and stop anything it left running:

```sh
tccutil reset All app.folio.desktop
pkill -f "/Folio.app/Contents/Resources/"
```

## 2. Remove the app

Drag **Folio** from Applications to the Trash, and out of the Dock if you kept
it there. The bundled tools (Python, Beancount, Fava, git, git-crypt) and
Folio's helpers are inside the app and go with it.

## 3. Remove what Folio keeps on your Mac

| Location | What it holds | When |
|---|---|---|
| `~/Library/Application Support/Folio` | **Settings, API keys, sync tokens, saved PDF passwords**, the PIN (hashed), chats, Inbox documents, masking maps, the model call log, browser caches | Always |
| `~/Library/Logs/Folio` | App logs, one file per hour | Always |
| `~/Library/Preferences/app.folio.desktop.plist` | Open and Save panel settings | Always |
| `~/Library/Caches/folio-ocr` | Text recognition cache | After reading an image or scanned PDF |
| `~/Library/Caches/folio-updater` | The last downloaded update | After an update |
| `~/Library/Caches/app.folio.desktop.ShipIt` | The update installer's state and logs | After an update |
| `~/Library/Preferences/ByHost/app.folio.desktop.ShipIt.*.plist` | The update installer's settings | After an update |
| `~/Library/Caches/app.folio.desktop`, `~/Library/HTTPStorages/app.folio.desktop` | The update installer's download cache | After an update |
| `~/Library/Caches/app.folio.desktop.names`, `~/Library/Caches/app.folio.desktop.speech`, `~/Library/Caches/app.folio.desktop.auth` | Helper caches | Only if macOS made them |
| `~/Library/Saved Application State/app.folio.desktop.savedState` | Window restoration | Only if macOS made it |
| `app.folio.desktop*` in the hidden cache folder (`getconf DARWIN_USER_CACHE_DIR`) | Graphics shader caches, a few MB | Always |
| `app.folio.desktop.ShipIt.*` and `folio-askpass-*` in `$TMPDIR` | An unpacked update (about 500 MB), the sync password socket | After an update, or a crash; macOS clears them in time |
| `~/Library/Logs/DiagnosticReports` (`Folio…`, `folio-…`) | Crash and hang reports | Only after a crash |

**In Terminal** (deletes permanently; paths that aren't there are skipped):

```sh
C="$(getconf DARWIN_USER_CACHE_DIR)"
rm -rf ~/Library/Logs/Folio ~/Library/Caches/{folio-ocr,folio-updater} ~/Library/Caches/app.folio.desktop{,.ShipIt,.names,.speech,.auth} ~/Library/HTTPStorages/app.folio.desktop ~/Library/"Saved Application State"/app.folio.desktop.savedState
[ -n "$C" ] && rm -rf "${C%/}"/app.folio.desktop{,.helper,.helper.GPU,.helper.Renderer,.helper.Plugin}
find "$TMPDIR" -maxdepth 1 \( -name 'folio-askpass-*' -o -name 'app.folio.desktop.ShipIt.*' \) -exec rm -rf {} + 2>/dev/null
find ~/Library/Logs/DiagnosticReports -maxdepth 2 \( -name 'Folio[-_ ]*' -o -name 'folio-[a-z]*-20*' \) -delete 2>/dev/null
defaults delete app.folio.desktop 2>/dev/null; rm -f ~/Library/Preferences/app.folio.desktop.plist
defaults -currentHost delete app.folio.desktop.ShipIt 2>/dev/null; find ~/Library/Preferences/ByHost -maxdepth 1 -name 'app.folio.desktop.ShipIt.*.plist' -delete 2>/dev/null
```

Then remove your settings and keys too, unless you'll reinstall Folio on this
Mac and want to keep them:

```sh
D=~/Library/"Application Support"/Folio; [ -f "$D/folio.db" ] && rm -rf "$D"
```

**Or in Finder:** Go → Go to Folder… (⇧⌘G), paste each `~/Library` path from
the table and move it to the Trash (in `ByHost` and `DiagnosticReports`, only
the files starting with `app.folio.desktop`, `Folio` or `folio-`). The hidden
cache and `$TMPDIR` items can stay; macOS clears them.

## 4. System Settings (optional)

- Remove Folio from General → Login Items if it is still listed.
- If you skipped `tccutil`, turn Folio off in Privacy & Security → Microphone,
  Files and Folders, and Local Network.
- Folio's entry under Notifications may stay; it holds no data.

## 5. Inside your ledgers

These are part of your ledgers and are kept:

- **`.folio`** (hidden), with your rules for that ledger (`rules.md`) and
  `config.json` if you made one. If you want it gone too, delete it in Finder
  (⇧⌘. shows hidden files) and, in a git ledger, commit that. To list the
  ledgers Folio opened, run this before removing your settings:

  ```sh
  sqlite3 -init /dev/null -readonly -noheader -list ~/Library/"Application Support"/Folio/folio.db "SELECT path FROM ledgers"
  ```

- **`documents`**, the statements and receipts you saved; your entries link to
  them.
- **`.gitignore`**, and **`.gitattributes`** in an encrypted ledger (it tells
  git-crypt what to encrypt).
- A hidden `.<file>.folio-….tmp` file is left only if Folio quit while
  applying changes. It can be the original of a file being replaced: check it
  before you delete it.

### Encrypted ledgers

Folio 1.0.0 and later tell git to use the `git-crypt` installed on your Mac,
so once Folio is gone, git needs it:

```sh
brew install git-crypt
```

A git app that still can't find it needs Homebrew's `bin` folder on its PATH.
If a ledger was last opened by a Folio older than 1.0.0, git fails with
`error: external filter '"…/Folio.app/…/git-crypt" clean' failed`; then run, in
that ledger:

```sh
git config filter.git-crypt.smudge '"git-crypt" smudge'
git config filter.git-crypt.clean '"git-crypt" clean'
git config diff.git-crypt.textconv '"git-crypt" diff'
```

## Nothing else

Folio never uses the Keychain, never writes to `~/.gitconfig`, `~/.config` or
`~/.ssh`, and leaves nothing in `/Library`, `~/Library/Containers` or
`~/Library/LaunchAgents`. The bundled tools write nothing outside Folio's
folders and your ledgers.
