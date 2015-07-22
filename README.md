# Zocker
Docker implemented in bash usinz ZFS.

  * [Prerequisites](#prerequisites)
  * [Example Usage](#example-usage)
  * [Functionality: Currently Implemented](#functionality-currently-implemented)
  * [Functionality: Not Yet Implemented](#functionality-not-yet-implemented)
  * [License](#license)

Based on
[bocker](https://github.com/p8952/bocker)


## Prerequisites

The following packages are needed to run zocker.

* [ZFS](http://zfsonlinux.org/epel.html)
* iproute2
* iptables
* util-linux >= 2.25.2

Because most distributions do not ship a new enough version of util-linux you will probably need grab the sources from [here](https://www.kernel.org/pub/linux/utils/util-linux/v2.25/) and compile it yourself.

Additionally your system will need to be configured with the following.

* A ZFS zpool named `zocker`
* A network bridge called `bridge0` and an IP of 10.0.0.1/24
* IP forwarding enabled in `/proc/sys/net/ipv4/ip_forward`
* A firewall routing traffic from `bridge0` to a physical interface.
* A `base-image` which contains the filesystem to seed your container with.

For ease of use a Vagrantfile is included which will build the needed environment.

Even if you meet the above prerequisites you probably still want to **run zocker in a virtual machine**. Zocker runs as root and among other things needs to make changes to your network interfaces, routing table, and firewall rules. **I can make no guarantees that it wont trash your system**.

## Example Usage

```
$ zocker init base-image/
Created: img_84632

$ zocker images
IMAGE_ID
img_84632

$ zocker run img_84632 uname -sro
Linux 3.10.0-123.20.1.el7.x86_64 GNU/Linux

$ zocker ps
CONTAINER_ID       COMMAND
ps_12277           uname -sro

$ zocker logs ps_12277
Linux 3.10.0-123.20.1.el7.x86_64 GNU/Linux

$ zocker rm ps_12277
Removed: ps_12277

$ zocker run img_84632 which wget
which: no wget in (/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin)

$ zocker run img_84632 yum install -y wget
Installing : wget-1.14-10.el7_0.1.x86_64                                  1/1
Verifying  : wget-1.14-10.el7_0.1.x86_64                                  1/1
Installed  : wget.x86_64 0:1.14-10.el7_0.1
Complete!

$ zocker ps
CONTAINER_ID       COMMAND
ps_14099           which wget
ps_43377           yum install -y wget

$zocker commit ps_43377 img_84632
Removed: img_84632
Created: img_84632

$ zocker run img_84632 which wget
/usr/bin/wget

$ zocker rm ps_14099
Removed: ps_14099

$ zocker rm ps_43377
Removed: ps_43377

$ zocker rm ps_95942
Removed: ps_95942

$ zocker rm img_84632
Removed: img_84632
```

## Functionality: Currently Implemented

* `docker build` †
* `docker images`
* `docker ps`
* `docker run`
* `docker logs`
* `docker commit`
* `docker rm` / `docker rmi`
* Networking

† `zocker init` provides a very limited implemetation of `docker build`

## Functionality: Not Yet Implemented

* Port Forwarding
* Data Volumes
* Data Volume Containers

## License

Based on Bocker:

Copyright (C) 2015 Brian Akins
Copyright (C) 2015 Peter Wilmott

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.

(I think I attributed and licensed correctly, let me know if I need to change.)
