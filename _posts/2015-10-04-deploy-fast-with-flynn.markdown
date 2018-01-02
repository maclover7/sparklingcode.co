---
layout: post
title:  "Deploy Fast with Flynn"
date:   2015-10-04 12:00:00
categories: deployments flynn
---

This is a five step blog post about how to deploy quickly to a VPS using
an awesome Go-powered PaaS (Platform as a Service). Deploy fast with Flynn -- zoom zoom!

# Part 0: Get a VPS (Virtual Private Server)

Before you can do anything with this tutorial, it is vital that you have
a clean VPS that you will install Flynn onto. Without a VPS, there is
nothing to be Flynn-ing (yes, I just turned Flynn into a verb) onto. My
preference is to get a VPS via DigitalOcean (this isn't an endorsement -
I just like their cheap prices + good support). Once you have your VPS
ready to go, ssh into the server, and proceed to the next step.

# Part 1: Install Flynn onto your VPS

Welcome to part 1! In this part, we'll be installing the Flynn PaaS onto
your VPS. Make sure you are SSH'd into the server already, and set to
go.

1. Run the following command to install the Flynn CLI (command line
   interface). This lets Flynn + you chat about all of the cool websites
you'll be building! This command may take a few minutes to run, so don't
be worried if it's taking a little while.

```bash
$ L=/usr/local/bin/flynn && curl -sSL -A "`uname -sp`" https://dl.flynn.io/cli | zcat >$L && chmod +x $L

$ sudo bash < <(curl -fsSL https://dl.flynn.io/install-flynn)

$ /sbin/modprobe zfs
```

2. Run the following command to get the discovery URL for your one-node
   cluster. If you want to have multiple nodes of Flynn, please see
[their documentation](https://flynn.io/docs/installation#ubuntu-14.04-amd64) or tweet at me (@applerebel) for more information.

```bash
sudo flynn-host init --init-discovery
```
3. Run the following two commands to get your one-node cluster going.
   Make sure the second command returns the correct response!

```bash
$ sudo start flynn-host
$ sudo status flynn-host
# --> This command should include `flynn-host start/running`. If it says `stop/waiting` instead, check out the logs at `/var/log/upstart/flynn-host.log` for more information.
```
4. Set up your cluster via the next command. Make sure to replace the
   CLUSTER_DOMAIN value with whatever the domain will be for your web
server. If you don't have a domain yet, you can use the following
formula to get a domain name: site.IP_ADDRESS.xip.io. An example of this
would be 10.0.0.1.xip.io. Also, make sure you repalce the --discovery
URL with the URL that was generated earlier.

```bash
sudo \
    CLUSTER_DOMAIN=demo.localflynn.com \
    flynn-host bootstrap \
    --discovery https://discovery.flynn.io/clusters/53e8402e-030f-4861-95ba-d5b5a91b5902
```

* If it's saying it can't find the cluster, then you should powercycle
flynn-host like so:

```bash
stop flynn-host && start flynn-host
```

* The above command should say "Flynn bootstrapping complete." if
  everything finished successfully. It should return a command to run
that will look something like this:

```bash
flynn cluster add -p REktHVsDrKFed7ACeKJCt9xF3TrMd96d1+N3Nr5BArY= default site.104.131.189.57.xip.io fdf9cdda89785ef655e7e787daec8be2
```

this means it's time to install the local Flynn client now on our dev
machine! Also, make sure you take note of the dashboard URL and login
token listed below. We'll be needing this URL and password for Part 3.

# Part 2: Install the Flynn CLI onto your Dev Machine

The hard part is now over -- the installing of Flynn onto the VPS. Next
is the easy part --> installing the Flynn CLI locally onto your
development machine.

1. To install the CLI itself, run the following command:

```bash
L=/usr/local/bin/flynn && curl -sSL -A "`uname -sp`" https://dl.flynn.io/cli | zcat >$L && chmod +x $L
```

2. Next, run the command that was returned above from Part 1, that
   begins with 'flynn cluster add ....'. This will associate your local
dev machine with your VPS' Flynn cluster!

The next part is to install your Flynn cluster's SSL certificate onto
your machine, so that way you can securely send your code up to Flynn!

# Part 3: Securing your Flynn Connection

In this part, we'll be installing the Flynn SSL certificate for your
cluster onto your machine. Remember the dashboard URL and token from
Part 1? We'll be needing that now. Scroll up in your terminal logs if
you don't have that information at the ready.

1. Go the URL outputted from the terminal in your web browser. It should start with
   'http://dashboard......'.

2. At the top of the page, there should be a link to install your Flynn VPS' SSL certificate. Download those certificates, double click to open, and choose to "Always trust" the certificate.

3. Close out the window for the certificate, and then select + reopen it
   again in Keychain Access. Open the trust dialog box, and make sure
that "Always trust" is selected. Sometimes the change made earlier
doesn't persist.

That's all for this part, where you secure your Flynn connection! The
last and final part is about creating your Flynn app and pushing it
live!

# Part 5: Push it up, push it up!

Welcome to the final part of the tutorial! In this part, we'll be
creating our Flynn app, adding a git remote, and then pushing it live
for the world to see. Let's get started!

1. 'cd' into any project you have that has a Procfile in it. If you
   don't have one, you can run this in your terminal:

```bash
$ git clone https://github.com/flynn-examples/ruby-flynn-example.git
$ cd ruby-flynn-example
```

2. Next, we're going to create a Flynn app, that will power our website.
   Run the following command below, and substitute APP_NAME with
whatever you'd like to call your app.

```bash
flynn create APP_NAME
```

3. The moment of truth! It's finally time to git push up to Flynn. The previous command setup a git remote for us, so now all we have to do is just push up to Flynn, and the rest is handled for us!

```bash
git push flynn master
```

---

That's all there is to it! Thanks for reading through this tutorial on
how to deploy with the Flynn PaaS. You can check out their [website](flynn.io),
[awesome documentation](flynn.io/docs) for more information, or even read Flynn's
[source code on Github](github.com/flynn/flynn).

If you have any questions or anything you'd like to tell me, you can
email me or [tweet](https://twitter.com/applerebel) at me.
