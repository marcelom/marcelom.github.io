Title: Pocket Survival Guide: Netapp
Tags: Pocket Survival Guide, Netapp
Author: Marcelo Moreira
Summary: PSG for Netapp: Dont leave home without it...

This article is a draft, and needs amjor rework...

---------

Based on the original at: http://users.cis.fiu.edu/~tho01/psg/netapp.html

======= Basic Stuff =======

https://netapp.myco.com/na_admin 	# web gui URL.  Most feature avail there, including a console.

ssh netapp.myco.com			# ssh (or rsh, telnet in) for CLI access

get root mount of /vol/vol0/etc in a unix machine do to direct config on files.

NOW = NetApp Support Site
NetApp man pages ("mirror" by uyema.net)
RAID-DP

IMHO Admin Notes

Notes about NetApp export, NFS and Windows CIFS ACL permission issues.

Best practices is for most (if not all) export points of NFS server is to  
implement root_squash.  root on
the nfs client is translated to user 'nobody' and would effectively have
the lowest access permission.  This is done to reduce accidents of user
wiping out the whole NFS server content from their desktops.

Sometime NetApp NFS exports are actually on top of filesystem using windows NT ACL,
their file permission may show up as 777, but when it comes to accessing
the file, it will require authentication from the Windows server (PDC/BDC 
or AD).  Any user login name that does not have a match in
windows user DB will have permission denied problems.  

Most unix client with automount can access nfs server thru /net.
However, admin should discourage the heavy reliance on /net.  It is good
for occassional use.
/home/SHARE_NAME or other mount points should be 
provided, such as /corp-eng and /corp-it.  This is because mount path will 
be more controllable, and also avoid older AIX bug of accessing /net when 
accessing NFS mounted volumes, access them as user instead of root, which 
get most priviledges squashed away.
If the FS is accessible by Windows and Unix, it is best to make share name
simple and keep them consistent.  Some admin like to create
matching 
\\net-app-svr1\share1   /net-app-svr1/share1
\\net-app-svr2\share2   /net-app-svr2/share2
I would recommend that in the unix side, that /net-app-svr1 be unified into a
single automount map called like /project .  This would mean 
all share names need to be uniq across all servers, but it help keep
transparency that allows for server migration w/o affecting user's work
behaviour.


Old Filer to New Filer Migration problems:

If copy files from Unix FS to Windows-style FS, there are likely going to 
be pitfalls. NDMP would copy the files, and permissions and date would be 
preserved, but ownership of the files may not be preserved.  XCOPY from 
DOS (or robocopy) may work a tad better in the sense that the files will 
go thru the normal windows access of checking access and ownership 
creation. Clear Case needed to run chown on the files that correspond to 
the view, and not having the ownership preserved becomes a big problem.  
Ultimately, User that run CC script for ownership change was made part of 
the NetApp Local Admin Group.  A more refined ACL would be safer.

Filer data migration:

NDMP is the quickest.  One can even turn off NFS and CIFS access to ensure 
no one is writting to the server anymore.  NDMP is a different protocol 
with its own access mechanism.


Mixed NFS and CIFS security mode:

Mix mode security (NT and Unix) is typically a real pain in the rear.
Migrating from NT a/o Unix to mix mode would mean filer has to fabricate
permissions, which may have unintenteded side effects.
Switch from mixed mode to either NT or Unix just drop the extra permission
info, thus some consultant say this is a safer step.

Clear Case and NetApp each point the other as recommending Mixed Mode
security.  It maybe nighmare if really used.  Unix mode worked flawlessly for
3+ years.

Different NetApp support/consultant says different things about mix mode, 
but my own experience match this description:
Mix-Mode means the filer either store Unix or NTFS acl on a file by file basis.
If a given file (or dir) ACL is set on unix, it will get to have only Unix ACL on it.
If last set on NTFS, then it will get Windows ACL.  
The dual mode options is not both stored, only one of the two is stored, and the rest
resolved in real time by the filer.  
This has a nasty side effect that flipping security style from mixed mode to say NTFS,
some files permissions are left alone and even windows admin can't change/erase the files, 
because they are not seen as root.
In short, avoid mix-mode like a plague!!



