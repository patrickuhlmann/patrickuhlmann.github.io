---
layout: post
title:  "Nextcloud encryption is dangerous"
date:   2022-05-16 10:36:00 +0200
categories: nextcloud
---

# What happened

Recently I decided to maintain an old Nextcloud installation of mine. I moved it to a new server and upgraded it from version 20 to 24 (step by step). I also enabled the groupfolders feature and moved some folders and files from a user to the new groupfolders.

At first everything seemed to work. After a while I got some complaints about files that could not be opened with the web interface. Upon further investigation I saw that the file was encrypted (this is quite easy to see as its content starts with "HBEGIN:oc_encryption_"). There were more and more reports about such files.

# Analysis

I decided to disable the encryption and to decrypt everything. Running the command was strange because you can't run it when you disable the encryption or go into maintenance mode. The command run successfully but still most of the files were encrypted. Luckily I found [decrypt-all-files.php](https://github.com/syseleven/nextcloud-tools) that was able to decrypt most files in the user folders.

There was still an issue with the files in the group folders. The group folder oficially [does not support encryption](https://github.com/nextcloud/groupfolders/issues/912). Still the files were there and what was worse was that there was no key material.

The way how the encryption works is that there is a key generated for every file that is encrypted by a master key. Decryption is not possible whithout the key per file. In the data folder you find a folder files which contains the files and a folder files_encryption that contains the key material per file.

In my case I had to restore a backup to get the encrypted files with their key material in the user folder and then could use nextcloud-tools to decrypt them.

During my investigation I also saw that there were many keys present for old files that have already been removed. Also when I decrypted all files it didn't remove the key material.

# Decryption/Restoration process

Decrypt all files:
```
php occ encryption:decrypt-all
```

Disable the encryption:
```
php occ encryption:disable
```

Check if there are still any encrypted files left (and get a list of them):
```
find /nextcloud_data/ -type f -exec sh -c 'head -c27 "$1" |grep HBEGIN:oc_encryption_ 1>/dev/null && echo "$1"' sh {} \;
```

Download the [decrypt-all-files.php](https://github.com/syseleven/nextcloud-tools/tree/master/rescue) script. You need to configure it. Mainly by setting the path ot the datadirectory, the instance id and the secret which can all be found in the config.php file.
```
./decrypt-all-files.php /restored "/nextcloud_data/still_encrypted_files"
```

If you have encrypted files in a group folder you can't decrypt them there. If you moved them from a user folder, you can restore the backup of that user folder and decrypt them there.

Check and move the files from /restored to the proper location. I found it easiest to do this on a desktop computer with an installed Nextcloud client and then let nextcloud do the syncing.

# Conclusion

In summary I have a pretty bad experience with encryption. 

The following points bothered me:
 * the command to disable the encryption and decrypt all files can't be run in maintenance mode
 * it allowed to move the encrypted files from an encrypted folder to an unencrypted group folder. During the process it didn't decrypt the files but deleted the key material making the files unrecoverable
 * the decryption process didn't indicate any error message but let lots of files encrypted. Obviously the files were decryptable because nextcloud-tools was able to do so
 * the key management doesn't seem to work well as there are keys left behind for old files that were deleted and also when decrypting all files.
 * there is barely any useful help from the official nextcloud. No instructions/tools to rescue/restore

 Fortunately the decrypt-all-files.php tool exists which is extremely helpful