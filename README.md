## Index

- [Docker and CentOS's firewalld service fix](DOCKER.md#centoss-firewalld-service-fix)
- [Docker and SELinux](DOCKER.md#optional-enable-selinux)
- [Enhance Email Security](EMAIL.md)
- [Prevent running process from randomly die](SWAP.md)
- [Web Server Best Practices](WEBSERVER.md)

## How To Write Proper Git Commit Message

Reference: https://medium.com/@steveamaza/how-to-write-a-proper-git-commit-message-e028865e5791

> Why is this change necessary?
> How does this commit address the issue?
> What effects does this change have?

## Signing commits with GPG

Reference:
- https://help.github.com/en/github/authenticating-to-github/generating-a-new-gpg-key
- https://help.github.com/en/github/authenticating-to-github/signing-commits

Using GPG or S/MIME, you can sign tags and commits locally. These tags or commits are marked as verified on GitHub so other people can trust that the changes come from a trusted source.

- Generate the GPG key

  ```
  $ gpg --full-generate-key
  ```

- Export the GPG public key and upload it to GitHub

  ```
  $ gpg --armor --export <keyid>
  ```
  
- Configure the GitHub Dekstop

  ```
  $ gpg --list-secret-keys --keyid-format LONG
  $ git config --global user.signingkey <keyid>
  
  # To sign all commits by default in any local repository on your computer, run
  $ git config --global commit.gpgsign true
  ```

### Fix for issue "gpg: skipped "keyid": No secret key" on Windows 10

```
$ git commit -S -m "first commit"
gpg: skipped "keyid": No secret key
gpg: signing failed: No secret key
error: gpg failed to sign the data
fatal: failed to write commit object
```

Run this `$ git config --global gpg.program "C:\Program Files (x86)\GnuPG\bin\gpg.exe"`

## A template to make good README.md

Reference:
- https://gist.github.com/PurpleBooth/109311bb0361f32d87a2
- https://gist.github.com/PurpleBooth/b24679402957c63ec426 for details on our code of conduct, and the process for submitting pull requests to us.

## "Premature Optimization" and paranoid security measures

Reference: ["Premature Optimization" and paranoid security measures](https://stackoverflow.com/questions/12543607/prevent-session-cookie-hijacking-without-ssl/12545243#12545243)

> From my experience and from what I have read, I agree with the statement that "premature optimization is [one of the greatest evils of programming]". Focus your time on building a site that works and is useful for people and then, if something start to go wrong, think about getting an SSL certificate. You just don't have the money for the certificate and you don't have the money because your site has not yet been successful (so do that first).

> "Premature Optimization" and paranoid security measures are temptations for programmers because we like to think of ourselves as working on large-scale, very important projects when we are not [yet].

## Server Best Practices

- Use non-root user with sudo privileges and SSH

  ```
  $ useradd [user]
  $ passwd [user]


  # Add [user] to the wheel group (sudo privileges)
  # ---
  $ gpasswd -a [user] wheel


  # Login as the non-root user
  # ---
  $ su [user]


  # Save the public key
  # ---
  $ mkdir ~/.ssh/
  $ nano ~/.ssh/authorized_keys

  # Change the folder and the file access permissions.
  # chmod 700 = Only owner can read, write and execute.
  # chmod 600 = Only owner can read and execute.
  # Reference: https://www.thinkplexx.com/learn/article/unix/command/chmod-permissions-flags-explained-600-0600-700-777-100-etc
  # ---
  $ chmod 700 ~/.ssh/
  $ chmod 600 ~/.ssh/authorized_keys
  ```

- Disable Root Login

  ```
  $ sudo sed -i '/^PermitRootLogin/s/yes/no/' /etc/ssh/sshd_config
  $ sudo systemctl reload sshd
  ```

- Disable Password Based Login

  ```
  $ sudo sed -i '/^ChallengeResponseAuthentication/s/yes/no/' /etc/ssh/sshd_config
  $ sudo sed -i '/^PasswordAuthentication/s/yes/no/' /etc/ssh/sshd_config
  $ sudo sed -i '/^UsePAM/s/yes/no/' /etc/ssh/sshd_config
  $ sudo systemctl reload sshd
  ```
  
- Secure the SSH connections

  Reference: https://unix.stackexchange.com/questions/333728/ssh-how-to-disable-weak-ciphers

  - To disable RC4 and use secure ciphers on SSH server. In `/etc/ssh/sshd_config` set
  
    ```
    Ciphers aes256-ctr,aes192-ctr,aes128-ctr

    HostKeyAlgorithms ssh-ed25519,ecdsa-sha2-nistp521,ecdsa-sha2-nistp384,ecdsa-sha2-nistp256,ssh-rsa

    KexAlgorithms ecdh-sha2-nistp521,ecdh-sha2-nistp384,ecdh-sha2-nistp256,diffie-hellman-group-exchange-sha256,curve25519-sha256@libssh.org

    MACs hmac-sha2-512,hmac-sha2-256
    ```
  
  - Instruct your SSH client to negotiate only secure ciphers with remote servers. In `/etc/ssh/ssh_config` set:
  
    ```
    Ciphers aes256-ctr,aes192-ctr,aes128-ctr
    MACs hmac-sha2-512,hmac-sha2-256
    ```

- Set the timezone to UTC

  ```
  # Set the timezone to UTC
  # ---
  $ sudo timedatectl set-timezone UTC
  $ sudo timedatectl set-local-rtc 0


  # Restart the Rsyslog's service
  # ---
  $ sudo systemctl restart rsyslog
  ```
