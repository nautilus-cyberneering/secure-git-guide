# GPG - Best Practices

![HEADER IMAGE](./media/HEADER/GitHub-Repo-SecureGitGuide-ART-005.jpg)

## Understanding GPG Defaults

![IMAGE](https://nautilus-cyberneering.de/wp-content/uploads/2022/01/gpg_gITHUB.jpg)

There are many arrangement and possible combinations of keys, sub-keys, user-id's and so on. When you use GPG to generate your keys, by default it generates your keys following a standard template:

- Keys
  - Primary Key (Certify and Signing)
  - Supplementary Key (Encrypting)
- User-ID
  - Primary ID (Name, Email, and Comment)

You can notice that the primary key has been set with the dual-capabilities of Certifying (to make new supplementary keys) and Signing (such as signing a Git commit).

This basis structure was chosen upon the thought that the keys used for Encryption need to be (or at least should be) rotated regularly, however Signing can remain constant over the lifetime of the OpenPGP Key.

However, in many cases this is not what the user would want if given the choice.

### Why is this not optimal?  

The default set-up leaves still some space for improvement. This is because it does not take advantage of the possibility to create individual sub-keys for each capability.

The idea is that you essentially disconnect all the rights of your choice from your primary key and just use your sub-keys to avoid using your primary key. The only times you then use your primary is to cancel (revoke) existing sub-keys or to generate new sub-keys.

It is very advanced to separate the primary key from the supplementary keys.

The advantage of this approach is that if any of these sub-keys gets compromised, you can revoke individually and generate a new key, all while keeping your primary key valid.

If you do not do this, you probably will end up someday with your primary key compromised and will have to regenerate a new primary key, etc.

## Recommended Best Practices

======================================================================

### How to create further sub-keys

---
![IMAGE](https://nautilus-cyberneering.de/wp-content/uploads/2022/01/MOTHERkEY-1024x384.jpg)

In order to create additional sub-keys, you need to use the GPG command-line interface.

You can read a very clear step-by-step guide [here](./008_GPG-How-to-create-a-subkey-for-signing.md).

I base the following summary of steps in the command line interface on his work.

1. Type:

    ```terminal
    gpg --list-keys --fingerprint --with-keygrip --with-subkey-fingerprints
    ```

2. In the list you get an overview of all the primary key and its existing sub-keys. You will copy the second line of your public key made up of 10 pairs of 4 numbers and or letters.

3. Using the noted public key type:

    ```terminal
    gpg --edit-key <public key 40 digits without spaces>
    ```

4. You will get a display of their associated private key and a new prompt so type:

    ```terminal
    addkey
    ```

5. Select your applicable key, most likely option **(4) RSA (sign only)**

6. It will ask you to specify the keysize duration, I recommend **4096** and **"0"** for does not expire.

7. Confirm the creation.

8. You get a new overview of the new secret keys, seeing the newly generated sub-key and the changed rights of the primary key.

9. To see the equivalent public keys for export type:

    ```terminal
    gpg --list-keys --fingerprint --with-keygrip --with-subkey-fingerprints <public key 40 digits without spaces>
    ```

10. You should now see the new sub-key and the changed primary key rights.

### Removing primary key rights

---
The last step to finish this is to remove all capabilities except the "certify" capability from the primary key. For this, you will continue using the command line but using the "expert" mode.

1. Type:

    ```terminal
    gpg --expert --edit-key <public key 40 digits without spaces>
    ```

2. You will get an overview of the primary key's rights.

3. Type:

    ```terminal
    change-usage
    ```

4. Use the toggle option taking away the rights for which you already have created the new sub-key.

5. Once you are done you get a new overview of the primary key's rights.

6. Type save and you are all set working on the keys.

7. Type:

    ```terminal
    gpg --list-keys --fingerprint --with-keygrip --with-subkey-fingerprints <public key 40 digits without spaces>
    ```

8. You will see the new public key rights where you should only see the "c" option for certify at the "pub" key.

### Configuring Git with your new key

---
In order to set up your new key for signing your commits you have to follow these steps:

1. In the command prompt type:

    ```terminal
    git config --global --edit</code>
    ```

2. This will open the git config file in your default editor. In my case it opens it in Visual Code.

3. Once here look for the following entry of the signing-key and update it with the last 16 digits of your new signing sub-key.

4. Save it.

5. If you are using GitHub you will need to export your new public key and import it into it, following the necessary steps as shown in their [GitHub Documentation - Signing commits](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits).

### Always use a Passphrase :exclamation

---
When creating the set of keys you are asked for a passphrase. Set it and remember it or even better write it down somewhere. This is another safety measure but it is essential.

### Backing up Your Revocation Certificate :exclamation

---
**_Make sure that you keep a backup of your revocation certificate or that you print it out and store it somewhere safe in case that you were to have to use it._**

### Rotating Your Encryption Keys :exclamation

---
This being one of the most used capabilities. It is recommended that you rotate these keys to prevent anyone to have access to any of your encrypted information, creating for example new keys in events such as computer change, etc. It is important though to back these up in the event that you were to have files encrypted with these.

### Setting an Expiration Date :exclamation

---
Another good idea is to set an expiration date not too far in the future in case that you were to not be able to revoke your certificate due to having lost your revocation certificate.

[Back to home](./index.md)
