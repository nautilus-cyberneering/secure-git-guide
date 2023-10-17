# GPG - How to use a signing key independently from the primary key

![HEADER IMAGE](./media/HEADER/GitHub-Repo-SecureGitGuide-ART-009.jpg)

Creating a subkey only for signing is considered a good practice, instead of using the primary key. If you want to know why you can read this [article](./008_GPG-How-to-create-a-subkey-for-signing.md).

In that article we explain how to create that signing subkey and use it in your computer. We also explain how to upload the public key to GitHub if you want to verify your Git commits.

Sometimes you want to use the private subkey on other machines and you should only expose the private key of the subkey.

One use case for that is signing commits on CI/CD. If you are using for example GitHub Actions, you could use a [GitHub Action to import GPG private keys](https://github.com/crazy-max/ghaction-import-gpg). After importing the private key you can sign new commits that are automatically created on the GitHub runners during the workflow executions.

You could upload the full export of the GPG key but ideally you should upload (as GitHub secret) only the private key of the key you are using for signing. In order to do that you need to export the subkey independently from the primary key.

## How to export only one private subkey from a GPG key

Given you have a key like the following one with a signing subkey (`8505CFE38CF47C8CBCF0C9D7FC47DBB5210FB475`):

```s
gpg -k --with-subkey-fingerprint 9DE332FB25C8A2D6EDF9DE8EB5B58E7CA8F56B98
pub   rsa3072 2022-03-03 [C]
      9DE332FB25C8A2D6EDF9DE8EB5B58E7CA8F56B98
uid           [ultimate] Test User <test.user@example.com>
sub   rsa3072 2022-03-03 [E]
      850CF85F94AF49D149273E1746BD4F801229C887
sub   rsa3072 2022-03-03 [S]
      8505CFE38CF47C8CBCF0C9D7FC47DBB5210FB475
```

You can export the private key for the subkey with:

```s
gpg --armor --export-secret-subkeys 8505CFE38CF47C8CBCF0C9D7FC47DBB5210FB475! > ~/Backups/primary_key.9DE332FB25C8A2D6EDF9DE8EB5B58E7CA8F56B98.sub_key.8505CFE38CF47C8CBCF0C9D7FC47DBB5210FB475.independent.secret.asc
```

You can also test the export by importing the same key in a different keyring. GPG works with a directory where it stores all the keys. That directory is called `homedir`. You can change the `homedir` using a command option `--homedir`. So you can create a new directory and import the key backup there:

```s
$ mkdir /tmp/import-only-subkey && cd /tmp/import-only-subkey
$ gpg --homedir=/tmp/import-only-subkey --import ~/Backups/primary_key.9DE332FB25C8A2D6EDF9DE8EB5B58E7CA8F56B98.sub_key.8505CFE38CF47C8CBCF0C9D7FC47DBB5210FB475.independent.secret.asc
gpg: key B5B58E7CA8F56B98: public key "Test User <test.user@example.com>" imported
gpg: To migrate 'secring.gpg', with each smartcard, run: gpg --card-status
gpg: key B5B58E7CA8F56B98: secret key imported
gpg: Total number processed: 1
gpg:               imported: 1
gpg:       secret keys read: 1
gpg:   secret keys imported: 1
```

If you list the private keys in that keyring you will see that the key still contains the public key for the primary key, even thought we only exported the subkey. But it does not include the other encryption subkey:

```s
$ gpg --homedir=/tmp/import-only-subkey -k --with-subkey-fingerprint 8505CFE38CF47C8CBCF0C9D7FC47DBB5210FB475
pub   rsa3072 2022-03-03 [C]
      9DE332FB25C8A2D6EDF9DE8EB5B58E7CA8F56B98
uid           [ unknown] Test User <test.user@example.com>
sub   rsa3072 2022-03-03 [S]
      8505CFE38CF47C8CBCF0C9D7FC47DBB5210FB475
```

Now you can export it again and use it whenever you want to use it independently from the primary key. For example, as a GitHub secret used in a CI/CD process.

## How to change the passphrase for the subkey

In the process above you have to use the same passphrase for the original key and the independent subkey. Unfortunately you can not change the passphrase when you export the key. If you want to use a different passphrase for the subkey you have to change it later.

```s
$ gpg --homedir=/tmp/import-only-subkey --edit-key 8505CFE38CF47C8CBCF0C9D7FC47DBB5210FB475
gpg (GnuPG) 2.2.20; Copyright (C) 2020 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret subkeys are available.

pub  rsa3072/B5B58E7CA8F56B98
     created: 2022-03-03  expires: never       usage: C   
     trust: unknown       validity: unknown
ssb  rsa3072/FC47DBB5210FB475
     created: 2022-03-03  expires: never       usage: S   
[ unknown] (1). Test User <test.user@example.com>
gpg> passwd
gpg: key B5B58E7CA8F56B98/B5B58E7CA8F56B98: error changing passphrase: No secret key

gpg> save
Key not changed so no update needed.
```

You have to enter the current password and the new one twice. You will get this error:

```s
gpg: key B5B58E7CA8F56B98/B5B58E7CA8F56B98: error changing passphrase: No secret key
```

That means GPG did not change the passphrase for the primary key, because there was not any private key for the primary key, but the passphrase was in fact changed.

If you try to change the passphrase again with `passwd` you will see that the old password does not work anymore.

[Back to home](./index.md)