LVM

Layers:

Qtree, and/or subdirectories, export-able
  |
Volume (TradVol, FlexVol), export-able, snapshot configured at this level.
  |
agregate (OnTap 7.0 and up)
  |
 plex      (relevant mostly in mirror conf)
  |
raid group
  |
disk


Disks	- Physical hardware device :)
	  Spares are global, auto replace failed disk in any raid group.
	  Sys will pick correct size spare.
	  If no hot spare avail, filer run in degraded mode if disk fail, and
	  def shutdown after 24 hours!  (options raid.timeout, in hours)

	  sysconfig -d		# display all disk and some sort of id
	  sysconfig -r		# contain info about usable and physical disk size
	  			# as well as which raid group the disk belongs to

	disk zero spare	 # zero all spare disk so they can be added quickly to a volume.
	vol status -s	 # check whether spare disks are zeroed 

	  web gui: Filer, Status 
	  	= display number of spares avail on system

	  web gui: Storage, Disk, Manage 
	  	= list of all disks, size, parity/data/spare/partner info, 
		  which vol the disk is being used for.
		  (raid group info is omited)

	  Disk Naming:  
	  .
	  2a.17		SCSI adaptor 2, disk scsi id 17
	  3b.97		SCSI adaptor 3, disk scsi id 97

	  a = the main channel, typically for filer normal use
	  b = secondary channel, typically hooked to partner's disk for takeover use only.



