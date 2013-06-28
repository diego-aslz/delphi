---
layout: post
title: "Working around PS3 lagged updates"
category: posts
---

I have a PS3 and I bought a new game. However, before I can play my new game,
I need to download and update the patches it requires (some updates and stuff).
Everything ok so far. The problem started when I saw the size of the update my PS3
was trying to download: 263 MB. Besides, this was only one of the eight files it
said it would need to download.

## The problem

My internet connection is not that bad, I have a 5 Mb connection. But, for some
reason, PS3 videogames download updates very slowly and mine uses to stop downloading
from nothing. The download just stops at any time and I have to restart it.
I think that's a problem with my router, but that's not the subject right now.

It was 263 MB, it would take forever to update via the normal way, I would have to
restart the download process again and again, always crossing my fingers and hoping
it succeded. Of course I didn't want that.

## The solution

A friend of mine told me once that there was this little program that you could
make work like a proxy to download updates to your PC and the download from
it directly via local network to the PS3 in a very faster connection. This way,
it would be possible to use a download manager to improve the speed.

I started looking for it, since I didn't remember the name. Found it: PS3 Proxy
Server GUI. However, it's for Windows. I hate Windows. I am a Linux user and
lover. I didn't want to install `wine` package in my system just to use this little piece
of software. So I started thinking and I thoght of another solution.

### My own proxy

Using Vagrant Up, I created a new little virtual machine using linux:

```
vagrant init
vagrant up
vagrant ssh
```

In it, then:

```
sudo apt-get update
sudo apt-get install squid -y
cd /etc/squid3
sudo vi squid.conf
```

In the configuration file, I changed `http_access deny all` to `http_access allow all`,
cause I didn't need security, I needed just a "legs open" proxy. Then, in my
Vagrantfile, I mapped the port 3128 on the host machine to the same port on the
guest machine. And:

```
vagrant halt
vagrant up
```

Then, in my PS3, I configured the network settings to use the proxy on my local
virtual machine. Inside my vm:

```
tail -f /var/log/squid3/access.log
```

Then, I put my PS3 to update again, and the Squid's log file showed me the URL
of the file it was trying to get:

```
http://b0.ww.np.dl.playstation.net/tppkg/np/BCUS98123/BCUS98123_T9/d9a4490f00b04dae/UP9000-BCUS98123_00-UNCHARTED2PATCH2-A0102-V0100-PE.pkg?
```

I canceled the update and downloaded the file to my PC without the proxy and with a download
manager. It came really, really fast.

Now, the update was hosted on b0.ww.np.dl.playstation.net, so ...

### My own "PS3 update site"

In /etc/hosts file in the VM I added:

```
127.0.1.1       b0.ww.np.dl.playstation.net
```

And in the console:

```
sudo apt-get install nginx
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/sony
sudo vi /etc/nginx/sites-available/sony
sudo rm /etc/nginx/sites-enabled/default
sudo ln -s /etc/nginx/sites-available/sony /etc/nginx/sites-enabled/sony
mkdir -p /vagrant/tppkg/np/BCUS98123/BCUS98123_T9/d9a4490f00b04dae
sudo vi /etc/nginx/sites-available/sony
```

In this last file, I uncommented `listen   80;` and set the root like this:
`root /vagrant;`. Then:

```
sudo service nginx restart
sudo service squid3 restart
```

After that, I copied the downloaded update file UP9000-BCUS98123_00-UNCHARTED2PATCH2-A0102-V0100-PE.pkg
to /vagrant/tppkg/np/BCUS98123/BCUS98123_T9/d9a4490f00b04dae. Then, when I tried
to update again. My proxy redirected b0.ww.np.dl.playstation.net to my NGinx,
that offered the local file to the videogame. It downloaded it really fast (it
was using my local network, after all).

After that file, there was still another half a dozen files to be downloaded. Of course,
I got an error right after the first file finished downloading and the device
tried to get the second one, my NGinx didn't have it. I realized
that the next files were following a pattern in the name: PATCH2-A0102, PATCH3-A0103,
PATCH4-A0104 and so on. So, I guessed the next URL's and downloaded them all
up to PATCH9-A0109 via my download manager in a few minutes (it ended up being
half a GB in updates). Then, I copied them to the same folder so my NGinx
could find them and started the update process again. It worked like a charm. =D
