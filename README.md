我是光年实验室高级招聘经理。
我在github上访问了你的开源项目，你的代码超赞。你最近有没有在看工作机会，我们在招软件开发工程师，拉钩和BOSS等招聘网站也发布了相关岗位，有公司和职位的详细信息。
我们公司在杭州，业务主要做流量增长，是很多大型互联网公司的流量顾问。公司弹性工作制，福利齐全，发展潜力大，良好的办公环境和学习氛围。
公司官网是http://www.gnlab.com,公司地址是杭州市西湖区古墩路紫金广场B座，若你感兴趣，欢迎与我联系，
电话是0571-88839161，手机号：18668131388，微信号：echo 'bGhsaGxoMTEyNAo='|base64 -D ,静待佳音。如有打扰，还请见谅，祝生活愉快工作顺利。

# honeybits
A simple tool to create and place breadcrumbs, honeytoken/traps or as I call it "honeybits", to lead the attackers to your decoys/honeypots! #cyber_deception #honeytoken

The problem with the traditional implementation of honeypots in production environments is that the bad guys can ONLY discover the honeypots by network scanning which is noisy! The only exception I can think of is [Beeswarm](https://github.com/honeynet/beeswarm) (it intentionally leaks credentials in the network traffic and then looks for the unexpected reuse of these honey credentials).

If you take a look at the [Mitre ATT&CK Matrix](https://attack.mitre.org/wiki/Main_Page), you will see that 'Network Service Scanning' is only one of the many different Post-breach activities of attackers. The more you plant false or misleading information in response to the post-compromise techniques (specially the techniques under ‘credential access’, ‘Discovery’, and ‘Lateral movement’ tactics in ATT&CK matrix), the greater the chance of catching the attackers. "Honeybits" helps you automate the creation of breadcrumbs/honeytokens on your production Servers and Workstations. These honeytokens or breadcrumbs can include:
* Fake bash_history commands (such as ssh, ftp, rsync, scp, mysql, wget, awscli)
* Fake AWS credentials and config files (you required to create fake AWS IAM users with no permissions and generate access keys for them)
* Configuration, backup and connection files such as RDP and VPN
* Fake entries in hosts, ARP table, etc.
* Fake browser history, bookmarks and saved passwords
* Injected fake credentials into LSASS
* Fake registry keys

This is a small but crusial component of your deception system which should also include honeypots (ideally high-interaction ones), Log collection and analysis system, alerting, and so on. 

![Honeybits](https://github.com/0x4D31/honeybits/blob/master/docs/honeybits.png)

### Current features:
* Creating honeyfiles and monitoring the access to these traps using go-audit or auditd 
* Template based content generator for honeyfiles
* Insert different honeybits into "bash_history", including the following sample commands:
  + ssh
```(sshpass -p '123456' ssh -p 2222 root@192.168.1.66)```
  + ftp
```(ftp ftp://backup:b123@192.168.1.66:2121)```
  + rsync
```(rsync -avz -e 'ssh -p 2222' root@192.168.1.66:/var/db/backup.tar.gz /tmp/backup.tar.gz)```
  + scp
```(scp -P 2222 root@192.168.1.66:/var/db/backup.tar.gz /tmp/backup.tar.gz)```
  + mysql
```(mysql -h 192.168.1.66 -P 3306 -u dbadmin -p12345 -e "show databases")```
  + wget
```(wget http://192.168.1.66:8080/backup.zip)```
  + any custom commands:
```(nano /tmp/backup/credentials.txt)```
  + aws
```
export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
aws ec2 describe-instances --profile devops --region us-east-2
```
* Insert honeybits into AWS config and credentials file
* Insert honeybits into /etc/hosts
* Reading config from a Remote Key/Value Store such as Consul or etcd

### Test:
```
$ go build
$ sudo ./honeybits 

Failed reading remote config. Reading the local config file...
Local config file loaded.

[failed] honeyfile already exists at this path: /tmp/secret.txt
[done] go-audit rule for /home/test/secret.txt is added
[done] honeyfile is created (/home/test/secret.txt)
[done] go-audit rule for /opt/secret.txt is added
[done] sshpass honeybit is inserted
[done] wget honeybit is inserted
[done] ftp honeybit is inserted
[done] rsync honeybit is inserted
[done] scp honeybit is inserted
[done] mysql honeybit is inserted
[failed] aws honeybit already exists
[done] hostsconf honeybit is inserted
[done] awsconf honeybit is inserted
[done] awscred honeybit is inserted
[done] custom honeybit is inserted
```

### TODO:
* Content generator for honeyfiles and file honeybits
  + note: honeyfiles are fake monitored files with random content (doesn't matter), but file honeybits are like connection, config, or backup files that may contain credentials and point the attackers to our honeypots/decoys
* Add more Credential Traps
  + Configuration, connection and backup files (file honeybit)
* Add more Network Traps
  + Monitoring some network traps using go-audit
* Add Application Traps
* Add Windows support (current version supports Linux and Mac OS X)
  + New traps including CMD/PowerShell commands history, Browser history, Saved passwords, Registry keys, Credentials, Connection and configuration files such as .rdp and etc.
* Documentation

