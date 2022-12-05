# gerrit
Gerrit configuration

## Requirements
* Gerrit Plugin

## Installation

* Install Java :
```
yum install java-11-openjdk.x86_64
```

* Open ports:
```
firewall-cmd --add-service ssh --permanent
firewall-cmd --add-port=8080 --permanent
firewall-cmd --add-port=29418/tcp --permanent
firewall-cmd --reload
```
* Download gerrit and start gerrit dev env.
```
wget https://gerrit-releases.storage.googleapis.com/gerrit-3.5.1.war
export GERRIT_SITE=~/gerrit_testsite
java -jar gerrit*.war init --batch --dev -d $
```

*  Start gerrit dev
```
cd $GERRIT_SITE
cd bin/
./gerrit.sh start
./gerrit.sh check
```

* Generate ssh keys
```
ssh-keygen -t rsa
```

## Problems:

when using SSH there is problem when adding ssh id_rsa key.


```
Jsch seems not to support the above private key format, to solve it, we can use ssh-keygen to convert the private key format to the RSA or pem mode, and the above program works again.

$ ssh-keygen -p -f ~/.ssh/id_rsa -m pem
```
