# Filter Check

This program helps you determine if mail sent by your email server is landing in the spam or junk folder of the major email providers.

It will send email via any service you have configured and check email via any service you have configured and report if the message was received and if so, if it landed in the inbox or spam folder.

## Quick Start

Copy filter-check.conf to ~/.filter-check.conf. Edit the configuration file.

Then run: `filter-check`

All settings in the [main] section of the configuration file can be overridden on the command line.

## Usage

		usage: filter-check [-h] [--sendvia SENDVIA] [--sendto SENDTO] [--quiet]
                    [--gtube] [--headers] [--sleep SLEEP]

		Check deliverability to various mail providers. Configuration file is in ~/.filter-check.conf

		optional arguments:
			-h, --help         show this help message and exit
			--sendvia SENDVIA  the host to relay the email
			--sendto SENDTO    the host to send the email
			--quiet            surpress all output, use exit codes only
			--gtube            use spamassassin code to identify the message as spam
												 (for debugging)
			--headers          output full headers
			--sleep SLEEP      number of seconds to wait between sending and checking
												 email

		Exit codes: 0 if message is found in Inbox, 1 if found in spam box, 3 if not
		delivered, 255 if error.
 
## Notes

For gmail to work, you have to configure your account to allow logins from "less secure apps" (see https://support.google.com/accounts/answer/6010255?hl=en). I have no idea what that means, but you can't login to gmail via this program with that setting enabled.

Yahoo still not woring (due to search error - see https://stackoverflow.com/questions/29012718/imap-search-not-working-with-yahoo. However... Yahoo also requires you t, enable insecure apps via  https://login.yahoo.com/account/security#other-apps
