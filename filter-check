#!/usr/bin/python

import smtplib
import sys
import imaplib
import time
import re
import ConfigParser
from email.parser import HeaderParser
from os.path import expanduser


def main(argv = None):
    check = filter_check()
    check.init()
    check.run()

class filter_check:
    subject = "Meeting next week"
    msg = "Hi all,\nDon't forget, the next meeting is on Wendesday at 8:00 pm.\n\nSee you there,\njamie"
    quiet = False
    gtube = False
    headers = False
    from_address = ""
    to_address = ""
    imap_user = ""
    imap_pass = ""
    imap_host = ""
    imap_spam_box = ""
    headers_regex = "" 
    smtp_user = ""
    smtp_pass = ""
    smtp_host = ""

    found = False 

    def init(self):
      config = ConfigParser.ConfigParser()
      home = expanduser("~")
      config.readfp(open(home + '/.filter-check.conf'))

      self.headers = config.getboolean("main", "headers")
      self.quiet = config.getboolean("main", "quiet")
      self.gtube = config.getboolean("main", "gtube")
      self.from_address = config.get("main", "from_address")

      send_to = config.get("main", "send_to")
      send_via = config.get("main", "send_via")

      self.to_address = config.get(send_to, "to_address")
      self.imap_user = config.get(send_to, "imap_user") 
      self.imap_pass = config.get(send_to, "imap_pass") 
      self.imap_host = config.get(send_to, "imap_host") 
      self.imap_spam_box = config.get(send_to, "imap_spam_box") 
      self.headers_regex = config.get(send_to, "headers_regex") 


      self.smtp_user = config.get(send_via, "smtp_user")
      self.smtp_pass = config.get(send_via, "smtp_pass")
      self.smtp_host = config.get(send_via, "smtp_host")

      if self.gtube == True:
          self.msg = "XJS*C4JDBQADN1.NSBN3*2IDNEN*GTUBE-STANDARD-ANTI-UBE-TEST-EMAIL*C.34X"

    def run(self):
      # Send the message.
      mail = "Subject: {0}\r\nFrom: {1}\r\nTo: {2}\r\n\r\n{3}".format(self.subject, self.from_address, self.to_address, self.msg)
      server = smtplib.SMTP_SSL(self.smtp_host)
      server.login(self.smtp_user, self.smtp_pass)
      #server.set_debuglevel(1)
      server.sendmail(self.from_address, self.to_address, mail)
      server.quit()

      # Give the message some time to be delivered.
      print("Sleeping for 10 seconds.")
      time.sleep(10)

      # Login to check for it.
      M = imaplib.IMAP4_SSL(self.imap_host)
      M.login(self.imap_user, self.imap_pass)
      # Uncomment to trouble shoot where the spam box is.
      #print M.namespace()
      #print M.list()
      
      reg = re.compile(self.headers_regex)

      # Try INBOX first.
      messages = []
      count = M.select()
      if count > 0:
          typ, data = M.search(None, 'Subject', self.subject)
          messages = data[0]

      if len(messages) > 0:
          self.found = 'Inbox'
      else:
          # Try spam box.
          count = M.select(self.imap_spam_box)
          if count > 0:
              typ, data = M.search(None, 'Subject', self.subject)
              messages = data[0]
              if len(messages) > 0:
                  self.found = 'Spam'

      if self.found == False:
          print "Found in: NOT FOUND"
      else:
          print "Found in: {0}\n".format(self.found)
      # Iterate over each message found
      for num in messages.split():
          typ, data = M.fetch(num, '(RFC822)')
          parser = HeaderParser()
          headers = parser.parsestr(data[0][1])
          for key in headers.keys():
              if self.headers == True or reg.match(key):
                  print "{0}: {1}".format(key, headers.get(key))

          M.store(num, '+FLAGS', '\\Deleted')

      M.expunge()
      M.close()
      M.logout()

if __name__ == "__main__":
    sys.exit(main())
