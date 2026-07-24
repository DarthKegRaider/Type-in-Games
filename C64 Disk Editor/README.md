# CBM Tool — User Guide

A KDE/Qt front-end for VICE's `c1541` and `petcat`, for browsing Commodore disk images, editing BASIC listings, and moving files between disks.
Any limitations of those applications, will also be a limitation of this front-end.
Also available at your disposal is a simple sprite viewer (View Graphics).

## Requirements

VICE, pyside6, (python3, already installed on CachyOS)
```bash
sudo pacman -S vice pyside6
```

Execute / Run tool with:

```bash
python3 cbmtool.py
```
**Create a KDE menu item or desktop launcher by adding the location of cbmtool.py to the "Command-line arguements" field.**


## Layout

- **Menu bar**
  - **Disk** — Open Image, New Image, Duplicate Image..., Recent Files
  - **Files** — Add File..., Extract Selected, Delete Selected (shown in red — destructive, but prompts for confirmation)
  - **Options** — Set Emulator...
- **Toolbar** — View Graphics..., Refresh, Launch Emulator
- **Disk directory** (left panel) — contents of the currently open disk image
- **BASIC source** (right panel) — the detokenized text of whatever file is selected
- **Log** (bottom strip) — status messages and errors from the last few actions

## Working with disk images
_______________________________________________________________________________________________
| Action                 | Function                                                           |
|------------------------|--------------------------------------------------------------------|
| **Open Image**         | Open an existing `.d64`/`.d71`/`.d81`/`.g64`/`.x64`                |
| **New Image**          | Format a new blank `.d64` (prompts for disk name + 2-char ID)      |
| **Duplicate Image...** | Byte-for-byte copy of the whole disk file. Use this for whole-game |
|                        | or compilation disks, requests new name for duplicated image.  See |
|                        | note below for this inclusion.                                     |
| **Recent Files**       | Your last 8 opened/created images, remembered between sessions     |
| **Refresh**            | Re-read the directory listing. (useful after external changes)     |
-----------------------------------------------------------------------------------------------
**Why "Duplicate Image" instead of copying files?** 
Many commercial *cough*  cracked disks use a custom fastloader — what displays in the 
directory as a `.prg` is often just a small loader stub, and the actual  game data 
lives in raw sectors outside the normal CBM file system. Extracting  and re-adding 
that stub to a new disk often won't bring the game with it. For those disks, always 
duplicate the whole `.d64` rather than moving individual files.

## Working with individual files
_______________________________________________________________________________________________
| Action                 | Function                                                           |
|------------------------|--------------------------------------------------------------------|
| **Click a file**       | In the directory list, This extracts + detokenizes (via `petcat`)  |
|                        | into the BASIC source panel automatically.                         |
| **Add File...**        | Write a local file into the open image. Lets you name it, and      |
|                        | includes a **"Swap upper/lower case"** checkbox.  If the directory |
|                        | shows the file in UPPER, delete and retry checking the swap box.   |
| **Extract Selected**   | Save the selected file to your local filesystem as a raw file.     |
|                        | e.g. `.prg`, `.seq`)                                               |
| **Delete Selected**    | Removes the file from the disk image. (Confirmation required)      |
| **View Graphics...**   | Reinterprets the selected file's raw bytes as an 8x8 character set |
|                        | or 24x21 hi-res sprites, for a quick visual check.                 |
-----------------------------------------------------------------------------------------------

## Options
_______________________________________________________________________________________________
| Set Emulator           | Enter the command used to launch your preferred emulator. (x64sc)  |
| BASIC Editor Colours   | Default - The default colurs chosen (Orange, Blue, Green)          |
|                        | Colour blind friendly - Okabe-Ito palette                          |
|                                                                                             |
| The options are saved for your next session                                                 |
-----------------------------------------------------------------------------------------------

## Editing BASIC

1. Click a BASIC `.prg` file in the directory list — its detokenized source loads into the right-hand editor.
   The panel incorporates simple markup highlighting.  Command are bold blue, numbers in orange, quoted sections green.
   ** For those with colour blindness, the "Okabe-Ito palette" is selectable in the Options menu.
2. Edit the text freely, using copy/cut/paste.
3. Choose one of:
   - **Save back to disk image** — re-tokenizes and writes the change back into the currently open image, replacing the original file.
   - **Save Listing As Text...** — saves the editor's plain-text BASIC listing to a `.txt` file on your filesystem.
   - **Export as PRG...** — tokenizes the editor's text straight to a standalone `.prg` file, without touching the open disk image.

## Running in an emulator

- **Launch Emulator** starts your configured emulator (`x64sc` by default), passing it the currently open disk image if one is loaded.
- **Options > Set Emulator...** lets you change the command if you use `x64`, `x128`, `xvic`, etc. instead. This is remembered between sessions.

## Known limitations

- Extraction/editing only makes sense for files that are stored as normal 
  CBM DOS files (plain BASIC programs, simple data files). Custom loaders, 
  copy protection, and multi-part loading schemes on commercial disks fall 
  outside what a file-level tool can meaningfully touch — use
  **Duplicate Image** for those instead.
- The graphics viewer is a simple raw-byte reinterpreter (8x8 hi-res chars
  or 24x21 hi-res sprites only) — it doesn't know about multicolor mode or
  non-standard memory layouts.
- BASIC tokenizing defaults to BASIC V2 (standard C64) at load address
  `$0801`. C128 or VIC-20 disks would need different flags in the code
  (`petcat -4`/`-w4`, different load address).