Raid group - a grouping of disks.  
 	Should really have hot spare, or else degraded mode if disk fail, and shut
	down in 24 hours by def (so can't tolerate weekend failure).

        max raid group size:
       		     raid4    raid-dp   (def/max)
	FC            8/14     16/28 
	SATA, R200    7/7      14/16

	Some models are slightly diff than above.


	Raid-DP?  
	2 parity disk per raid group instead of 1 in raid4.
	If you are going to have a large volume/aggregate that spans 2 raid group (in
	a single plex), then may as well use raid-dp.
	Larger raid group size save storage by saving parity disk.
	at expense of slightly less data safety in case of multi-disks failure.
 

Plex
	- mirrored volume/aggregate have two plexes, one for each complete copy of the
  	  data.
        - raid4/raid_dp has only one plex, raid groups are "serialized".


aggregate - OnTap 7.0 addition, layer b/w volume and disk.  With this, NA
	recommend creating a huge aggregate that span all disks with 
	same RAID level, then carve out as many volume as desired.


Volume - traditional mgnt unit, called an "independent file system".
     aka Traditional Volume, starting in OnTap 7.0
     Made up of one ore more raid groups. 
     -  disk(s) can be added to volume, default add to existing raid group
	in the vol, but if it is maxed out, then it will create a new raid
	group.
     - vol size can be expanded , but no strink, concat or split.
     - vol can be exported to another filer (foreign vol).
     - small vol implies small raid group, therefore waste more space.
     - max size = 250 GB recommended max vol size in 6.0

     vol status -v [vol0]	# display status of all [or specific] volume,
			        # -v gives all details on volume options
     vol lang    vol0	        # display [set] character set of a volume

     vol status -r		# display volume and raid status
     sysconfig  -r 		# same as vol status -r

     vol create newvol  14	# create new vol w/ 14 disks
     vol create newvol2 -t raid4 -r 14 6@136G
	 # vol size is 6 disks of 133 GB
	 # use raid4 (alt, use raid_dp)
	 # use raid group of 14 disks (def in cli), 
	 # each raid group need a parity disk, so
	 # larger raid group save space (at expense of ??)
	 # 28 disks usable in raid_dp only?


     vol add newvol2 3		# add 3 more disks to a volume
     vol options vol1 nosnap on	# turn off snapshot on a vol
     vol offline vol2
     vol online  vol2

FlexVol - OnTap 7.0 and up, resembles a TradVol, but build ontop of aggregate
    - grow and srink as needed


QTree 	- "Quota Tree", store security style config, oplock, disk space usage and file limits.
      Multiple qtrees per volume.  QTrees are not req, NA can hae simple/plain 
      subdir at the the "root level" in a vol, but such dir cannot be converted to qtree.
      Any files/dirs not explicitly under any qtree will be placed in a
      default/system QTree 0.

    qtree create   /vol/vol1/qtree1		# create a qtree under vol1
    qtree security /vol/vol1/qtree1	unix	# set unix security mode for the qtree
					        # could also be ntfs or mixed
    qtree oplocks  /vol/vol1/qtree1 enable	# enable oplock (windows access can perform catching) 


Config Approach

Aggregate:
Create largest aggregate, 1 per filer head is fine, unless need traditional vol.

Can create as many FlexVol as desired, since FlexVol can growth and srink as needed.
Max vol per aggregate = 100 ??

TradVol vs QTree?
- use fewer traditional volume when possible, since volume has parity disk overhead
- and space fragmentation problem.
- use QTree as size management unit.


FlexVol vs QTree?
- Use Volume for same "conceptual management unit"  
- Use diff vol to separate production data vs test data
- QTree should still be created under the volume instead of simple plain subdirectories
  at the "root" of the volume. 
  This way, quota can be turned on if just to monitor space usage.
- One FlexVol per Project is good.  Start Vol small and expand as needed.
  Strink as it dies off.
- Use QTree for different pieces of the same project.
- Depending on the backup approach, smaller volume may make backup easier.
  Should try to limit volume to 3 TB or less.



Quotas

mount root dir of the netapp volume in a unix or windows machine.
vi (/) etc/quotas   (in dos, use edit, not notepad!!)
then telnet to netapp server, issue command of quota resize vol1 .

quota on  vol1
quota off vol0
quota report
quota resize	# update/re-read quotas (per-vol)
		# for user quota creation, may need to turn quota off,on for volume
		# for changes to be parsed correctly.

Netapp quota support hard limit, threshold, and soft limit.
However, only hard limit return error to FS.  The rest is largely useless, 
quota command on linux is not functional :(


Best Practices:

Other than user home directory, probably don't want to enforce quota limits.
However, still good to turn on quota so that space utilization can be monitored.



/etc/quotas

##                                          	hard limit | thres |soft limit
##Quota Target      	type                   	disk  files| hold  |disk  file
##-------------     	-----                  	----  -----  ----- ----- -----

*       		tree@/vol/vol0  	-       -       -       -       - # monitor usage on all qtree in vol0
*       		tree@/vol/vol1  	-       -       -       -       -
*       		tree@/vol/vol2  	-       -       -       -       -

/vol/vol2/qtree1    	tree                200111000k 	75K 	-	-	- # enforce qtree quota, use kb is easier to compare on report
/vol/vol2/qtree2    	tree                    -     	-	1000M  	-	- # enable threshold notification for qtree (useless)


*                       user@/vol/vol2       	-	-       -       -       - # provide usage based on file ownership, w/in specified volume
tinh                    user                 50777000k	-       5M      7M      - # user quota, on ALL fs ?!  may want to avoid
tinh                    user@/vol/vol2         	10M     -       5M      7M      - # enforce user's quota w/in a specified volume
tinh       		user@/vol/vol2/qtree1	100M    -       -   	-       - # enforce user's quota w/in a specified qtree
										  # exceptions for +/- space can be specified for given user/location


# 200111000k = 200 GB
#  50777000k =  50 GB
# they make output of quota report a bit easier to read

# * = default user/group/qtree 
# - = placeholder, no limit enforced, just enable stats collection

Snapshot
Snapshots are configured at the volume level. Thus, if different data need to have different snapshot characteristics, then they should be in different volume rather than just being in different QTree.
WAFL automatically reserve 20% for snapshot use.

snap list vol1
snap create vol1 snapname	# manual snapshots creation.
snap sched			# print all snapshot schedules for all volumes
snap sched vol1 2 4 		# scheduled snapshots for vol1: keep 2 weekly, 4 daily, 0 hourly snapshots
snap sched vol1 2 4 6		# same as above, but keep 6 hourly snapshots, 
snap sched vol1 2 4 6@9,16,20	# same as above, specifying which 3 hourly snapshot to keep + last 3 hours
snap reserve vol1   		# display the percentage of space that is reserved for snapshot (def=20%)
snap reserve vol1 30		# set 30% of volume space for snapshot

vol options vol1 nosnap on	# turn off snapshot, it is for whole volume!

gotchas, as per netapp:
"There is no way to tell how much space will be freed by deleting a particular snapshot or group of snapshots."


NFS

(/) etc/export
is the file containing what is exported, and who can mount root fs as root.  Unix NFS related only.

/vol/vol0        -access=sco-i:10.215.55.220,root=sco-i:10.215.55.220
/vol/vol0/50gig  -access=alaska:siberia:root=alaska

Unlike most Unices, NetApp allow export of ancestors and descendants.

other options:
-sec=sys	# unix security, ie use uid/gid to define access
		# other options are kerberos-based.

Besides just having export for nfs and ahare for cifs, 
there is another setting about fs security permission style, nfs, ntfs, or mixed.  
this control characteristic of chmod and files ACL.

Once edit is done, telnet to netapp and issue cmd:
exportfs -a		# rea-add all exports as per new file
exportfs -u vol/vol1	# unexport vol1 (everything else remains intact)
exportfs -au		# remove all exports that are no longer listed in etc/exports

The bug that Solaris and Linux NFS seems to exist on NetApp also.
Hosts listed in exports sometime need to be given by IP address, or an
explicit entry in the hosts file need to be setup.  Somehow, sometime
the hostname does not get resolved thru DNS :(


options nfs.per_client_stats.enable on
	# enable the collection of detained nfs stat per client 
options nfs.v3.enable  on
options nfs.tcp.enable on
	# enable NFS v3 and TCP for better performance.

nfsstat 	# display nfs sttistics, separte v2 and v3
nfsstat -z 	# zero the nfsstat counter
nfsstat -h 	# show detailed nfs statistics, several lines per client, since zero
nfsstat -l 	# show 1 line stat per client, since boot (non resetable stat)


CIFS

cifs disable		# turn off CIFS service
cifs enable
cifs setup		# configure domainname, wins.  only work when cifs is off.
cifs testdc		# check registration w/ Windows Domain Controller



cifs shares					# display info about all shares
cifs shares -add sharename path	-comment desc	# create new share and give it some descriptive info
cifs shares -change shrname -forcegroup grpname	# specify that all cifs user will use a forced unix group on Unix-style FS.
						# this is for both read and write, so the mapping unix user need not be 
						# defined in this forcegroup in passwd or group map/file.
						# the groupname is a string, not gid number, this name need to be resolvable
						# from NIS, LDAP, or local group file.
cifs shares -change shrname -umask 002    	# define umask to be used.

cifs access -delete wingrow  Everyone				
	# by default, share is accessible to "everyone" (who is connected to the domain)
	# above delete this default access
	# Note that this is equiv to exports, not file level ACL
cifs access wingrow "authenticated users"  "Full Control" 
	# make share usable by authenticated users only
cifs access it$ AD\Administrator "Full Control"		 	
	# make share "hidden" and only give access to admin  
	# (not sure if can use group "administrators")



cifs sessions ...				# list current active cifs connections

options cifs.wins_servers
	list what WINS server machine is using

ifconfig  wins	# enable  WINS on the given interface
ifconfig  -wins	# disable WINS on the given interface

# WINS registration only happens when "cifs enable" is run.
# re-registration means stopping and starting cifs service.
# enabling or disabling wins on an interface will NOT cause re-registration

etc/lslgroups.cfg 	# list local group and membership SSID 
			# don't manually edit, use windows tool to update it!



wcc		wafle cache control, oft use to check windows to unix mapping
	-u uid/uname	uname may be a UNIX account name or a numeric UID
	-s sid/ntname 	ntname may be an NT account name or a numeric SID

	SID has a long strings for domainname, then last 4-5 digits is the user.
	All computer in the same domain will use the domain SID.

	-x remove entries from WAFL cache
	-a add entrie
	-d display stats


options wafl.default_nt_user	username 
	# set what nt user will be mapped to unix by def (blank)
options wafl.default_unix_user	username
	# set what unix username will be used when mapped to NT (def = pcuser)


user mapping b/w nt and unix, where user name are not the same.
It is stored in the (/) etc/usermap.cfg file.

NT acc			unix acc username
Optionally, can have <= and => for single direction mapping instead of default both way.
eg:

tileg\Administrator      root
tileg\fgutierrez         frankg
tileg\vmaddipati         venkat
tileg\thand              thand2
tileg\thand              thand1
tileg\kbhagavath         krishnan

*\eric			=> allen
ad\administrator	<= sunbox:root
nt4dom\pcuser		<= tinh

This mapping will be done so that users will gain full permission of the files under both env.
a lot of time, they get nt account first, and thus end up with read only access to their 
home dir in windows, as they are mapped as non owner.

< !-- -- >

usermap.cfg does get read by windows user writting to unix-style FS.
Be careful when doing M-1 mapping.  While this may allow many unix user to use same NT account
to gain access to NF-style FS as part of "everyone", the reverse access would be problematic.
eg:
hybridautoAD\tho	sa
hybridautoAD\tho	tho
While unix sa and tho maps to same user on windows, when Windows tho login, and try to write
to UNIX-style FS, permission will assume that of unix user sa, will not be tho!!

It maybe possible to use <== and ==> to indicate direction of mapping ??


(??) another map does the reverse of windows mapping back to NFS when fs is NFS and access is from windows.
(or was it the same file?).  It was pretty stupid in that it needed all users to be explicityly mapped.


NetApp Web Interface control the share access (akin to exports)
Windows Explorer file namager control each file ACL (akin to chmod on files).

Can use Windows Manager to manage NetApp, general user can connect and browse.
User list may not work too well.




CIFS Commands

cifs_setup			# configure CIFS, require CIFS service to be restarted 
				# - register computer to windows domain controller
				# - define WINS server
				
options cifs.wins_server	# display which WINS server machine is using
				# prior to OnTap 7.0.1, this is read only

cifs domaininfo			# see DC info
cifs testdc			# query DC to see if they are okay
cifs prefdc print		# (display) which DC is used preferentially
				

WINS info from NetApp, login req: http://now.netapp.com/Knowledgebase/solutionarea.asp?id=3.0.4321463.2683610

# etc/cifsconfig_setup.cfg 
# generated by cifs_setup, command is used to start up CIFS at boot
# eg:
cifs setup -w 192.168.20.2  -w 192.168.30.2 -security unix  -cp 437

# usermap.cfg

# one way mapping
*\lys	=> lks
NETAPP\administrator <= unixhost:root

# two way mapping
WINDOM\tinh	tin

## these below are usually default, but sometime need to be explicitly set
## by some old NT DC config.
WINDOM\* == *	# map all user of a specific domain
# *\*    == *  	# map all user in all domains  

Command

Commands for NetApp CLI (logged in thru telnet/ssh/rsh)

? = help, cmd list
help cmd	


dns info		# display DNS domain, 
			# extracted from WINDOWS if not defined in resolve.conf
options dns.domainname 	# some /etc/rc script set domain here

sysconfig -v	
sysconfig -a	# display netapp hw system info, include serial number and product model number
sysconfig -c	# check to ensure that there are no hardware misconfig problem, auto chk at boot

sysstat 1 	# show stats on the server, refresh every 1 sec.

df -h
	similar to unix df, -h for "human readable"
	.snapshot should be subset of the actual volume 

ndmpd status
	list active sessions

ndmpd killall
	terminate all active ntmpd sessions.
	Needed sometime when backup software is hung.  kill ndmpd session to free it.

useradmin useradd UID
	add new user (to telnet in for admin work)

useradmin userlist
	list all users


options 		# list run time options.
options  value 	# set specific options

#eg:
options autosupport.to autosupport@netapp.com,tin.ho@tileg.com
	# Change who receives notification emails.
options autosupport.doit case_number_or_name
	# Generate an autosupport email to NetApp (to predefined users).

#find out about ntp config:
cat registry| grep timed
options.cf.timed.max_skew=
options.service.cf.timed.enable=off
options.service.timed.enable=on
options.timed.log=off
options.timed.max_skew=30m
options.timed.min_skew=10
options.timed.proto=ntp
options.timed.sched=hourly
options.timed.servers=time-server-name			# time server to use
options.timed.window=0s
state.timed.cycles_per_msec=2384372
state.timed.extra_microseconds=-54
state.timed.version=1

rdfile 		read data file (raw format)
		eg rdfile /etc/exports
		inside telnet channel, will read the root etc/exports file to std out.
		equiv to unix cat

wrfile		write stdin to file  
		not edit, more like cat - > file kind of thing.


FilerView
FilerView is the Web GUI. If SSL certificate is broken, then it may load up a blank page.

secureadmin status
secureadmin disable ssl
secureadmin setup -f ssl	# follow prompt to setup new ssl cert

SSH
To allow root login to netapp w/o password, add root's id_dsa.pub to vol1/etc/sshd/root/.ssh/authorized_keys
Beware of the security implications!
Config Files

all stored in etc folder.
resolve.conf	
nsswitch.conf

# etc/exports

/vol/unix02		-rw=192.168.1.0/24:172.27.1.5:www,root=www
/vol/unix02/dir1	-rw=10.10.10.0/8

# can export subdirs with separate permissions
# issue exportfs -a to reread file


Logs

(/) etc/messages.* unix syslog style logs.  can configure to use remote syslog host.

(/) etc/log/auditlog
	log all filer level command.  Not changes on done on the FS.



The "root" of vol 0,1,etc in the netapp can be choose as the netapp root and store the /etc directory, 
where all the config files are saved.  eg.
/mnt/nar_200_vol0/etc
/mnt/na4_vol1/etc

other command that need to be issued is to be done via telnet/rsh/ssh to the netapp box.


< ! - - - - >
Howto
Create new vol, qtree, and make access for CIFS

vol create win01 ...

qtree create   /vol/win01/wingrow
qtree security /vol/win01/wingrow ntfs
qtree oplocks  /vol/win01/wingrow enable
cifs shares -add wingrow /vol/win01/wingrow -comment "Windows share growing"
#-cifs access wingrow ad\tinh "Full Control"  # share level control is usually redundant
cifs access -delete wingrow  Everyone
cifs access wingrow "authenticated users"  "Full Control"

# still need to go to the folder and set file/folder permission,
# added corresponding department (MMC share, permission, type in am\Dept-S
# the alt+k to complete list (ie, checK names).
# also remove inherit from parent, so took out full access to everyone.


Network interface config

vif = virtual interface, eg use: create etherchannel

link agregation (netapp typically calls it trunking, cisco EtherChannel).

single mode 	= HA fail over, only single link active at the same time.
multi mode	= Performance, multiple link active at the same time.  Req swich support
		  Only good when multiple host access filer.  Switch do the
		  traffic direction (per host).

Many filer comes with 4 build in ethernet port, can do:

2 pair of multi mode (e0a+e0b, e0c+e0d).
then single mode on the above pair to get HA, filer will always have 2 link
active at the same time.


pktt start all -d /etc		# packets tracing, like tcpdump
pktt stop all
# trace all itnerfaces, put them in /etc dir, 
# one file per interface.
# files can be read by ethereal/wireshark



Backup and Restore, Disaster Recovery

NetApp supports dump/restore commands, a la Solaris format. Thus, the archive created can even be read by Solaris ufsrestore command.
NetApp championed NDMP, and it is fast. But it backup whole volume as a unit, and restore has to be done as a whole unit. This may not be convinient.
volcopy is fast, it will also copy all the snapshots associated with the volume.

DFM
Data Fabric Manager, now known as ...
typically https://dfm:443/
Ment to manage multiple filer in one place, but seems to just collect stats. Kinda slow at time. And to get volume config, still have to use FilerView, so not one-stop thing. ==> limited use.
Links

   1. RAID_DP
2.

History

		- ca 2000 = Alpha Chip
OnTap 5.3	- 
OnTap 6.1 	- 2003?  Intel based ? 
OnTap 6.5	- 2004?  RAID_DP        Dual Paritiy introduced here.
OnTap 7.0	- 2005?  Aggregate introduced here.
