## Note

This project is evolving from 'web-starter-hugo' which is on the git branch 'wsh-1.0'.
It is rudimentary but usable.

# HuMC

(aka Hugo Master Controller or Hugo Multisite Controller)

HuMC is:

- A script, 'hum' to help manage, develop and deploy a collection of web sites built with [Hugo]().
- A starter site for [Hugo]() (it may become several possible starter sites).
- A starter theme for [Hugo]() based on [HTML5 Biolerplate]() called Bunait

In my head I pronounce HuMC as 'hum'.


## Dependencies

The new-site task relies on sed, this works fine on Linux but some BSD based sed versions dont allow case insensitive search - AFAIK that includes OSX - so install gnu-sed uisng your favourite package manager (brew, macports, fink etc).

You'll need hugo of course. You'll need a hugo-extended version to have the PostCSS asset processing in Bunait work. I use dkebler's script detailed on the [Hugo discourse Forum](https://discourse.gohugo.io/t/script-to-install-latest-hugo-release-on-macos-and-ubuntu/14774/14) - note the script may need tweaked for your linux distro, read the discussion there.

```bash
wget https://download.kebler.net/hugo-update
chmod +x hugo-update
# Check the script contents before running it!
sudo mv hugo-update /usr/local/bin
sudo hugo-update
```

You'll need nodejs installed for some asset processing.

PostCSS is used to process CSS. Install postcss-cli globally, it is not listed as a dev dependency in package.json of the Bunait starter theme.

```bash
npm i -g postcss-cli
```

The setup task will check if these are installed, with the exception of gnu-sed.

## Note
 _Commands documented below always use the short cli flag. If you wish to learn the long option name run:_ 'hum -h'.


## Install & setup

Clone HuMC from github.

Create a symbolic link into your $PATH for easier use.
```bash
sudo ln -s /path/to/HuMC/hum /usr/local/bin/hum
```

Run setup task.

```bash
cd HuMC
hum --setup
```

Test HuMC.

```bash
cd starters/five-page
hugo serve
```

And visit http://localhost:1313 to check everything is working.
Use 'ctrl-c' to quit server.

## Init configuration or clone existing

Hum uses the sites/conf directory to store configuration files for each site. Hum will create this path and init a git repository when --create-conf is run.

```bash
hum -c
```
_However_ if you use multiple devices or collaborate with other site developers you'll need portable configuration of the sites HuMC manages. On the first HuMC instance add a remote to the git repository in sites/conf and push.

On further installations of HuMC you wish to keep in sync you should clone this conf repository instead of running 'hum -c'.


## Start a new site

From within the HuMC directory run 'hum --new-site SITENAME' e.g. for a site named 'awesome' do:

```bash
hum --new-site awesome
```

Or using the short cli flag:

```bash
hum -n awesome
```

This will create a new site at 'sites/awesome' with a the base theme bunait 'sites/awesome/theme/bunait' ready to go. It will also have run 'git init', 'git add .', 'git commit' and created staging plus production branches.

(Optional, but wise) Next add a git remote and push the starter code before you begin work. (An automated git deploy setup might make this redundant in future.)
Use the '--all' flag to push all branches at once:

```bash
cd sites/awesome
git remote add origin gitea@gitea.example.com:username/awesome.git
git push -u origin --all
```

It's a good idea to set the baseURL for the staging and production sites at this point if you know  what they'll be. For our example site 'awesome' edit 'sites/awesome/config/staging/config.toml' and 'sites/awesome/config/production/config.toml'.

To view your new site and begin work change directory and launch Hugo's built in server:

```bash
cd sites/awesome
hugo server
```

Then visit localhost:1313 in your browser as normal for Hugo.


## Workflow

### Keeping synced instances up to date

Run with -u to update the sites/conf directory, then -p to update sites/sitename working copies (note that currently this only updates the checked out branch, not all branches) and finally with -j to update node_modules in any site directories. 

```bash
hum -u
hum -p
hum -j
```

### Development

cd into sites/sitename and run 'hugo serve'. Normal Hugo site development applies.

The new site command creates staging and production branches in addition to master. Developement happens on master, the use of staging and production branches should be self evident!

The site is created with a duplicate of the bunait theme. Bunait is intended as a starter theme which you modify per site. You can add any other theme's you choose, however - don't forget to update hugo config to use them.

### Content editing

Content should be edited as normal.

### Deployment

Deployment will be assumed to be via Git.

Add a post commit script to your git repository that deploys code from the staging branch to a staging site and ditto for production.

(TODO: Add an example post-commit script to the repository and document this in more detail. [Issue here](https://github.com/adrinux/HuMC/issues/72).)

## List of available hum commands

- 'hum -s' Check hum setup.
- 'hum -c' Creates the sites/conf directory
- 'hum -u' Update the sites/conf directory working - copy.
- 'hum -p' Update sites/sitename working copies including checking out any new sites.
- 'hum -j' Update node_modules in theme folders (use after hum -p).
- 'hum -n sitename' Creates a new site in sites/sitename with config file in sites/conf/sitename.conf.
- 'hum -l' List sites.
- 'hum -k' Clean sites/ directory. Any site found in sites/ but without a matching sitename.conf will be archived.
- 'hum -a' Archive site. Creates a tar+zip of the named site and moves it to archived/ then removes the sitename.conf file and updates plus pushes changes to the conf repository.
