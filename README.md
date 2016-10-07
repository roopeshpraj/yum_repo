# yum_repo
#Create a directory to be used as yum repo
[jenkins_agent@ba-mgt-mon1 ~]$ cd /var/www/repo/myrepo

#Move the set of rpms to the repo
mv *.rpm /var/www/repo/myrepo

#Sign the rpms
#gpg-agent should be running
[jenkins_agent@ba-mgt-mon1 repo]$ ps aux | grep gpg-agent
3000      2038  0.0  0.0 112136   608 ?        Ss   Oct04   0:02 gpg-agent --enable-ssh-support --daemon --write-env-file /home/jenkins_agent/.gpg-agent-info

#If not running, run gpg-agent
1) Run gpg-agent
[jenkins_agent@ba-mgt-mon1 ~]$ cat gpgagent.sh
#!/bin/sh
exec gpg-agent --enable-ssh-support --daemon \
           --write-env-file ${HOME}/.gpg-agent-info "$@"

gpg-agent[2037]: directory `/home/jenkins_agent/.gnupg' created
gpg-agent[2037]: directory `/home/jenkins_agent/.gnupg/private-keys-v1.d' created
GPG_AGENT_INFO=/tmp/gpg-1QhcuJ/S.gpg-agent:2038:1; export GPG_AGENT_INFO;
SSH_AUTH_SOCK=/tmp/gpg-4L9Txp/S.gpg-agent.ssh; export SSH_AUTH_SOCK;
SSH_AGENT_PID=2038; export SSH_AGENT_PID;
gpg-agent[2038]: gpg-agent (GnuPG) 2.0.14 started

2) Install screen
To configure a signature, you first need to create a new key with the gpg command, using the --gen-key option, as shown following - 
[ec2-user@ip-172-31-31-76 ~]$ sudo yum install screen
(172-31-31-76 is personal aws instance)
 
Once screen is installed su as user1
[ec2-user@ip-172-31-31-76 ~]$ su - user1
Password:
 
Invoke screen
[jenkins_agent@ba-mgt-mon1 stubo]$ screen
 
Generate keys 
(Once inside screen, enter)
gpg --gen-key -v --debug-level guru
 
reference - https://bugzilla.redhat.com/show_bug.cgi?id=659512

2) Install [jenkins_agent@ba-mgt-mon1 ~]$ screen
[jenkins_agent@ba-mgt-mon1 ~]$ gpg --gen-key -v --debug-level


3) [jenkins_agent@ba-mgt-mon1 stubo]$ gpg --export -a 'roopesh raj' > RPM-GPG-KEY


Remove old signs and 
[jenkins_agent@ba-mgt-mon1 test]$ rpm --resign *.rpm

[jenkins_agent@ba-mgt-mon1 test]$ rpm --checksig *.rpm


Create new repo
[jenkins_agent@ba-mgt-mon1 test]$ createrepo /var/www/repo/stubo
Spawning worker 0 with 72 pkgs
Workers Finished
Gathering worker results

Saving Primary metadata
Saving file lists metadata
Saving other metadata
Generating sqlite DBs
Sqlite DBs complete

Check the contents of newly created metadata 
/var/www/repo/test/repodata

AWS yum repo to s3 bucket
aws s3 sync /var/www/repo/stubo s3://stubo-repo –delete



