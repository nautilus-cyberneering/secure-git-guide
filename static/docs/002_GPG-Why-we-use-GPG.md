# GPG - Why we use GPG

![HEADER IMAGE](./media/HEADER/GitHub-Repo-SecureGitGuide-ART-002.jpg)

The main reason to use GPG in our daily work is to make sure that our contributions to the company's repositories belong to one of its active contributors or maintainers.

We use it to prevent impersonation attacks.

![Impersonation Attack](https://nautilus-cyberneering.de/wp-content/uploads/2022/01/impersonation-attack-1024x576.png)

GPG is one of the options for commit signing used with GitHub.

It is the preferred option by our team members.

## Why to use it?

![PROS&CONS](https://nautilus-cyberneering.de/wp-content/uploads/2022/01/Pro-Con-1500x600-1-1024x410.png)

### Advantages

For authentication, certification and signing the receiving end can verify that the content is from you, having your public key. On the other hand, if the other party wants to send you something only for you, they will use your public encryption key in combination with their own private key to sign and certify the content. Such content can only be decrypted using your own private key, and by having their public key you will know that it is from them.

***First advantage:***

If someone sends to you something encrypted, meant only for you, you will be the only one capable of decrypting it.

>"All right but why now, so many keys?"

Essentially there is one primary key, which is typically used only for signing and certification, and a subkey signed by the primary key for encryption. However, you can have one for each of the usages if you wanted.

***Second advantage:***

 Each subkey when used is transmitted at the same time, but if compromised it can be revoked individually and a new key generated while keeping your primary key valid. It makes it easier to manage your keys and split them for the different purposes you want to use them.

### Disadvantage

If your primary key is compromised or you lose it, your security has been breached and someone can impersonate you or you lose access to your digital "id" stemming from this key.... you may have to start building your digital reputation from scratch.

Nevertheless, for this situation there exists the **revocation certificate** which is created from the start at the same time that you create your keys for the first time or at any given time for the individual keys you want to revoke. With this certificate you would have to go to the before mentioned GPG key servers and upload it, to publicly revoke the affected key or keys.

[Back to home](./index.md)
