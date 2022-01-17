# GPG - 101 - How to get your first GPG Keys

## Installing GPG

The steps to install GPG on your computer are the following:

1. Download the necessary software for your Operating System at:
    - [Windows](https://gpg4win.org/download.html)
    - [Linux - Installed through terminal](https://linuxhint.com/gpg-command-ubuntu/)
    - [Mac OS](https://sourceforge.net/p/gpgosx/docu/Download/)

## GPG4Win Kleopatra

### Generating your own new GPG Keys

---

Generating your own new GPG Keys is extremely easy.
Here is a series of screenshots of the process using the Kleopatra application for Windows.

![001](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_001.png)

![002](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_002.png)

![003](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_003.png)

![004](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_004.png)

![005](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_005.png)

![006](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_006.png)

![007](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_007.png)

![008](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_008.png)

![009](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_009.png)

![010](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_010.png)

![011](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_011.png)

### Creating a Revocation Certificate

---

![012](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_012.png)

![013](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_013.png)

![014](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_014.png)

![015](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_015.png)

### Sample Public GPG Key

---

![016](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/KLEO_CREATE_016.png)

## Command Line or Terminal

In order to generate your new keys you have two options:

- Running the default setup.
- Running the full setup which lets you define some additional specifications such as the encryption level.

### Default Setup

---
Open the command line as an admin and type: gpg --gen-keys

![cli001](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_001.png)

![cli002](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_002.png)

![cli003](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_003.png)

![cli004](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_004.png)

![cli005](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_005.png)

![cli006](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_006.png)

Once this has been done the keys get generated with the default encryption level of 3072.

### Full Setup

---

This option lets you:

- Define the encryption level up to 4096.
- Insert a comment.
- Define the key type (RSA, DSA, Elgamal, etc.).

It automatically generates also a revocation certificate at the default location which is indicated at the final step.

Open the command line as an admin and type: gpg --full-generate-keys

#### Defining the key type

![cliFgk001](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_fgk_001.png)

![cliFgk002](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_fgk_002.png)

#### Defining the encryption level

![cliFgk003](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_fgk_003.png)

![cliFgk004](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_fgk_004.png)

#### Defining the expiration date

![cliFgk005](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_fgk_005.png)

![cliFgk006](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_fgk_006.png)

![cliFgk007](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_fgk_007.png)

#### Defining the user ID to identify the key

![cliFgk008](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_fgk_008.png)

![cliFgk009](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_fgk_009.png)

![cliFgk010](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_fgk_010.png)

![cliFgk011](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_fgk_011.png)

#### Final screen with revocation certificate location

![cliFgk012](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/media/CLI_CREATE_fgk_012.png)

[Back to Readme Index](https://github.com/Nautilus-Cyberneering/GPG-Bootcamp/blob/main/README.md)