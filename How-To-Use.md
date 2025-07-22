# How-To-Use

This guide explains in plain English how to prepare your computer, obtain your iMessage data, and run **imessage-exporter**. It is written for macOS users but also notes what to do for iOS backups.

## 1. Prepare the computer

1. **Install Rust (recommended way)**
   - Visit [https://rustup.rs](https://rustup.rs) and follow the on-screen instructions to install Rust.
   - This installs the `cargo` command which allows you to install `imessage-exporter`.
2. **Give Terminal full disk access**
   - Open **System Settings > Privacy & Security > Full Disk Access**.
   - Add your terminal application (for example, Terminal, iTerm, or another shell) and enable the switch.
   - This permission is required so `imessage-exporter` can read the Messages database.
3. **(Optional) Install extra tools**
   - For converting media you may want **ffmpeg** and **ImageMagick**. These can be installed with Homebrew:
     ```bash
     brew install ffmpeg imagemagick
     ```
   - They are only needed if you want attachments converted to common formats.

## 2. Get the messages database

There are two possible sources of data:

### macOS database

1. The Messages database is stored at `~/Library/Messages/chat.db` and attachments are under `~/Library/Messages/Attachments`.
2. Nothing else is required if the messages you want are already on your Mac.

### iOS backup

1. Connect your iPhone or iPad to the computer.
2. In Finder (or iTunes on Windows), create a **local backup** of the device.
3. If you choose **Encrypt local backup**, remember the password. You will need it later when running `imessage-exporter`.
4. The backup directory will appear under `~/Library/Application Support/MobileSync/Backup/` (macOS) or under your user profile on Windows.

## 3. Install the program

The easiest method is via **cargo**:

```bash
cargo install imessage-exporter
```

This downloads and compiles the program. After it finishes you will have an `imessage-exporter` executable in `~/.cargo/bin/`.

If you prefer Homebrew you can run:

```bash
brew install imessage-exporter
```

Homebrew may lag behind the latest release but it does not require Rust.

Prebuilt binaries are also available on the project’s releases page if you wish to download one directly.

## 4. Run imessage-exporter

Basic syntax:

```bash
imessage-exporter -f <txt|html> [options]
```

Common options:

- `-c <clone|basic|full|disabled>` – how to handle attachments.
  - `clone` copies files without converting.
  - `basic` also converts HEIC pictures to JPEG.
  - `full` converts HEIC, CAF and MOV files to common formats.
  - `disabled` does not copy attachments. This is the default.
- `-p <path>` – location of `chat.db` or of an iOS backup.
- `-o <path>` – folder where the export will be written (defaults to `~/imessage_export`).
- `-s <YYYY-MM-DD>` and `-e <YYYY-MM-DD>` – start and end dates for filtering messages.
- `-x <password>` – password for an encrypted iOS backup.

### Examples

1. **Export everything from your Mac as HTML and convert attachments**
   ```bash
   imessage-exporter -f html -c full
   ```
2. **Export from a specific iOS backup with a password**
   ```bash
   imessage-exporter -f txt -p ~/Library/Application\ Support/MobileSync/Backup/<backup_id> -a iOS -x <password>
   ```
3. **Only export messages from 2020**
   ```bash
   imessage-exporter -f txt -s 2020-01-01 -e 2021-01-01
   ```

Running the command will print progress information. When finished your chosen export directory will contain one file per conversation (for example `Chat with Steve.html`). Attachments will be placed under an `Attachments/` folder if copying was enabled.

## 5. Diagnostics mode

If you simply want to inspect the health of the database, run:

```bash
imessage-exporter -d
```

This prints information such as missing attachments or duplicated contacts and then exits.

## 6. Viewing the export

- Text exports are plain `.txt` files you can open in any text editor.
- HTML exports can be opened in a web browser. If you did **not** copy attachments you may need to give the browser full disk access so it can read files from `~/Library`.
- To customize the appearance of HTML exports, create a file named `style.css` in the export directory. Any rules you put in that file override the defaults.

That’s it! Following these steps will create a readable archive of your iMessage history.
