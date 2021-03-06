# Harden and optimize VPS for production
No fluff instructions on how to harden and optimize a VPS.

> Before you go running to AWS or GCP or Azure to get an instance to experiment
> with, I urge you to consider the smaller guys for two reasons:
> - A world with three big gorillas is far less interesting.
> - The world of gorillas is a jungle and you can get lost in the wild.
>
> The chances of you requiring web scale are slim to none; so why bother moving
> into the jungle when you can have a picnic in the park to handle whatever your
> world changing ideas are?
>
> On that note, take a look at the [alternatives](https://joshtronic.com/2018/10/15/vps-showdown-october-2018/)
> and choose whatever gets you going fast. We have worked with Linode and
> Digital Ocean. Both work fine. Although, I find it super annoying and
> disappointing to see that their websites have added crap load of third
> party trackers of late.
>
> I can see bloggers and news sites whose livelyhood depends on advertising
> going to the darkside by injecting trackers and third party cookies for
> sustenance. But I fail to see any reason why sites like Linode and Digital
> Ocean feel the need for those. I can only imagine a clueless product manager
> unaware of the privacy battles raging on the Internet or an evil and greedy
> management trying to make money by selling user data as a side hustle. I hope
> the former and not the latter is the case.
>
> Anyway, you can mitigate that annoyance by using FireFox and disabling all
> third party cookies and trackers. For a good measure we recommend installing
> the [Privacy Badger plugin from EFF](https://www.eff.org/privacybadger). Or
> try the Brave Browser.

## Prerequisites
The following information must be available in the work order to complete the
task:

- New instance's ip address
- Root access to the instance

## Sys Admin steps
Log in to the VPS instance as root over SSH and do the following.

### Disable root login and password based ssh authentication
NOTE:
  do this step after verifying that a system administrator
  can login using ssh pubkey!

- Edit /etc/ssh/sshd_config and ensure the following settings:
  ```shell
  PermitRootLogin no
  ClientAliveInterval 300
  ClientAliveCountMax 600
  PasswordAuthentication no
  ```
- Restart sshd
  ```shell
  systemctl restart sshd.service
  ```

### Update the system
```shell
  sudo apt update
  sudo apt upgrade
  sudo apt dist-upgrade
```

### Close the gates that should not be open
```shell
  sudo apt install ufw
  sudo apt install fail2ban
  sudo ufw allow SSH
  sudo ufw allow http
  sudo ufw allow https
  sudo ufw limit ssh/tcp
  sudo ufw enable
  sudo ufw status numbered
```

### Get stingy and make the $5 VPS do more for us
- check swap settings and adjust
```console
sysadm@localhost:~$ free -h
              total        used        free      shared  buff/cache   available
Mem:           996M         58M        447M        4.5M        489M        788M
Swap:          511M          0B        511M

sysadm@localhost:~$ sudo swapon -s
Filename				Type		Size	Used	Priority
/dev/sdb                               	partition	524284	0	-1
```
- make the swap spaces to be at least as much as the available memory
- follow detailed steps available here https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-ubuntu-14-04

## Test
The main tests are to ensure that ports you want to be open are open
and the rest closed. Typically, what you need is a port scanner. The
following links can help you get started...

- [Quick and dirty port scan from your browser](https://viewdns.info/portscan/?host=$IpAddress)

# References
- [Fail2Ban](https://www.fail2ban.org/wiki/index.php/MANUAL_0_8)
- [Uncomplicated firewall](https://wiki.ubuntu.com/UncomplicatedFirewall)
