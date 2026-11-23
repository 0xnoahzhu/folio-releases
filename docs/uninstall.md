# Uninstall Folio

Removing Folio does not touch your ledgers. Your ledger folders, their git
history and any git remotes, for example a repository on GitHub, stay
exactly as they are.

## 1. Before you quit

- If **Open Folio in the background at login** is on (Settings → Notifications),
  turn it off.
- If a ledger uses encryption (git-crypt), make sure you still have its
  exported key file (Folio suggests `~/Documents/folio-git-crypt.key`). It is
  your file: Folio never deletes it, and this page does not either. Without it
  an encrypted remote cannot be read. See also
  [encrypted ledgers](#encrypted-ledgers) below.

Then quit Folio (⌘Q).

If Folio ever quit unexpectedly, a Fava server it started may still be
running. Restarting the Mac stops it, or in Terminal:


## 2. Remove the app

Drag **Folio** from Applications to the Trash. The bundled tools (Python,
Beancount, beanquery, beanprice, Fava, git, git-crypt) and Folio's helpers
live inside Folio.app and go with it.

## 3. Remove what Folio keeps on your Mac

| Location | What it holds | When it is there |
|---|---|---|
| `~/Library/Application Support/Folio` | Folio's database `folio.db`, and the copies made before an update (`folio.db.backup-…`, the newest two): settings, model profiles, **API keys, sync tokens and remembered PDF passwords**, the lock PIN (as a hash), chats, Inbox documents (imported statements and receipts), masking maps, recent ledgers. Also the model call log (`logs/calls`), the sync password helper (`askpass`), the record of a running Fava server (`fava-process.json`), and the browser engine's storage and caches | Always |
| `~/Library/Logs/Folio` | App logs, one file per day | Always |
| `~/Library/Preferences/app.folio.desktop.plist` | macOS preferences (Open and Save panel size and last folder) | Always |
| `~/Library/Caches/folio-ocr` | Text recognition cache macOS makes for Folio's reader | After Folio reads an image or a scanned PDF |
| `~/Library/Caches/folio-updater` | An update Folio downloaded and has not installed yet | While an update waits |
| `~/Library/Caches/app.folio.desktop.ShipIt` | The update installer's state and logs | After an update |
| `~/Library/Caches/app.folio.desktop`, `~/Library/HTTPStorages/app.folio.desktop` | Download cache and network storage macOS keeps for the update installer | After an update, if macOS made them |
| `~/Library/Caches/app.folio.desktop.names`, `.speech`, `.auth` | Caches macOS may make for Folio's name finder, voice input and "Forgot PIN?" helpers | Only if macOS made one |
| `~/Library/Saved Application State/app.folio.desktop.savedState` | Window restoration | Only if macOS made it |
| `app.folio.desktop` and `app.folio.desktop.helper…` in macOS's hidden per-user cache folder (`getconf DARWIN_USER_CACHE_DIR`) | Compiled graphics shaders, a few MB | Always |
| `folio-askpass-…` in your temporary folder (`$TMPDIR`) | The sync password helper's socket, used instead of the `askpass` folder above when your user folder's path is very long | While Folio runs; left only if Folio quit unexpectedly |
| `~/Library/Logs/DiagnosticReports` (`Folio-…`, `Folio Helper…`, `folio-ocr-…` and the other helpers) | Crash reports | Only if something crashed |

The first row holds your API keys and tokens. Remove it unless you will
reinstall Folio on this Mac and want to keep your settings.

**In Terminal** (deletes permanently; paste as one block; a path that is not
there is skipped):

```sh
pkill -f "/Folio.app/Contents/Resources/"
D=~/Library/"Application Support"/Folio; C="$(getconf DARWIN_USER_CACHE_DIR)"
[ -f "$D/folio.db" ] && sqlite3 "$D/folio.db" "SELECT path FROM ledgers" | while IFS= read -r L; do [ -n "$L" ] && rm -rf "$L/.folio"; done
rm -rf "$D" ~/Library/Logs/Folio ~/Library/Caches/{folio-ocr,folio-updater} ~/Library/Caches/app.folio.desktop{,.ShipIt,.names,.speech,.auth} ~/Library/HTTPStorages/app.folio.desktop ~/Library/"Saved Application State"/app.folio.desktop.savedState
[ -n "$C" ] && rm -rf "$C"app.folio.desktop{,.helper,.helper.GPU,.helper.Renderer,.helper.Plugin}
find "$TMPDIR" -maxdepth 1 -name 'folio-askpass-*' -exec rm -rf {} + 2>/dev/null
find ~/Library/Logs/DiagnosticReports -maxdepth 1 \( -name 'Folio-20*' -o -name 'Folio Helper*' -o -name 'folio-*-20*' \) -delete 2>/dev/null
defaults delete app.folio.desktop 2>/dev/null; rm -f ~/Library/Preferences/app.folio.desktop.plist; tccutil reset All app.folio.desktop
```

**Or in Finder:** choose Go → Go to Folder… (⇧⌘G), paste each `~/Library`
path from the table, and move what you find to the Trash; in
`~/Library/Logs/DiagnosticReports`, only the files whose names start with
`Folio` or `folio-`. Then empty the Trash. The graphics caches and the
temporary folder are hidden from Finder; leave them, or use the Terminal block.

## 4. Permissions and login items (optional)

macOS keeps the permissions you gave Folio: the microphone, and access to
folders such as Documents, Desktop, Downloads, iCloud Drive or a network or
external drive if your ledgers are there. To clear them:

```sh
tccutil reset All app.folio.desktop
```

- If Folio still appears in System Settings → General → Login Items (Login
  Items & Extensions on macOS 15 and later), remove it there.
- If you used a model server on another device and Folio is listed in
  System Settings → Privacy & Security → Local Network, turn it off there.
- Notification settings hold no data; macOS manages them.

## 5. Inside your ledgers

- **`.folio`**: a folder in the top of each ledger that holds Filio's rules for
  that ledger (`rules.md`, and `config.json` if you made one). The commands
  above remove it from every ledger Folio opened; in a ledger kept in git,
  commit that.

These are part of your ledger and are kept:

- **Saved documents**: statements and receipts you saved into the ledger, in
  its `documents` folder (or the folder named by `option "documents"`). Keep
  them: your entries link to them.
- **`.gitignore`** with a `.DS_Store` line, if Folio turned the folder into a
  git repository.

### Encrypted ledgers

When Folio set up encryption or unlocked a ledger, git-crypt stored the key in
that ledger's `.git/git-crypt` folder and told git to run the git-crypt inside
Folio.app. Once Folio is removed, git commands in that folder fail with
`error: external filter '"…/Folio.app/…/git-crypt" clean' failed`. To keep
using the ledger with git outside Folio,
install git-crypt (for example `brew install git-crypt`) and point git at it:

```sh
cd /path/to/your/ledger
git config filter.git-crypt.smudge '"git-crypt" smudge'
git config filter.git-crypt.clean '"git-crypt" clean'
git config diff.git-crypt.textconv '"git-crypt" diff'
```

Keep the exported key file (`folio-git-crypt.key`) for as long as you need
the encrypted ledger or its remote.

## Nothing else

- Folio never uses the macOS Keychain, so there is nothing to remove there.
- Folio never writes to `~/.gitconfig`, `~/.config` or `~/.ssh`. With an SSH
  remote, git uses your Mac's own ssh and keys.
- The bundled tools write nothing outside Folio's folders and your ledger: no
  Python or Beancount caches, nothing in `~/.cache`, and nothing inside
  Folio.app.
- Nothing in `/Library`, `/Users/Shared`, `~/Library/Containers` or
  `~/Library/LaunchAgents`.
- On-device speech models used by voice input belong to macOS, which manages
  them for all apps.
