# DigitalOcean

DigitalOcean is a VPS provider that offers a number of pre-prepared
virtual machine images to start from. The guide below will show how to
deploy a Ruby on Rails application, built on Shippable, to DigitalOcean
using Capistrano deployment tool.

> **Note**
>
> Some points from this guide were adapted from [DigitalOcean tutorial on Capistrano](https://www.digitalocean.com/community/tutorials/how-to-automate-ruby-on-rails-application-deployments-using-capistrano).
> Please refer to if for more detailed instructions about steps in the process.

## Creating and preparing a DigitalOcean droplet

First, we need to create a virtual machine, which is called 'droplet',
in the DigitalOcean web interface. After clicking
[Create](https://cloud.digitalocean.com/droplets/new) in the
administration panel and picking hostname and size for the machine, we
choose 'Applications/Ruby on Rails on 14.04 (Nginx + Unicorn)' as the
base image for our machine. After about a minute the machine should be
booted and ready for configuration.

Capistrano works by fetching the application code from a git repository
and requires `git` to be installed:

```bash
# apt-get install git
```

It is recommended for security reasons to create a separate user for
performing the deployment, as Capistrano will need to be able to
establish a SSH session to the server to execute commands in its
deployment workflow.

```bash
# adduser deployer
# passwd -l deployer # disable logging with password
```

Then, prepare the directory structure with correct permissions (see
[Capistrano documentation](http://capistranorb.com/documentation/getting-started/authentication-and-authorisation/#authorisation) for details):

```bash
# chown deployer:www-data /home/deployer
# chmod g+s /home/deployer/
# mkdir /home/deployer/{releases,shared}
# chown deployer /home/deployer/{releases,shared}
```

Next, remove the sample application that is included as a part of the
DigitalOcean image and replace it with a link to a directory that will
house the latest deployed version of the application:

```bash
# rm -rf /home/rails
# ln -sf /home/deployer/current /home/rails
```

Next, we need to authorize the Shippable minion to execute commands on
the droplet. Copy the deployment key that is visible next to
repositories list in the Shippable web interface and create a file
called `/home/deployer/.ssh/authorized_keys` with this key as the
content (you will need to create `.ssh` directory).

Now the host is ready for the Capistrano deployment triggered from
Shippable minion.

## Adding Capistrano to a Rails project

First, you need to install Capistrano on your development workstation,
as well as on the Shippable minion. Simply issue the following command
on your desktop:

```bash
gem install capistrano
```

And add the following `before_install` block to the `shippable.yml`
file:

```yaml
before_install:
  - gem install capistrano
```

Next, add the following entries to the `Gemfile` of your project:

```ruby
group :development do
  gem 'capistrano-rails', '~> 1.1'
  gem 'capistrano-rvm'
end
```

And execute `bundle install`.

> **Note**
>
> `capistrano-rvm` gem automatically detects RVM installation (that is a
> part of the DigitalOcean RoR image) and modifies the environment of
> the shell used by Capistrano to include the Ruby interpreter and
> installed gems. This is a preferred way of loading RVM into the
> environment, as the shell used by Capistrano is [non-login and non-interactive](http://capistranorb.com/documentation/faq/why-does-something-work-in-my-ssh-session-but-not-in-capistrano/).

Now it is time to configure the application itself for Capistrano
deployment. First add default Capistrano configuration files by issuing
the following command in the repository root at your workstation:

```bash
cap install
```

Among the others, it will generate file called `Capfile`. Add the
following lines to it to turn on the Capistrano Rails, Bundler and RVM
support:

```ruby
require 'capistrano/rails'
require 'capistrano/bundler'
require 'capistrano/rvm'
```

## Configuring Capistrano deployment

Capistrano configuration is split between the `config/deploy.rb` file
that holds the entries common for all the environments and the
`config/deploy/` directory that contains configuration files specific
for environments known to Capistrano.

In this example, we use our DigitalOcean droplet to host both
application, web and database servers for the production environment. In
file `config/deploy/production.rb` change first three uncommented lines
to the following to reflect this (replacing the IP address with the one
of your virtual machine):

```ruby
role :app, %w{deployer@104.131.177.214}
role :web, %w{deployer@104.131.177.214}
role :db,  %w{deployer@104.131.177.214}
```

Next, add the following entries to the `config/deploy.rb` file:

```ruby
set :application, 'shippable_capistrano_test'
set :repo_url, 'https://github.com/shippableSamples/sample-rubyonrails-capistrano-digitalocean.git'
set :deploy_to, '/home/deployer'
set :linked_files, %w{.env}
```

The last line will result in symlinking the `/home/deployer/shared/.env`
file to the directory that contains the current application release
(`/home/deployer/current`). Using this mechanism, we can keep the
secrets and password out of the application git repository. We add extra
gem at the top of the `Gemfile`:

```ruby
gem 'dotenv-rails'
```

And create `.env` file that contains all the 'secret' variables:

```bash
SECRET_KEY_BASE=0b2827a01bbcacde4a1b77386eff145cb8f65625375d64dd33e4e1490e0edbdca4ee7bb14d120f8fc04b6a327bffa5cdafb1666cf5f88ce470f521356663cc34
DATABASE_PASSWORD=l3iIfV6atH
```

This file should be kept secure and not checked in to the version
control (`.gitignore` entry should be created for it). It needs to be
manually copied to each server in the `/home/deployer/shared/` location.
Next, the variables can be used in the Rails configuration files (such
as `config/secrets.yml` and `config/database.yml`) as follows:

```ruby
production:
  secret_key_base: <%= ENV["SECRET_KEY_BASE"] %>
```

Finally, we can add `after_success` step to the `shippable.yml`,
ordering deployment to the production after successful build:

```yaml
after_success:
  - cap production deploy
```

## Using private git repositories

In the sample `config/deploy.sh` file above, we are using public GitHub
repository to host the application code. If the repository is private,
additional steps need to be taken in order to grant access for the
DigitalOcean droplet. There are several methods of achieving it, but the
most convenient one is to use Shippable deployment key by leveraging SSH
agent key forwarding.

First add the Shippable key to the deploy keys in the settings of the
GitHub repository. Next, we need to modify the repository url in
`config/deploy.sh` to use SSH instead of HTTPS:

```ruby
set :repo_url, 'git@github.com:shippableSamples/sample-rubyonrails-capistrano-digitalocean.git'
```

Next, we need to launch SSH agent and add Shippable key, so it can be
forwarded and used by the git command on the droplet:

```yaml
after_success:
  - eval `ssh-agent -s`
  - ssh-add
  - cap production deploy
```

We invite you to explore our Ruby on Rails + Capistrano sample at
[Shippable GitHub account](https://github.com/shippableSamples/sample-rubyonrails-capistrano-digitalocean).
