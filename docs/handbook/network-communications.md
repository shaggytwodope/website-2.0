<!--

The contents of this Documentation are subject to the Public Documentation License Version 1.01
 (the "License"); you may only use this Documentation if you comply with the terms of this License.
A copy of the License is available at http://illumos.org/license/PDL.


The Original Documentation is _________________.

The Initial Writer of the Original Documentation is ___________ Copyright (C)_________[Insert year(s)].
All Rights Reserved. (Initial Writer contact(s):________________[Insert hyperlink/alias]).

Contributor(s): ______________________________________.

Portions created by ______ are Copyright (C)_________[Insert year(s)].
All Rights Reserved. (Contributor contact(s):________________[Insert hyperlink/alias]).

-->

# Network Communications - (Draft) - work in progress

## PPP

## PPPoE

## Email

## Firewalls

## Advanced Networking

## Crossbow


## Using OpenIndiana as a NAS

< place holder for section introduction content >


#### Configuring OpenIndiana as a CIFS (Samba) Server

##### Home NAS setup steps

* Get the hardware
* Assemble the hardware
* Install OpenIndiana
* Configure OpenIndiana
* Configure Windows

##### commands used

* `sharemgr` - configure and manage file sharing
* `smbadm` - configure and manage CIFS local groups and users, and manage domain membership
* `zfs` - configures ZFS file systems
* `passwd` - change login password and password attributes
* `chown` - change file ownership

<!--

For a variation of configuring a home NAS - this could be done virtually as well

* Running OI as a VMware EXSI guest
    * Local storage hardware is passed through to the OI guest and then shared via ISCSI, CIFS, NFS, etc.

For help writing this section, see the following OpenSolaris references:

* [Setting Up an OpenSolaris NAS Box](https://web.archive.org/web/20091008234550/http://developers.sun.com/openstorage/articles/opensolaris_nas.html)
* [Getting Started With the Solaris CIFS Service](https://web.archive.org/web/20091005070838/http://wiki.genunix.org/wiki/index.php/Getting_Started_With_the_Solaris_CIFS_Service)
* [How to enable guest access to a Solaris CIFS share](https://web.archive.org/web/20091021005616/http://blogs.sun.com/afshinsa/entry/how_to_enable_guest_access)
* [Solaris CIFS Service Troubleshooting](https://web.archive.org/web/20091126111451/http://wiki.genunix.org/wiki/index.php/Solaris_CIFS_Service_Troubleshooting)
* [What's New With Solaris CIFS](https://web.archive.org/web/20091124124935/http://wiki.genunix.org/wiki/index.php/What's_New_With_Solaris_CIFS)
* [CIFS Technical References](https://web.archive.org/web/20090725231658/http://wiki.genunix.org/wiki/index.php/CIFS_Technical_References)

Also have a look at the [OpenSolaris CIFS Administration Guide](https://docs.oracle.com/cd/E19120-01/open.solaris/820-2429/820-2429.pdf)

-->

Start by listing available storage pools.

```bash
# zfs list

NAME                           USED  AVAIL  REFER  MOUNTPOINT
storage                        498K   899G    19K  /storage

```

Create your ZFS dataset to be shared via CIFS/SMB.

```bash
# zfs create -o casesensitivity=mixed -o sharesmb=on storage/backup

```

Start the CIFS service.

```bash
# svcadm enable -r smb/server
```

Join the CIFS server to a workgroup.

```bash
# smbadm join -w WORKGROUP
```

Configure PAM authentication for the CIFS service.

```bash
# echo "other password required pam_smb_passwd.so.1 nowarn" >> /etc/pam.conf
```

Reset the password for the local user accounts which will be used for remotely accessing the CIFS/SMB share.

```bash
# passwd <user_account>
```

Set the share name to be used for the CIFS/SMB share.

```bash
# zfs set sharesmb=name=backup storage/backup
```

Change the ownership of ZFS dataset to the user account which will be used for remotely accessing the CIFS/SMB share.

```bash
# chown -R <user_account> /storage/backup
```

Verify everything is all set to go.

```bash
# sharemgr show -vp

default nfs=()
smb smb=()
        * /var/smb/cvol  smb=() ""
                  c$=/var/smb/cvol       smb=(abe="false" guestok="false")      "Default Share"
zfs smb=()
    zfs/storage/backup smb=()
          backup=/storage/backup
```

You can create additional CIFS datasets using the following 4 commands.

```bash
# zfs create -o casesensitivity=mixed -o sharesmb=on <pool_name/dataset_name>
# zfs set sharesmb=name=<new_share_name> <pool_name/dataset_name>
# chown -R <user_account> <path_to_dataset>
# sharemgr show -vp

```

##### Configuring CIFS/SMB linux client connectivity


Adding a remote share using the linux smbclient

* [Accessing an SMB Share With Linux Machines](http://www.tldp.org/HOWTO/SMB-HOWTO-8.html)

Adding a remote share using the KDE Dolphin file manager GUI

* In the left hand pane click _Network_
* In the right hand pane click _Add Network Folder_
* The Network Folder Wizard opens
* Select the radio button for _Microsoft Windows network drive_ and click next
* Specify a name for the share - can be anything - this is just a label
* Specify the remote CIFS/SMB server name (or IP address)
* Specify the share name of the remote CIFS/SMB share
* Click the save and connect button
* You'll be prompted for a remote username and password
* Ensure the checkbox is marked to save credentials or you'll be asked for everything you do.

Adding a remote share using a Windows client

* < place_holder >



#### Configuring OpenIndiana as an NFS Server

< Place holder for content >


#### Configuring OpenIndiana as an ISCSI Target Server -(COMSTAR)

< Place holder for content >


