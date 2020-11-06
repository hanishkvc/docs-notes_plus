################################
Getting mails from commandline
################################
Author: HanishKVC
Version: 20201106IST1519


Create a GPG Id/KeyPair
########################

Setup a gpg public-private key pair to use for securing the password store
which will contain the user's email passwords.

gpg --generate-key

realname: AUserId4PassStore OR userid
email: user@world

gpg --list-keys

Misc
======

Delete a key

	gpg --delete-keys UserId

Encrypt a file

	gpg --encrypt --recipient UserIdOfPersonWhoCanDecrypt --sign TheFile2Encrypt

	One should have the public key of the Recipient.

Decrypt the file

	gpg --decrypt TheEncryptedFile
        gpg --output decryptedFileName --decrypt TheEncryptedFile

Verify

        gpg --verify TheEncryptedFile

Misc

        gpgtar --create --encrypt --symmetric --output testhkvc hkvc


Setup the pass store
#####################

Use the unix program pass to store the email passwords in a secure way.
It will internally use gpg to secure the password store.

apt install pass

Initialise pass to use a given gpgKey (gpgidRealName) to secure things

	pass init AUserID4PassStore

Store the email password of the user at domain my.domain

	pass insert user@email.my.domain

	NOTE: Prefixing of the domain with email is just a convention
	to easily identify what that password stored in pass represents.
	One is free to use what ever id or tag or name to identify the
	password being stored / inserted into pass.

pass ls


Using GetMail
###############

Setup GetMail
===============

Use getmail to fetch mails from email servers. The password for the email accounts is stored-using/got-from pass. 

install getmail
-----------------

sudo apt install getmail python2

configure getmail
-------------------

create a .getmail folder

	mkdir ~/.getmail

set .getmail mode to 0700

	chmod 0700 ~/.getmail

create .getmail/getmailrc

	[retriever]
	type = SimplePOP3SSLRetriever
	server = pop.email.server
	username = user@my.domain
	port = 995
	password_command = ("/usr/bin/pass", "show", "user@email.my.domain")

	[destination]
	type = Maildir
	path = ~/mail/


Fetch emails
==============

getmail

getmail --new

By default getmail doesnt remove emails from the server.


Using FDM
###########

This can both fetch as well as filter and organise the emails.

Setup fdm
==========

install fdm
-------------

sudo apt install fdm

configure fdm
---------------

create a .fdm.conf file in your home directory with details about your emails
as well as any filtering you may want to do wrt your emails.

	set maximum-size 128M

	action "inbox" maildir "%h/Mail/INBOX"
	action "group1" maildir "%h/Mail/INBOX/Grouping/Group1"

	account "pop3s" pop3s server "pop.my.email.server" port 995
        	user "user@my.domain" pass $(/usr/bin/pass show user@email.my.domain)

	match "^(To|cc|Sender):.*@.*\.group1.com" action "group1"

	match all action "inbox"

Ensure the .fdm.conf has a 0600 file mode.

Fetch emails
=============

To fetch emails from the server, run

	fdm fetch

This deletes the emails from the server.

If you want to keep emails in the server, even after fetching it, use

	fdm -k fetch

Using mutt
############

.mutt/muttrc
--------------

set mbox_type=Maildir
set folder=~/Mail/INBOX
set spoolfile=+/

set mailcap_path = ~/.mutt/mailcap

auto_view text/html

alternative_order text/plain text/html

.mutt/mailcap
---------------

text/html; w3m -I %{charset} -T text/html; copiousoutput;


