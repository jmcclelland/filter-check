# Filter Check

This program helps you determine if mail sent by your email server is landing in the spam or junk folder of the major email providers.

It will send email via any service you have configured and check email via any service you have configured and report if the message was received and if so, if it landed in the inbox or spam folder.

## Quick Start

Copy filter-check.conf to ~/.filter-check.conf. Edit the configuration file. More configuration documentation is in the sample configuration file.

Then run: `filter-check`

All settings in the [main] section of the configuration file can be overridden on the command line.

## Usage

    usage: filter-check [-h] [--sendvia SENDVIA] [--sendto SENDTO] [--quiet]
                        [--gtube] [--headers] [--sleep SLEEP]
                        [--emailfrom EMAILFROM] [--subject SUBJECT] [--msg MSG]
                        [--sendonly] [--fetchfrom FETCHFROM]
                        [--messageid MESSAGEID]

    Check deliverability to various mail providers. Configuration file is in
    ~/.filter-check.conf

    optional arguments:
      -h, --help            show this help message and exit
      --sendvia SENDVIA     the host to relay the email
      --sendto SENDTO       the host to send the email
      --quiet               surpress all output, use exit codes only
      --gtube               use spamassassin code to identify the message as spam
                            (for debugging)
      --headers             output full headers, instead of brief headers
      --sleep SLEEP         number of seconds to wait between sending and checking
                            email
      --emailfrom EMAILFROM
                            send the test message from this email
      --subject SUBJECT     specify the subject for the test message
      --msg MSG             specify the body of the test message
      --sendonly            don't fetch the message, only send it and output the
                            message-id sent
      --fetchfrom FETCHFROM
                            don't send a message, only fetch a message from this
                            host matching the passed messageid
      --messageid MESSAGEID
                            when using fetchfrom, fetch the messageid specified in
                            this option

    Exit codes: 0 if message is found in Inbox, 1 if found in spam box, 2 if not
    delivered, 255 if error.
     
## Examples

With a minimally set configuration file, you can send and check the message you sent via a single invocation:

    filter-check

If you want to set it via a cron job/nagios - you can specify --quiet so you only get an exit code. 0 means success (found in inbox). 1 means found in spambox. 2 means not found. And 255 means there was an error:

    filter-check --quiet
    echo $?

If you are getting message not found errors, try setting a longer time to sleep between sending and receiving (e.g. 5 minutes):

    filter-check --sleep 300

If you want to check messages sent from a variety of different email addresses (maybe a missing SPF record is the problem?):

    filter-check --emailfrom joe@example.org
    filter-check --emailfrom lucia@example.net

Maybe you have your email server configured in a [mayfirst] section of your configuration file and both a [yahoo] and a [gmail] section configured in your configuration file and you want to check both:

    filter-check --sendvia mayfirst --sendto gmail
    filter-check --sendvia mayfirst --sendto yahoo

Maybe you want to use batch processing so your script runs more quickly. First, send all your messages:

    gmail_msg_id=$(filter-check --sendvia mayfirst --sendto gmail --sendonly --quiet)
    yahoo_msg_id=$(filter-check --sendvia mayfirst --sendto yahoo --sendonly --quiet)
    outlook_msg_id=$(filter-check --sendvia mayfirst --sendto outlook --sendonly --quiet)
    sleep 600
    filter-check --fetchfrom gmail --messageid=$gmail_msg_id
    filter-check --fetchfrom yahoo --messageid=$yahoo_msg_id
    filter-check --fetchfrom outlook --messageid=$outlook_msg_id

## Notes

For gmail to work, you have to configure your account to allow logins from "less secure apps" (see https://support.google.com/accounts/answer/6010255?hl=en). I have no idea what that means, but you can't login to gmail via this program with that setting enabled.

Yahoo requires you to enable insecure apps via https://login.yahoo.com/account/security#other-apps.
