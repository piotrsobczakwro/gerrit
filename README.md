# Gerrit configuration
Gerrit on Rocky Linux:
- installation of Gerrit on Rocky Linux
- integration Gerrit with Jenkins

## Requirements
* Gerrit Trigger Plugin

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
* Add public key to UI ( Settings -> SSH keys) 
* Check connection.
```
ssh admin@localhost -p 29418
```
![image](https://user-images.githubusercontent.com/86531003/205731479-3143f91b-ad0c-4c96-8f33-d7b5174c367c.png)

Integrartion with Jenkins:
  * Use plugin:Gerrit Plugin
  * Copy ssh id_rsa from gerrit - and put that in `/var/lib/jenkins/.ssh`
  * Convert to PEM file :  ssh-keygen -p -f ~/.ssh/id_rsa -m pem - without that there will be problem like describe below.
  * Configure Gerrit Plugin:
    * Manage Jenkins -> Gerrit Trigger -> Add New Server
      ![image](https://user-images.githubusercontent.com/86531003/205732382-52abce1c-0515-44ff-aa97-7d3598e003e1.png)
    * Setup :
    ![image](https://user-images.githubusercontent.com/86531003/205732547-71cf907c-6737-4abb-8c3f-11fa07225a95.png)
    
    ! rember that file in `/var/lib/jenkins/.ssh/id_rsa` should have ownership like jenkins user:
      - file permission : .ssh (700)
      - file permission : .ssh/id_rsa (600)

  * Configuration fo Gerrit Trigger in freestylejob
   * ![image](https://user-images.githubusercontent.com/86531003/205733452-aa003bf0-e661-40f5-8f12-e13d55441817.png)


## Problems:
when using SSH there is problem when adding ssh id_rsa key in Jenkins plugin.

```
# Jsch seems to have problem with private key, to solve that ssh-keygen have possibility to convert the private key RSA => pem mode.
$ ssh-keygen -p -f ~/.ssh/id_rsa -m pem
```

## Gerrit cheat and sheet
```  
# Create project:
ssh -p 29418 admin@192.168.0.162 gerrit create-project demo-project --empty-commit


# Congiguration:
git config --global user.email user@gmail.com
git config --global user.name user

# Clone repository
git clone ssh://admin@192.168.0.162:29418/demo-project

# Create ChangeId
gitdir=$(git rev-parse --git-dir); scp -p -P 29418 admin@vm-lnx-gerrit:hooks/commit-msg ${gitdir}/hooks/
f="$(git rev-parse --git-dir)/hooks/commit-msg"; curl -o "$f" http://vm-lnx-gerrit:8080/tools/hooks/commit-msg ; chmod +x "$f"

# Commit changes
git commit --amend --no-edit

# Push changes
git push ssh://admin@192.168.0.162:29418/demo-project HEAD:refs/for/master
 
```

## Gerrit install plugin

- Go to website:
https://gerrit-ci.gerritforge.com/

- Download plugin to folder $gerrit/plugin
```
wget https://gerrit-ci.gerritforge.com/view/Plugins-stable-3.5/job/plugin-events-log-bazel-master-stable-3.5/lastSuccessfulBuild/artifact/bazel-bin/plugins/events-log/events-log.jar
```