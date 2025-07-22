# How-To-Use (Windows)

This guide explains in very plain English how to prepare a Windows PC, find your iMessage data, and run **imessage-exporter**. Each step shows how to check if something is already installed before installing it.

## 1. Prepare the computer

1. **Check if Rust is installed**
   - Open **Command Prompt** or **PowerShell**.
   - Type `rustc --version` and press Enter.
   - If you see a version number, Rust is already installed and you can skip the install step.
2. **Install Rust if needed**
   - Go to [https://rustup.rs](https://rustup.rs) in your web browser.
   - Download and run the installer. Accept the defaults.
   - Close the installer and open a new command window.
   - Type `cargo --version` to make sure the install worked. This also installs `cargo` which is needed below.
3. **(Optional) Check for media tools**
   - Type `ffmpeg -version`. If the command is not found, you can download **ffmpeg** from [https://ffmpeg.org](https://ffmpeg.org).
   - Type `magick -version`. If the command is not found, you can download **ImageMagick** from [https://imagemagick.org](https://imagemagick.org).
   - These tools are only required if you want attachments converted to common formats.

## 2. Get the messages database

Most Windows users will use an iOS backup. You may also copy a `chat.db` file from a Mac.

### iOS backup

1. Install or open **iTunes** on your PC.
2. Connect your iPhone or iPad and create a **local backup**.
3. If you choose **Encrypt local backup**, remember the password. You will need it when running `imessage-exporter`.
4. The backup folder is stored under `%APPDATA%\Apple Computer\MobileSync\Backup\`. Inside it is a folder with a long name. That path is the one you pass to the program.

### macOS database copy

1. If you have access to a Mac, copy the `chat.db` file and the `Attachments` folder from `~/Library/Messages/` to your Windows computer.
2. Note the location of these files on Windows so you can pass the paths when running the exporter.

## 3. Install the program

1. **Check if imessage-exporter is already installed**
   - Type `imessage-exporter --version`.
   - If a version number prints, you can skip installation.
2. **Install using cargo**
   - Run `cargo install imessage-exporter` in a command window.
   - The program will be placed in `C:\Users\<you>\.cargo\bin\` as `imessage-exporter.exe`.
3. **Alternatively download a release**
   - Visit the project’s releases page and download the Windows zip file.
   - Unzip it anywhere you like and use the contained `imessage-exporter.exe`.

## 4. Run imessage-exporter

Basic syntax:

```cmd
imessage-exporter -f <txt|html> [options]
```

Common options:

- `-c <clone|basic|full|disabled>` – how to handle attachments.
- `-p <path>` – location of `chat.db` or the iOS backup.
- `-o <path>` – folder where the export will be written (defaults to `imessage_export` in your user directory).
- `-s <YYYY-MM-DD>` and `-e <YYYY-MM-DD>` – start and end dates to filter messages.
- `-x <password>` – password for an encrypted iOS backup.

### Examples

1. **Export from an iOS backup with attachments converted**
   ```cmd
   imessage-exporter -f html -c full -p "%APPDATA%\Apple Computer\MobileSync\Backup\<backup_id>" -x <password>
   ```
2. **Export from a copied Mac database**
   ```cmd
   imessage-exporter -f txt -p "C:\path\to\chat.db" -o "C:\path\to\export"
   ```
3. **Only export messages from 2020**
   ```cmd
   imessage-exporter -f txt -s 2020-01-01 -e 2021-01-01 -p "%APPDATA%\Apple Computer\MobileSync\Backup\<backup_id>"
   ```

Running the command will print progress information. When finished, your export directory will contain one file per conversation. Attachments will be placed in an `Attachments` folder if copying was enabled.

## 5. Diagnostics mode

To check the health of your database, run:

```cmd
imessage-exporter -d -p "%APPDATA%\Apple Computer\MobileSync\Backup\<backup_id>"
```

This prints information such as missing attachments or duplicated contacts and then exits.

## 6. Viewing the export

- Text exports are plain `.txt` files you can open in any text editor.
- HTML exports can be opened in any web browser.
- To customize how HTML exports look, create a file called `style.css` in the export folder and add your own CSS rules.

That’s it! Following these steps on Windows will create a readable archive of your iMessage history.
