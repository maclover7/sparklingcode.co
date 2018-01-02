---
layout: post
title:  "Hello World with the Node.js Ansible Setup"
date:   2018-01-01 12:00:00
categories: oss nodejs
---

If you are recently onboarded to the Node.js Build Working Group, use this guide to become familiar with your new access to Jenkins worker machines. I wrote this guide as I myself was figuring what exactly I had access to as a member of the team.

### Part 1: Setting up your machine

Step 1: Confirm access to the nodejs/secrets repository, and your public PGP key has been added to the "test" group.

Step 2: Run `cd build/test`, and then `dotgpg cat nodejs_build_test`. Confirm that a valid SSH key (should start with `-----BEGIN RSA PRIVATE KEY-----` and end with `-----END RSA PRIVATE KEY-----`) has been printed out.

Step 3: Create a new file called `~/.ssh/nodejs_build_test`, and copy and paste the key into the file.

Step 4: Run `chmod 400 ~/.ssh/nodejs_build_test` to ensure proper permissions.

Step 5: `cd` into the nodejs/build repository, and then into the `ansible` folder -- this is where all of the Ansible configuration lives.

### Part 2: Making the Magic Happen

Step 1: Pick any machine listed in the `test` section of `ansible/inventory.yml`. Personally, Ubuntu machines on DigitalOcean seem to be better for beginners.

Step 2: After you pick a machine, find the IP address for the machine. If `ubuntu1604-x86-1: {ip: 159.203.77.233}` is the machine listing, then the IP address would be `159.203.77.233`.

Step 3: Time to validate that the SSH key is working properly. Run the following command: `ssh -i ~/.ssh/nodejs_build_test root@REPLACE_IP_ADDRESS_HERE`. You may get a `The authenticity of host [...] can't be established`, but you should type in `yes`, and continue.

Step 4: Hopefully Step 3 went well, and you now have access to the machine. Try and type `echo 'hi'` and `whoami` to play around a little bit. When you are done, you can type `exit`.

Step 5: For a final activity, let's try and execute a basic command on a few machines. In this case, the command that will be run on all machines under the `test-digitalocean` heading will be `echo 'hello'`. Run this command on your machine to make it happen: `ansible test-digitalocean-* --private-key=~/.ssh/nodejs_build_test -a "/bin/echo hello"`.

Step 6 (Extra Credit): Select a different infrastructure provider under `test` to run the Ansible command on, such as `test-joyent` or `test-mininodes`.

### Part 3: Wrapping it Up

Congratulations -- you've just setup your machine to interact with the build infrastructure maintained by the Node.js Build Working Group. Give yourself a pat on the back for your hard work.

Now that you have access to all Jenkins workers, use this power for good. Consider reading and improving the playbooks stored in `ansible`, or `ssh`-ing into machines to provide assistance to other Node.js contributors when problems arise. Welcome again to the team!
