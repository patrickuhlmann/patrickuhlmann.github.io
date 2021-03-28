---
layout: post
title:  "Nokia 3310 3G - Import Contacts from Nextcloud"
date:   2021-03-20 15:00:33 +0100
categories: nokia 3310 nextcloud
---
I bought a Nokia 3310 3G. The first challenge was already to import my contacts from Nextcloud. After some research in the internet I have succeeded. Here you find a list of the steps:

1. Login in the nextcloud webinterface and go to contacts
2. Click on All contacts and then on settings
3. Choose download. This will save a *.vcf file with all your
contacts. The format of the vcf file is version 3.0/4.0 (depending on your version), however the nokia 3310 supports only version 2.1. Thus a conversion is needed. I wrote a small utility to do this. You find the utility and a description how to execute it here: <a href="https://github.com/patrickuhlmann/nokia3310contacts">https://github.com/patrickuhlmann/nokia3310contacts</a></p>
4. Use the utility to convert the downloaded file to the 2.1 format
5. Connect the phone with an usb cable and choose storage
6. Put the file in the 2.1 format on the external card in the folder vCard
7. optional: delete all existing contacts
8. Use the File application on the phone to locate the file. Against intuition, you don’t need to select open but “save vCard”. The process is fast and unspectacular (you don’t see a message at all).
9. Open the contact app again and enjoy your contacts :D

Sources from research:
  * https://stackoverflow.com/questions/54009980/parsing-converting-nokia-smart-feature-os-backup-ib-files/54060829
  * https://github.com/mangstadt/ez-vcard
  * https://dba.ovh/vcf222.html
  * https://nokiapoweruser.com/how-to-import-contacts-on-nokia-215-220-225-230/
  * https://superuser.com/questions/1461970/import-vcard-vcf-file-to-nokia-3310-3g-phone
