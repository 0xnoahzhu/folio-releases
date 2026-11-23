# Move Folio to another Mac

Folio keeps two separate things, and each moves in its own way:

- **Your ledgers** are ordinary folders of `.bean` files, with their git
  history. They are yours, not Folio's.
- **Folio's own data** is one database, `folio.db`, in
  `~/Library/Application Support/Folio`.

If you move to the new Mac with Apple's **Migration Assistant**, both come
along with everything else and there is nothing more to do. The steps below
are for moving by hand, or for moving only part of Folio's data.

## What is in `folio.db`

| Kept in `folio.db` | Notes |
|---|---|
| Chats with Filio | Plus the masking key, which gives each hidden name or number the same stand-in every time it is sent to the model |
| Inbox documents (imported statements and receipts) | Including what Folio read from them |
| Changes waiting in Review | |
| What Filio remembers, and the categories it learned from your picks | |
| Settings, model profiles, recent ledgers | |
| **API keys** (model providers, speech) | Secret |
| **Sync tokens** for your git remotes | Secret |
| **Remembered PDF passwords** | Secret |
| **The lock PIN** (stored as a hash) | |

Everything else in `~/Library/Application Support/Folio` (caches, `logs`) is
not needed on the new Mac.

## 1. Move your ledgers first

- **With a git remote** (for example a private GitHub repository): clone it on
  the new Mac (`git clone …`). A ledger that uses encryption (git-crypt) also
  needs its exported key file: unlock the clone with it before choosing the
  folder in Folio. Folio carries git and git-crypt inside the app, so nothing
  needs to be installed:

  ```sh
  cd /path/to/your/ledger
  PATH="/Applications/Folio.app/Contents/Resources/git/bin:$PATH" \
    git-crypt unlock ~/Documents/folio-git-crypt.key
  ```
- **Without a remote:** copy the whole ledger folder, including the hidden
  `.git` folder.

> **Put each ledger at the same path as before**, for example
> `/Users/you/Documents/Ledger`, which also means the same macOS user name.
> Folio files chats, Inbox documents, waiting changes, memories and sync
> tokens under the ledger folder's full path. A ledger opened from a different
> path starts with none of them.

## 2. Move Folio's data

1. Install Folio on the new Mac, but **do not open it yet**. If you already
   opened it, quit it (⌘Q).
2. Quit Folio on the old Mac (⌘Q), so the database is complete on disk.
3. Copy these files from the old Mac to the same folder on the new Mac,
   `~/Library/Application Support/Folio` (create it if it is not there):
   - `folio.db`
   - `folio.db-wal` and `folio.db-shm`, if they exist
4. Open Folio on the new Mac.

This moves everything in the table above, including your API keys, sync
tokens and lock PIN. Copy the files over a connection you trust (a USB drive
you keep, AirDrop, or your own network), not through a shared or public
service: `folio.db` holds your keys in plain form.

## Move only some of it

Your chats, Inbox, waiting changes and memories cannot be split from each
other: they move together in `folio.db`. What you can leave behind are the
secrets and the lock PIN. Do this on the new Mac **after step 3 and before
step 4**, so Folio has not opened the database yet:

```sh
cd ~/Library/"Application Support"/Folio
cp folio.db folio.db.backup
sqlite3 folio.db "DELETE FROM secrets WHERE name = 'lock:pin';"
```

That removes only the lock PIN, so Folio opens without asking for one. You can
set a new PIN in Settings later. To leave other secrets behind too, add these
lines before opening Folio:

```sh
sqlite3 folio.db "DELETE FROM secrets WHERE name LIKE 'provider:%' OR name LIKE 'stt:%';"   # model and speech API keys
sqlite3 folio.db "DELETE FROM secrets WHERE name LIKE 'sync:%';"                            # sync tokens
sqlite3 folio.db "DELETE FROM secrets WHERE name LIKE 'pdf-password:%';"                    # remembered PDF passwords
sqlite3 folio.db "PRAGMA integrity_check;"                                                  # should print: ok
```

Then open Folio and enter the API keys and sync tokens again in Settings.

- **Keep `masking:key`.** If it were missing, Folio would make a new one, and
  a name from an earlier chat would get a different stand-in when you
  continue that chat: the model would see the same person under two names.
- `sqlite3` comes with macOS; nothing needs to be installed.
- If anything goes wrong, quit Folio and put `folio.db.backup` back as
  `folio.db`.
- Once the new Mac works, you can remove Folio from the old one: see
  [Uninstall Folio](uninstall.md).
