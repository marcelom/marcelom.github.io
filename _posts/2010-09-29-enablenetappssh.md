Title: Enabling SSH on a Netapp filer
Tags: Netapp
Author: Marcelo Moreira
Summary: How to enable ssh on the netapp filer

This article is a draft, and needs major rework...

---------

	[remote-mgr::root:/root]# ssh 192.168.15.200
	ssh: connect to host 192.168.15.200 port 22: Connection refused
	
	[remote-mgr::root:/root]# telnet 192.168.15.200
	Trying 192.168.15.200...
	Connected to 192.168.15.200.
	Escape character is '^]'.
	
	Password:
	filer01>
	filer01> secureadmin status
	ssh2    - inactive
	ssh1    - inactive
	ssl     - inactive
	
	filer01> secureadmin setup ssh
	SSH Setup
	---------
	Determining if SSH Setup has already been done before...no
	
	SSH server supports both ssh1.x and ssh2.0 protocols.
	
	SSH server needs two RSA keys to support ssh1.x protocol. The host key is
	generated and saved to file /etc/sshd/ssh_host_key during setup. The server
	key is re-generated every hour when SSH server is running.
	
	SSH server needs a RSA host key and a DSA host key to support ssh2.0 protocol.
	The host keys are generated and saved to /etc/sshd/ssh_host_rsa_key and
	/etc/sshd/ssh_host_dsa_key files respectively during setup.
	
	SSH Setup will now ask you for the sizes of the host and server keys.
	 For ssh1.0 protocol, key sizes must be between 384 and 2048 bits.
	 For ssh2.0 protocol, key sizes must be between 768 and 2048 bits.
	 The size of the host and server keys must differ by at least 128 bits.
	
	Please enter the size of host key for ssh1.x protocol [768] :
	Please enter the size of server key for ssh1.x protocol [512] :
	Please enter the size of host keys for ssh2.0 protocol [768] :
	
	You have specified these parameters:
	        host key size = 768 bits
	        server key size = 512 bits
	        host key size for ssh2.0 protocol = 768 bits
	Is this correct? [yes]
	
	Setup will now generate the host keys in the background. It will take a
	few minutes. After Setup is finished you can start SSH server with
	command 'secureadmin enable ssh'. A syslog message will be generated
	when Setup is complete.
	
	Tue Aug 18 21:52:37 GMT [rc:info]: SSH Setup: SSH Setup is done. Host keys are stored in /etc/sshd/ssh_host_key, /etc/sshd/ssh_host_rsa_key and /etc/sshd/ssh_host_dsa_key.
	filer01>
	filer01> Connection to 192.168.15.200 closed by foreign host.
	
	[remote-mgr::root:/root]# mkdir /tmp/filer01
	[remote-mgr::root:/root]# mount 192.168.15.200:/vol/vol0 /tmp/filer01
	
	[remote-mgr::root:/root]# cd /tmp/filer01/etc/sshd
	[remote-mgr::root:/tmp/filer01/etc/sshd]# ls -l
	total 48
	-rw-------   1 root     root         534 Aug 19 05:52 ssh_host_dsa_key
	-rw-r--r--   1 root     root         470 Aug 19 05:52 ssh_host_dsa_key.pub
	-rw-------   1 root     root         411 Aug 19 05:52 ssh_host_key
	-rw-r--r--   1 root     root         249 Aug 19 05:52 ssh_host_key.pub
	-rw-------   1 root     root         688 Aug 19 05:52 ssh_host_rsa_key
	-rw-r--r--   1 root     root         174 Aug 19 05:52 ssh_host_rsa_key.pub
	
	[remote-mgr::root:/tmp/filer01/etc/sshd]# ssh 192.168.15.200 uptime
	ssh: connect to host 192.168.15.200 port 22: Connection refused
	
	[remote-mgr::root:/tmp/filer01/etc/sshd]# rsh 192.168.15.200 secureadmin status
	ssh2    - inactive
	ssh1    - inactive
	ssl     - inactive
	
	[remote-mgr::root:/tmp/filer01/etc/sshd]# rsh 192.168.15.200 secureadmin enable ssh
	
	[remote-mgr::root:/tmp/filer01/etc/sshd]# ssh 192.168.15.200 uptime
	The authenticity of host '192.168.15.200 (192.168.15.200)' can't be established.
	RSA key fingerprint is 9b:aa:c9:0e:92:ed:f7:98:60:9f:9d:0e:b1:65:96:ed.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added '192.168.15.200' (RSA) to the list of known hosts.
	root@192.168.15.200's password:
	  9:57pm up 36 mins, 12 NFS ops, 0 CIFS ops, 0 HTTP ops, 0 DAFS ops, 0 FCP ops, 0 iSCSI ops
	
	[remote-mgr::root:/tmp/filer01/etc/sshd]# mkdir -p root/.ssh
	[remote-mgr::root:/tmp/filer01/etc/sshd]# mkdir .ssh
	[remote-mgr::root:/tmp/filer01/etc/sshd]# ls -l
	total 56
	drwxr-xr-x   2 root     root        4096 Aug 19 05:58 root
	-rw-------   1 root     root         534 Aug 19 05:52 ssh_host_dsa_key
	-rw-r--r--   1 root     root         470 Aug 19 05:52 ssh_host_dsa_key.pub
	-rw-------   1 root     root         411 Aug 19 05:52 ssh_host_key
	-rw-r--r--   1 root     root         249 Aug 19 05:52 ssh_host_key.pub
	-rw-------   1 root     root         688 Aug 19 05:52 ssh_host_rsa_key
	-rw-r--r--   1 root     root         174 Aug 19 05:52 ssh_host_rsa_key.pub
	[remote-mgr::root:/tmp/filer01/etc/sshd]# cd root/
	[remote-mgr::root:/tmp/filer01/etc/sshd/root]# ls -la
	total 24
	drwxr-xr-x   3 root     root        4096 Aug 19 05:58 .
	drwxr-xr-x   3 root     root        4096 Aug 19 05:58 ..
	drwxr-xr-x   2 root     root        4096 Aug 19 05:58 .ssh
	[remote-mgr::root:/tmp/filer01/etc/sshd/root]#
	[remote-mgr::root:/tmp/filer01/etc/sshd/root]# cd .ssh/
	[remote-mgr::root:/tmp/filer01/etc/sshd/root/.ssh]# ls -la
	total 16
	drwxr-xr-x   2 root     root        4096 Aug 19 05:58 .
	drwxr-xr-x   3 root     root        4096 Aug 19 05:58 ..
	
	[remote-mgr::root:/tmp/filer01/etc/sshd/root/.ssh]# cat /root/.ssh/id_dsa.pub >> /tmp/filer01/etc/sshd/root/.ssh/authorized_keys2
	
	[remote-mgr::root:/tmp/filer01/etc/sshd/root/.ssh]# ssh 192.168.15.200 uptime
	 10:00pm up 39 mins, 58 NFS ops, 0 CIFS ops, 0 HTTP ops, 0 DAFS ops, 0 FCP ops, 0 iSCSI ops
	
	[remote-mgr::root:/tmp/filer01/etc/sshd/root/.ssh]# ssh 192.168.15.200
	filer01>
	filer01>
	filer01>
	filer01> Connection to 192.168.15.200 closed by remote host.
Connection to 192.168.15.200 closed.
