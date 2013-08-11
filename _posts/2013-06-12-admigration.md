Title: AD Migration
Tags: AD
Author: Marcelo Moreira
Summary: How we migrated from our own domain into Stanford's SU

The department I work for is one of a few that has its own AD subdomain from Stanford's Win domain.

Several reasons made us move away from it, including:

 * high hardware costs to maintain the domain controllers
 * administration costs of these controllers (central IT must manage them in this particular case, so we had to pay them for it)
 * responsibility (full access to the domain settings bring some risks)

Plus a few others...

Fortunately, Stanford does provide a domain for the departments. Each department has its own OU, with full rights on it. We don't have to worry about hardware refresh cycles or maintenance, since it is all managed by ITS. There are no costs as well. Domain services are also available in the DR site, which makes it even more appealing.

How did we migrate ? Well, the migration happened in stages. I will explain each one in more details below.

## Migrating the OU structure

I first migrated the OU structure from our domain into the newly created OU inside SU.

I dumped all the OU structure using the following command

    dsquery ou "our root OU (change it to yours here)" -d fac.win.stanford.edu -limit 0

That provided me a list of all teh OUs that needed to be migrated. I ran them through this python script:

    a = []
    for line in sys.stdin:
        a.append(line.strip().replace('OU=LBRE,DC=fac','OU=LBRE,DC=su').split(',')) 

    l = 6
    while True:
        n = [i for i in a if len(i) == l]
        if n == []:
            break
        print "level %s:" % l
        for item in n:
            print 'dsadd ou -d su.win.stanford.edu "%s"'% ','.join(item)
        l += 1

The script dumps dsadd commands in a structured fashion: OU "a" is dumped before OU "a, b", otherwise the second one fails (because the underlying OU does not exist).

## Migrating GPO's

Next, with the underlying OU structure now in place, we proceeded into migrating the GPO's.

Unfortunately, since our GPO structure was pretty messed up, we decided to migrate it manually and use the opportunity to clean it up as well.

We were able to consolidate several GPOs into a single one, and made the overall structure a little bit more manageable and easier to use and understand.

## Migrating groups

This one was a challenge...

There were a lot of groups, and most of them needed to be migrated.

First I dumped all groups and members into a CSV list using this command:

    FOR /F "delims=!" %%i in ('"dsquery group -d fac.win.stanford.edu -limit 0"') DO FOR /F "delims=!" %%j in ('"dsget group %%i -d fac.win.stanford.edu -members"') DO echo %%i,%%j

A funny thing: I was tagged as an "old-schooler" by some co-workers that mentioned that "FOR /F" was an ancient thing... I do agree in some degree that it was indeed old-school, but I was able to accomplish that as a one-liner. I cant say that I was proud of myself (after all, I am a Unix person...), but I am pretty content with the results... ;-)

I processed this list to do some general cleanup, modified some names and OU structure as well, and then ran it through a python script that dumped some VBScript.

The VBScript is roughly like this:

    On Error Resume Next
    Const ADS_GROUP_TYPE_UNIVERSAL_GROUP = &h8
    Const ADS_GROUP_TYPE_SECURITY_ENABLED = &h80000000
	Const ADS_PROPERTY_APPEND = 3 
	 
	Set objOU = GetObject("LDAP://...,DC=su,DC=win,DC=stanford,DC=edu")
	 
	g = "GROUPNAMEHERE"
	m = Array("CN=user1,OU=...,DC=win,DC=stanford,DC=edu","CN=user2,OU=...,DC=win,DC=stanford,DC=edu")
	WScript.Echo "(" & g & "): Starting"
	Set objGroup = objOU.Create("Group", "cn=" & g)
	objGroup.Put "sAMAccountName", g
	objGroup.Put "groupType", ADS_GROUP_TYPE_UNIVERSAL_GROUP Or ADS_GROUP_TYPE_SECURITY_ENABLED
	objGroup.PutEx ADS_PROPERTY_APPEND, "member", m
	objGroup.SetInfo
	If Err.Number = 0 Then
	    WScript.Echo "(" & g & "): Done"
	Else
	    WScript.Echo "(" & g & "): Error: " & Err.Number & ", Src: " & Err.Source & ", Desc: " & Err.Description
	    Err.Clear
	End If
	WScript.Echo "(" & g & "): End"

## Conclusion

At this point, the AD migration is roughly completed. We do have to make some minor adjustments to further cleanup the groups from old domain accounts, and then start migrating computers.

We also need to update the ACLs on the file servers to reflect these new groups.

Luckily, I guess my work here is done, and I will let the Helpdesk folks and my sysadmins take this from here. These tasks alone will demand a gigantic amount of time and a lot... I really mean a lot... of patience...
