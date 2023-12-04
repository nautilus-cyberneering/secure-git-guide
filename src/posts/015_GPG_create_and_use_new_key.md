---
title: 15. Generate and use a new key for Git.
description: Generate and use a new key for Git.
date: '2023-4-23'
published: true
---

This walkthrough guide show how to create a new GPG key and use it for signing your git commits.

- [Setup GPG](#setup)
- [Launching GPG](#launching-gpg)
- [Making a Key for Git](#new-key)
  - [Certification and Signing](#cert-and-sign)
- [Generate a New Key](#gen-key)
  - [Step 1: Create the primary key and ID](#create-primary)
    - [Step 1.0: Generate Key](#generate)
    - [Step 1.1: Key Type](#key-type)
    - [Step 1.2: Key Capabilities](#key-capabilities)
    - [Step 1.3: Key Curve](#key-curve)
    - [Step 1.4: Key Expiry](#key-expiry)
    - [Step 1.5: Key Id](#key-id)
    - [Step 1.6: Key Id User](#key-id-user)
    - [Step 1.7: Key Id Email](#key-id-email)
    - [Step 1.8: Key Id Comment](#key-id-comment)
    - [Step 1.9: Review Key Id](#review-key-id)
    - [Step 1.10: Key Password](#key-password)
  - [Finished Step 1](#finished-key)
  - [Step 2: Add a Signing Subkey to your GPG Key](#signing-key)
    - [Step 2.0: List Keys](#list-keys)
    - [Step 2.1: Edit Key](#edit-key)
    - [Step 2.2: Add Subkey](#add-subkey)
    - [Step 2.3: Subkey Type](#subkey-type)
    - [Step 2.4: Subkey Curve](#subkey-curve)
    - [Step 2.5: Subkey Expiry](#subkey-expiry)
    - [Step 2.6: Confirm Add Subkey](#confirm-add-subkey)
    - [Step 2.7: Save and Exit Editing Key](#save-and-exit)
  - [Step 3: Confirm By Signing a Test-Message](#test-sign)
    - [Step 3.1: List Subkeys](#list-subkeys)
    - [Step 3.2: Sign Test Message](#sign-test-message)
    - [Step 3.3: Verify Test Signature](#verify-test-signature)
- [Sign Commits with Git](#sign-commits)
  - [Add Signing Key to Git](#add-key-to-git)
  - [Sign Commits By Default](#auto-sign-commits)
- [Register Signing Key with Github or Gitlab](#online-key-registration)
  - [Export Public Signing Key](#export-key)
    - [Register Key in Gitlab](#register-key-in-gitlab)
    - [Register Key in Github](#register-key-in-github)
- [Further Reading](#further-reading)

<h2 id='setup'>Setup GPG</h2>

GPG is installed alongside GIT. If your systems has git, then it should also have GPG.

On windows git is not pre-installed. You can install the official version from: https://git-scm.com/

<h2 id='launching-gpg'>Launching GPG</h2>

On linux or macos: simply load up the terminal and type `gpg --help`, the command should be found and the normal help-text should appear.

> Note: if you type `gpg` (without "--help"), you will be placed into a gpg-prompt that asks you to write your message; this prompt is for encryption and is not related to the use-case of signing git commits. You can simply exit this prompt with the "Ctl-C" / "Cmd-C" key combination.

On windows, you will need to launch the "git-prompt", a special terminal shell that includes the git command. From this prompt the `gpg` command will work as expected.

<h2 id='new-key'>Making a Key for Git</h2>

For use with git, we only need to sign commits, so we do not need to create an encryption key. However the defaults for gpg create both a signing and a encryption key.

> Note: there are some subtle reasons why it is disadvantageous to create (and publish) a encryption key needlessly. By making a encryption key it is saying that you accept and may use encrypted messages, this creates a suspicions that may be unwelcome. Many have the opinion that it is generally better to avoid non-transient encryption in altogether.

In this guide we explicitly avoid making an encryption key, and explicitly separate the "certification" and "signing" roles.

<h3 id='cert-and-sign'>Certification and Signing</h3>

The conventions of modern cryptographic systems have created a important distinction between: "certification", simply abbreviated to 'c'; and "signing", likewise occasionally abbreviated to simply 's'.

_Certification_ makes a statement about another element in a cryptographic system, such as an ID or a SUBKEY, these statements are not intended to have any meaning outside of the cryptographic system itself (such as a PGP (or GPG) system).

_Signing_ on the other hand is making a general statement about something that it's primary meaning outside of the cryptographic system, such as a letter or a document.

By default GPG generates a single key for both purposes, however this has the unfortunate disadvantage that the signing key cannot be easily expired and rotated with a new one.

In this guide we separate these two roles onto different keys.

<h2 id='gen-key'>Generate a New Key</h2>

To fulfill our goals of:

1. Avoiding the generation of a Encryption Key.
2. Using separate keys for certification and signing.

It is needed to use some of the slightly more advanced functionally of GPG. - Thankfully it is not too hard and still quire manageable for a beginner.

> Note: It is simple to add a encryption key at a latter point if one finds the need for having one. For example: to receive security vulnerability reports in a more secure manner, or to store backups of your fiends revocation certificates.

<h3 id='create-primary'>Step 1: Create the primary key and ID</h3>

In this first step follow the interactive prompted guide to create the primary key that caries the "certification" role and we also follow the prompted guide to create a public identification that is certified by this primary key.

<h4 id='generate'>Step 1.0: Generate Key</h4>

We load gpg with two arguments:

1. Expert, we need this for selecting the key capabilities.
2. Full Generate Key, we need this to generate the key and make the ID.

```shell
$ gpg --expert --full-generate-key
```

<h4 id='key-type'>Step 1.1: Key Type</h4>

The first output prompt should look like this:

> Note: it is asking about what form the `PRIMARY KEY` should take.

```r
Please select what kind of key you want:
   (1) RSA and RSA
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
   (9) ECC (sign and encrypt) *default*
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (13) Existing key
  (14) Existing key from card
Your selection?
```

> There are different types of keys to choose from: RSA, DSA, and ECC. Both RSA and DSA are old. It is now recommended that you select ECC, as the keysize and signatures (for the same security) are smaller.

For this guide we wish to remove the signing capabilities for the primary key. So it is needed that you select an option with "set your own capabilities".

**Select number `11`.**

<h4 id='key-capabilities'>Step 1.2: Key Capabilities</h4>

The second prompt asks about the capabilities for the key you are about to generate:

```r
Possible actions for this ECC key: Sign Certify Authenticate
Current allowed actions: Sign Certify

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection?
```

For this prompt we wish to toggle the sign capability so that the current actions reads:

```r
Current allowed actions: Certify
```

**Select number `Q` to move to the next prompt.**

<h4 id='key-curve'>Step 1.3: Key Curve</h4>

The next prompt has a list of elliptic curves to choose from.

```r
Please select which elliptic curve you want:
   (1) Curve 25519 *default*
   (2) Curve 448
   (3) NIST P-256
   (4) NIST P-384
   (5) NIST P-521
   (6) Brainpool P-256
   (7) Brainpool P-384
   (8) Brainpool P-512
   (9) secp256k1
Your selection?
```

GPG chooses a very reasonable default: `Curve 25519`.

**Just press `enter` to select the default and move on to the next prompt.**

<h4 id='key-expiry'>Step 1.4: Key Expiry</h4>

The next prompt is where you select the expiry of you primary key.

```r
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for?
```

> Note: Choosing an appropriate expiry is a bit of an art. In general it is important not to choose `key dose not expire`.
>
> As it is not difficult to adjust this expiry in the future, it is reasonable to choose a reasonably short time, such as half a year.

**Choose you expiry, perhaps `180` days, and press `enter` to go to the next prompt.**

<h4 id='key-id'>Step 1.5: Key Id</h4>

The next set of prompts asks for information for the `PRIMARY ID` that is certified by the `PRIMARY KEY`. Once you have generated your primary key it is possible to add as many ID's as you need to you gpg key.

```r
GnuPG needs to construct a user ID to identify your key.
```

<h4 id='key-id-user'>Step 1.6: Key Id User</h4>

Firstly, it asks for for your _"real name"_.

```r
Real name:
```

> Note: this dose not need to be your legal pseudonym, it could be your username, or online handel according to your taste.
>
> For the purposes of this guide it is suggested that you select the same name as you use for authoring your git commits.

**Enter the `name` of your pseudonym, and press `enter`.**

<h4 id='key-id-email'>Step 1.7: Key Id Email</h4>

The next prompt asks for your email.

```r
Email address:
```

> Note: for the purposes of this guide it is suggested that you select the same email as you use for authoring you git commits.

**Enter the `email` of your pseudonym, and press `enter`.**

<h4 id='key-id-comment'>Step 1.8: Key Id Comment</h4>

The next prompt asks for a comment.

```r
Comment:
```

> Note: It is good to enter a comment that suggests that this key is online and of somehow lower securely than a offline dedicated key.

**Enter a `comment`, such as `Online Key`, and press `enter`.**

<h4 id='review-key-id'>Step 1.9: Review Key Id</h4>

```r
You selected this USER-ID:
```

At this stage you get to review the proposed ID.

**If everything looks good, select `O`, and proceed to the generation of you key.**

<h4 id='key-password'>Step 1.10: Key Password</h4>

Creating a password will encrypt the private parts of you key. The password is useful to protect against evil maid-attacks and for storing a backup of your key in a insecure location.

In general, if your computer is compromised, then you key is also compromised independent of the strength of your password. - Many people just choose some sort of "reminder" password, to remind them that they are about to use particular key, and set their password to the the name of the pseudonym that the key is associated with.

<h4 id='finished-key'>Finished Step 1</h4>

Now you have generated you new gpg key. GPG prints out some information to take note of.

1. GPG prints out the location of a revocation certificate for the newly generated key. This certificate is useful if you have forgotten the password, or otherwise lost the private keys to you gpg-key.

2. The fingerprint of you primary key. This hexadecimal number is used to unambiguously refer to the primary key that you just generated.

<h3 id='signing-key'>Step 2: Add a Signing Subkey to your GPG Key</h3>

GPG keys are made up of many cryptographically related elements, after the primary key, the two most commonly used is the user-id's, (that we have already created in the primary key generation process), and subkeys.

Since we are wanting to use our gpg key for interaction with the real world, in our case signing git commits, we need to create a key that has the "signing" usage flag set.

<h4 id='list-keys'>Step 2.0: List Keys</h4>

The long key-fingerprint that was printed to the terminal at the generation of the key is needed. However if you have closed you terminal, there is no issue we can quickly work it out:

```shell
$ gpg --list-secret-keys
```

This command is very useful and will print the fingerprint of each of your private primary keys alongside the user-id's associated with these keys.

You should be able to find the key fingerprint for your key, it should looks something like `0123456789ABCDEF0123456789ABCDEF01234567`, but with different letters and numbers.

<h4 id='edit-key'>Step 2.1: Edit Key</h4>

We now need to edit the key to add a new subkey that will be responsible for signing messages (and git commits).

```shell
$ gpg --edit-key 0123456789ABCDEF0123456789ABCDEF01234567
```

> > Note: You need to adjust your commands fingerprint to the key that you generated in **Step 1**.

The prompt should tell you:

```r
Secret key is available.
```

<h4 id='add-subkey'>Step 2.2: Add Subkey</h4>

The gpg prompt will appear:

```
gpg>
```

**Enter the command `addkey`, and press `enter` to continue.**

<h4 id='subkey-type'>Step 2.3: Subkey Type</h4>

The next prompt asks for what kind of key you wish to add:

```r
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
  (10) ECC (sign only)
  (12) ECC (encrypt only)
  (14) Existing key from card
Your selection?
```

**Select `10`, and press `enter` to continue.**

<h4 id='subkey-curve'>Step 2.4: Subkey Curve</h4>

The next prompt, as before, asks for the curve you wish for the new key:

```r
Please select which elliptic curve you want:
   (1) Curve 25519 *default*
   (4) NIST P-384
   (6) Brainpool P-256
Your selection?
```

**The default is good, just press `enter` to continue.**

<h4 id='subkey-expiry'>Step 2.5: Subkey Expiry</h4>

The next prompt, as before, asks for the key expiry:

```r
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for?
```

It is good to select a date that is sooner than the primary key expiry. In this guide we choose 3/4 of the primary key expiry as a suggestion:

**Enter `135` and press `enter` to continue.**

<h4 id='confirm-add-subkey'>Step 2.6: Confirm Add Subkey</h4>

Pleaser review, and confirm that you wish to create and add this key.

```r
Really create? (y/N)
```

Upon creation you will be prompted for you primary key password.

<h4 id='save-and-exit'>Step 2.7: Save and Exit Editing Key</h4>

the gpg prompt will return:

```
gpg>
```

**Enter `save`, and press exit to commit the changes and exit.**

<h3 id='test-sign'>Step 3: Confirm By Signing a Test-Message</h3>

Now we sign a message and verify it, confirming the signature was made by the subkey.

<h4 id='list-subkeys'>Step 3.1: List Subkeys</h4>

Lets first show the fingerprint of the signing subkey that we just created:

```shell
gpg --with-subkey-fingerprints --list-key 0123456789ABCDEF0123456789ABCDEF01234567
```

This command will output the details about the key selected, and show the sub-key fingerprint. Take note of the subkey fingerprint, as we will compare it to see if everything went correctly.

<h4 id='sign-test-message'>Step 3.2: Sign Test Message</h4>

```shell
gpg --armor --sign-with 0123456789ABCDEF0123456789ABCDEF01234567 --sign
```

A prompt will ask for the message to sign.

**Type You Message, such as `0123456789ABCDEF0123456789ABCDEF01234567 gpg test-sign`, and press `enter`, then "ctl-d"/"cmd-d" to finish.**

The signed message will be printed to the terminal.

<h4 id='verify-test-signature'>Step 3.3: Verify Test Signature</h4>

```shell
gpg --verify
```

A prompt will ask for the message to verify.

**Copy-Paste the signed message from above starting with `-----BEGIN PGP MESSAGE-----`, press `enter`, then "ctl-d"/"cmd-d" to finish.**

GPG will about a report to the terminal that tell you if the message was signed correctly, and the fingerprint of the key that signed the message.

Take note of the fingerprint, and compare it to the subkey that was listed in "Step 3.1". If they are the same, we can see that the message was correctly signed with the right key.

<h2 id='sign-commits'>Sign Commits with Git</h2>

Now that we have successfully generated your new key, we need to tell git about it so it may sign your commits by default.

<h3 id='add-key-to-git'>Add Signing Key to Git</h3>

```shell
$ git config --global --add user.signingkey 0123456789ABCDEF0123456789ABCDEF01234567
```

<h3 id='auto-sign-commits'>Sign Commits By Default</h3>

```shell
$ git config --global --add commit.gpgSign true
```

Now when you make commits in git, they will automatically be signed by your gpg key.

<h2 id='online-key-registration'>Register Signing Key with Github or Gitlab</h2>

Lastly it is good to add your gpg to your github or gitlab account. This will enable you commits have a little icon next to them showing that they have been verified.

<h3 id='export-key'>Export Public Signing Key</h3>

The following command tell git to print to the console your gpg-public key:

```shell
$ gpg --armor --export 0123456789ABCDEF0123456789ABCDEF01234567
```

<h4 id='register-key-in-gitlab'>Register Key in Gitlab</h4>

Under Settings select SSH and GPG Keys", and add your public key under the GPG section there.

<h4 id='register-key-in-github'>Register Key in Github</h4>

Under Profile select "GPG Keys", and add your gpg public key there.

<h2 id='further-reading'>Further Reading</h2>

- How to add a encryption sub key to your gpg key.
- How to add additional user-id's to your gpg key.
- How to revoke a user-id or sub-key, but not your entire gpg key.
- How to store your primary gpg-key offline, and only have your subkeys online.
- How to extend the expiry of your gpg key and rotate your subkeys.
