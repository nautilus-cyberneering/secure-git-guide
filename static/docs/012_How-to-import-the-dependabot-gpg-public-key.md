# How to import the dependabot GPG public-key

![HEADER IMAGE](./media/HEADER/GitHub-Repo-SecureGitGuide-ART-012.jpg)

THe GitHub [dependabot](https://github.com/dependabot) is a _GitHub dependency management tool that allows you to manage your dependencies in a way that is easy to use and easy to understand_.

The dependabot checks your dependencies regularly and if it finds any new dependencies it will automatically add them to your repository. This is a great way to keep your dependencies up to date and secure. The dependabot is a _GitHub app_ that runs on your GitHub repository. It creates a new pull request to update your dependencies.

It updates not only you language dependencies but also the action you use in your workflows.

A commit from the dependabot looks like this:

```s
git show --show-signature  1ae99046a1e9da0de09cf02ec383a9433acf0665
commit 1ae99046a1e9da0de09cf02ec383a9433acf0665
gpg: Signature made mar 07 jun 2022 01:35:52 WEST
gpg:                using RSA key 4AEE18F83AFDEB23
gpg: Can't check signature: No public key
Author: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com>
Date:   Tue Jun 7 00:35:52 2022 +0000

    build(deps): bump actions/setup-node from 3.2.0 to 3.3.0
    
    Bumps [actions/setup-node](https://github.com/actions/setup-node) from 3.2.0 to 3.3.0.
    - [Release notes](https://github.com/actions/setup-node/releases)
    - [Commits](https://github.com/actions/setup-node/compare/v3.2.0...v3.3.0)
    
    ---
    updated-dependencies:
    - dependency-name: actions/setup-node
      dependency-type: direct:production
      update-type: version-update:semver-minor
    ...
    
    Signed-off-by: dependabot[bot] <support@github.com>
```

As you can see in the line `gpg: Can't check signature: No public key`, this is a _GPG error_ that means that the dependabot's public GPG key is not installed on your machine.

You can import the public key by executing this command:

```s
curl https://github.com/web-flow.gpg | gpg --import
gpg -k 4AEE18F83AFDEB23
```

If you run the previous command again you will see the following output:

```s
git show --show-signature  1ae99046a1e9da0de09cf02ec383a9433acf0665
commit 1ae99046a1e9da0de09cf02ec383a9433acf0665
gpg: Signature made mar 07 jun 2022 01:35:52 WEST
gpg:                using RSA key 4AEE18F83AFDEB23
gpg: Good signature from "GitHub (web-flow commit signing) <noreply@github.com>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 5DE3 E050 9C47 EA3C F04A  42D3 4AEE 18F8 3AFD EB23
Author: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com>
Date:   Tue Jun 7 00:35:52 2022 +0000

...
```

This time you see this message: `gpg: Good signature from "GitHub (web-flow commit signing) ...`

In order to remove that message you have to trust that key.

```s
$ gpg --edit-key 5DE3E0509C47EA3CF04A42D34AEE18F83AFDEB23
gpg (GnuPG) 2.2.20; Copyright (C) 2020 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.


pub  rsa2048/4AEE18F83AFDEB23
     created: 2017-08-16  expires: never       usage: SC  
     trust: unknown       validity: unknown
[ unknown] (1). GitHub (web-flow commit signing) <noreply@github.com>

gpg> trust
pub  rsa2048/4AEE18F83AFDEB23
     created: 2017-08-16  expires: never       usage: SC  
     trust: unknown       validity: unknown
[ unknown] (1). GitHub (web-flow commit signing) <noreply@github.com>

Please decide how far you trust this user to correctly verify other users' keys
(by looking at passports, checking fingerprints from different sources, etc.)

  1 = I don't know or won't say
  2 = I do NOT trust
  3 = I trust marginally
  4 = I trust fully
  5 = I trust ultimately
  m = back to the main menu

Your decision? 5
Do you really want to set this key to ultimate trust? (y/N) y

pub  rsa2048/4AEE18F83AFDEB23
     created: 2017-08-16  expires: never       usage: SC  
     trust: ultimate      validity: unknown
[ unknown] (1). GitHub (web-flow commit signing) <noreply@github.com>
Please note that the shown key validity is not necessarily correct
unless you restart the program.

gpg> quit
```

Finally, if you show the commit signature again you will see:

```s
$ git show --show-signature  1ae99046a1e9da0de09cf02ec383a9433acf0665
commit 1ae99046a1e9da0de09cf02ec383a9433acf0665
gpg: Signature made mar 07 jun 2022 01:35:52 WEST
gpg:                using RSA key 4AEE18F83AFDEB23
gpg: Good signature from "GitHub (web-flow commit signing) <noreply@github.com>" [ultimate]
Author: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com>
Date:   Tue Jun 7 00:35:52 2022 +0000

...
```

If you want to know more [about validating other keys on your public keyring](https://www.gnupg.org/gph/en/manual/x334.html) you can read the [GPG documentation](https://www.gnupg.org/gph/en/manual/x334.html).

## Links

- [Validating other keys on your public keyring](https://www.gnupg.org/gph/en/manual/x334.html)

[Back to home](./index.md)
