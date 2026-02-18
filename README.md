# jxl-reenc

> Batch image-to-JpegXL conversion tool

## About

`jxl-reenc` is a homebrew Bash script designed to efficiently convert directories of images to the JpegXL (`.jxl`) format. It is built for users who want to save disk space without the hassle of manually converting files one by one.

It handles recursion, prevents overwriting by automatically renaming collisions (e.g., `image (Copy).jxl`), and provides real-time progress bars and compression statistics.

## Prerequisites

This script relies on standard CLI tools. Ensure you have the following installed:

* **libjxl** (for converting)
* **bash** (v4.0+)
* **magick** (ImageMagick v7, with JXL support)
* **file** (for MIME type detection)
* **pv** (for the progress bar)
* **coreutils** (specifically `numfmt`, `du`, `find`, `awk`)

## Installation

Simply download the script and make it executable:

```bash
wget https://github.com/rifux/jxl-reenc/raw/master/jxl-reenc
install -m 755 jxl-reenc ~/.local/bin/
```

## Usage

```bash
[environment variables] jxl-reenc [locations such as dirs and files]
```

If no arguments are provided, the script runs in the current working directory.

## Environment Variables

You can control behavior by setting environment variables before the command:

* `JR_REMOVE=TRUE`

  Automatically removes the original file after a successful conversion.
  *Warning: Use with caution.*

* `JR_QUIET=TRUE`
  
  Disables all output, including the progress bar and final statistics.

## Examples

**1. Basic conversion of current directory:**

```bash
jxl-reenc
```

**2. Convert specific files and directories:**

```bash
jxl-reenc ~/Pictures ~/Downloads/image.png
```

**3. Convert and delete originals (Space Saving Mode):**

```bash
JR_REMOVE=TRUE jxl-reenc /run/media/user/backup/
```

## Output Explanation

The script provides a summary of operations upon completion.

Example output:

```text
❯ JR_REMOVE=TRUE jxl-reenc /run/media/user/test_dir

Estimating images count for '/run/media/user/test_dir'...
Found 87 images, starting conversion.
87.0  0:00:41 [2.08 /s] [=======================================>] 100%
Found 22 .jxl images, they were skipped.

Total compression rate: 41.1%.
In size it's 14Mi difference (24Mi -> 9.6Mi).
```

* **Skipped images:** Files already in `.jxl` format are ignored to prevent redundant processing.
* **Compression rate:** Calculated as `(new size / old size) * 100`. Lower is better.
* **Difference:** The actual amount of disk space saved.

## How it works

* **Encoding:** Uses `magick` (ImageMagick) with `jxl:effort=7`. This is a "sweet spot" setting that provides good compression ratios without taking excessively long.
* **Collision Handling:** If `image.jpg` is being converted but `image.jxl` already exists, the script generates `image (Copy).jxl`. If that exists, it increments to `image (Copy 1).jxl`, and so on.
* **Safety:** The script only touches files that are detected as images via `file --mime-type`.

## License

MIT License - see [LICENSE](LICENSE) for details.
