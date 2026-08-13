---
title: "Linux Terminal Quick Reference"
---

# Linux Terminal Quick Reference

You only need a few Linux terminal commands for this workshop.

You do **not** need to memorize them. Keep this page open as a reference and return to it whenever you need it.

---

## Where am I?

Run:

```bash
pwd
```

`pwd` means **print working directory**. It shows the directory you are currently working in.

For example:

```text
/home/jovyan
```

or:

```text
/home/jovyan/workshop
```

---

## What is here?

Run:

```bash
ls
```

`ls` lists the files and directories in your current location.

You can also list a specific directory without moving into it:

```bash
ls ~/workshop
```

For a more detailed listing with human-readable file sizes:

```bash
ls -lh
```

---

## Move into a directory

Use `cd` followed by a directory path.

For example:

```bash
cd ~/workshop/newport_dem
```

Then check your location:

```bash
pwd
```

and list what is there:

```bash
ls
```

---

## Move back one directory

Run:

```bash
cd ..
```

`..` means the **parent directory**, or one level up.

For example:

```text
~/workshop/newport_dem
          ↓
        cd ..
          ↓
~/workshop
```

---

## Return to your home directory

From anywhere, run:

```bash
cd ~
```

or simply:

```bash
cd
```

Both return you to your home directory.

---

## Useful path shortcuts

| Symbol | Meaning |
|---|---|
| `~` | Your home directory, such as `/home/jovyan` |
| `.` | The current directory |
| `..` | The parent directory |
| `/` | The top, or root, of the Linux filesystem |

For this workshop:

```text
~/workshop
```

means the `workshop` directory inside your home directory.

---

## Use Tab to autocomplete

Start typing a filename or directory name and press **Tab**.

For example:

```text
cd ~/workshop/newp<Tab>
```

The terminal may complete the name for you.

Autocomplete is especially helpful for long filenames and reduces typing mistakes.

---

## Recall a previous command

Press the **Up Arrow** to bring back commands you ran previously.

This is useful when you want to make a small change and run a command again.

---

## Stop a running command

If you intentionally need to stop a command that is currently running, press:

```text
Ctrl+C
```

Do not use `Ctrl+C` just because a DEM-processing step takes some time. Some workshop commands are expected to run for several minutes.

---

## Multi-line commands

Some workshop commands are written across several lines to make them easier to read:

```bash
globato build \
  -R -124.10/-124.00/44.59/44.64 \
  -P epsg:4269+5703 \
  ...
```

The backslash (`\`) at the end of a line tells the Linux shell that the command continues on the next line.

You can paste the full multi-line command into the terminal as shown.

:::{tip}
If you type a multi-line command manually, the backslash must be the final character on the line.
:::

---

## Workshop quick reference

| Command or key | What it does |
|---|---|
| `pwd` | Show your current directory |
| `ls` | List files and directories |
| `ls ~/workshop` | Show the workshop workspace |
| `ls -lh` | List files with details and readable file sizes |
| `cd directory_name` | Move into a directory |
| `cd ..` | Move up one directory |
| `cd ~` or `cd` | Return to your home directory |
| `Tab` | Autocomplete a filename or directory |
| `Up Arrow` | Recall a previous command |
| `Ctrl+C` | Stop the currently running command |
| `\` at the end of a line | Continue a command on the next line |

---

## If you get lost

These two commands are usually the best place to start:

```bash
pwd
ls
```

To inspect the workshop workspace from anywhere:

```bash
ls ~/workshop
```

If a workshop command fails and you are not sure why, copy the error message and show it to an instructor rather than installing or changing software on your own.
