layout: true
class: inverse, middle, large

---
class: special
# How to get Galaxy

slides by @martenson

.footnote[\#usegalaxy / @galaxyproject]

---
class: larger

## Please Interrupt!
Your questions will be answered.

---
class: normal
# Reasons to Install Your Own Galaxy

You only need to download Galaxy if you plan to:

- Run a local production Galaxy because you want to
  - Install and use tools unavailable on public Galaxies
  - Use sensitive data (e.g. clinical)
  - Process large datasets that are too big for public Galaxies
  - Plug-in new datasources
- Develop Galaxy tools
- Develop Galaxy itself

Even when you plan any of the above sometimes you can leverage pre-configured
[Docker image](https://github.com/bgruening/docker-galaxy-stable)
or use [Cloudlaunch](https://launch.usegalaxy.org).

---
# Get logged in to your VM

For OS X/Linux/Windows w/ Linux Subsystem or OpenSSH:
```console
$ ssh -L 8080:localhost:8080 ubuntu@<your_ip>
```

For [PuTTY](http://www.putty.org/) on Windows:
- Create a session
- See [instructions here](http://realprogrammers.com/how_to/set_up_an_ssh_tunnel_with_putty.html).
- User: ubuntu
- Host Name: `<your_ip>`
- (Tunnel) Source port: 8080
- (Tunnel) Destination: 127.0.0.1:8080
- (Tunnel) Click "Add"

---
# Requirements

- Any Linux or Mac OS
- Python 2.7

Optional
  - samtools (metadata etc.)
  - Git code versioning system
  - GNU Make + gcc to compile and install tool dependencies
  - Additional requirements for shipped tools

Run:
```shell
$ sudo apt-get install git python samtools
```

---
# Clone the repository

1. Check what is the latest [release](https://docs.galaxyproject.org/en/master/releases/index.html)
1. Run
   ```shell
   $ git clone -b release_17.09 https://github.com/galaxyproject/galaxy.git
   ```

Release is defined by the branch name: `release_17.09` see the [branch list](https://github.com/galaxyproject/galaxy/branches/all))

Without specifying branch during clone you are running the *development* version of Galaxy.

For the rolling stable release: `git checkout master`<sup>[1]</sup>.

.footnote[<sup>[1]</sup> This is currently not updated as often as it should be.]

---
# Start Galaxy

1. `$ cd galaxy/`
1. `$ ./run.sh`
1. Visit `http://localhost:8080` with your laptop's web browser

You should see default Galaxy running.

.footnote[Note that first startup needs internet connection and takes longer than the subsequent ones.]

---
# Locale issues when starting Galaxy?

Galaxy and the dependent programs requires US locale so if you have a
different one set on your machine, you may get an exception starting Galaxy
that points to a locale issue.

To fix, do the following in your VM:
```
sudo vi /etc/ssh/sshd_config
# Comment out the following line
AcceptEnv LANG LC_*
# Save & exit
# Restart ssh service
sudo service restart sshd
```
Then exit your ssh session and reconnect. Start Galaxy and it should run fine.

---
# What happened?

* Galaxy started logging into the terminal from which it is run.
* Galaxy created a Python virtual environment (venv) in `.venv/`.
* Galaxy sourced this environment (`$ source .venv/bin/activate`)
* Galaxy fetched needed Python binaries ('wheels') into this environment.
* Galaxy created the default SQLite database and migrated it to the latest version.
* Galaxy bound to the default port `8080` on `localhost`.

.footnote[All of the above can be configured.]

---
# Look around

1. Run a basic job (e.g. upload a file).
1. Check http://localhost:8080/api/version to see Galaxy's version.
1. Stop Galaxy by terminating the console process (`CTRL+C`).

---
# Basic configuration

- Galaxy works out of the box with default configuration.
- Most important config files are in `config/`.
- Galaxy often uses the files with suffix `*.sample` as declared defaults.

---
# Exercise: make your own config

* Copy the provided sample and open editor.
```shell
$ cp config/galaxy.ini.sample config/galaxy.ini
$ nano config/galaxy.ini
```
* Set the following entries.
```shell
message_box_visible = True
message_box_content = Hey, at least I'm not a popup!
message_box_class = info
```
* (Re)start Galaxy.

---
# Update the welcome page

Welcome page is `$GALAXY_ROOT/static/welcome.html` and is the first thing that
users see. It is a good idea to extend it with things like:
- Downtimes/Maintenance periods
- New tools
- Publications relating to your Galaxy

No restarting is necessary.

???
You can load remote content to this iframe (blog, existing presentation, etc.).

---
# Make yourself an administrator

* Create a user account using the Galaxy UI (Login or Register -> Register) <sup>[1]</sup>
* Modify `config/galaxy.ini` to include e.g. `admin_users = foo@example.org`
* (Re)start Galaxy.

.footnote[<sup>[1]</sup> Registering in the UI *before* setting `admin_users` is not strictly necessary, but is the best security practice]

---
# Start/stop in the background

Start:
```shell
$ sh run.sh --daemon
  ...
Activating virtualenv at .venv
Entering daemon mode
```

Monitor:
```shell
$ tail -f paster.log
  ...
Starting server in PID 1469.
serving on http://127.0.0.1:8080
^C
```

Stop:
```console
$ sh run.sh --stop-daemon
```

---
# Be secure

You are running Galaxy as an **admin** user with **sudo** privileges (that's bad)!

We'll fix this shortly.

---
# What to do next?

- Keep your code up to date
- Install tools
- Join the mailing list
- Set up a backup process for your instance
- Configure for production
