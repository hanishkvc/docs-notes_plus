===============
GitHub Notes
===============

Author: HanishKVC
Version: 20200824IST1504


Using ssh with github
=======================


Generate key-pair
-------------------

Generate a new key pair for use with github

    ssh-keygen -t rsa -b 4096 -C "your_email@example.com-github"

    Save the key-pair has id_rsa.github and id_rsa.github.pub. This is usually saved in ~/.ssh/

    NOTE: Dont forget to set a strong passphrase for your private key

    NOTE: Generate additional key-pairs if you want to use different sets for different repositories or groups of repositories or so.


Setup for local use
--------------------

Either use ssh-agent or .ssh/config to use this key-pair with git and inturn github

Using ssh-agent
~~~~~~~~~~~~~~~~

Run ssh-agent in your shell / terminal session and let it setup the environment variables to allow ssh to use it.

    eval `ssh-agent -s`

Make ssh-agent aware of your new private key from the above key-pair

    ssh-add ~/.ssh/id_rsa.github


Using .ssh/config
~~~~~~~~~~~~~~~~~~

Add a entry for Host github.com

    ::

        Host github.com
            User git
            IdentityFile ~/.ssh/id_rsa.github

In this case one can use

    git clone git@github.com:YourGithubUserName/YourGithubRepository.git

You can even add additional github ssh credentials, which are seperate from above. One could use different credentials
for different subsets of their repositories or so.

    ::

        Host github-set02
            User git
            HostName github.com
            IdentityFile ~/.ssh/id_rsa.github_set02

In this case one needs to use

    git clone git@github-set02:YourGithubUserName/YourOtherGithubRepository.git


Configure Ur Github Account
-----------------------------

Add your public key(s) from above key-pair(s) to your github account

    YourGithubAccount -> Settings -> SSH and GPG Keys -> New SSH key

        Paste your public key content into the edit box shown

        Click on the Add SSH key button


    Verify the MD5 finger print shown by github wrt the added SSH Key, with the one in your local machine.

        ssh-add -l -E md5


Misc
-----

Verify SSH based access to Github

    ssh -vT git@github.com


In case you want to change your private key passphrase, do the following

    ssh-keygen -p -f ~/.ssh/YourPrivateKey

    when prompted enter the old and the new passphrases.



New Github Repository
======================

If you want to setup a github repository for a existing local git repository

Local git repository
-----------------------

On your local machine setup your local git repository

    git init

    git add test01.txt

    git diff

    git commit

Create Github repository
--------------------------

Login to your github account and create a repository on github.

Link git and github repository
---------------------------------

Next link your local git repository to the github repository using the following commands.


    git remote add origin git@github.com:YourUserName/YourGithubRepositoryName.git

    git push -u origin master


From now on, one can just do

    git push

    git push origin someOtherBranch

    git push origin someTag

    git pull

    etc...


# vim: set sts=4 expandtab: #
