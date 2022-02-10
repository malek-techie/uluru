# README

Uluru (/ˌuːləˈruː/; Pitjantjatjara: Uluṟu [ˈʊlʊɻʊ]), also known as Ayers Rock (/ˈɛərz/ AIRS) and officially gazetted as Uluru / Ayers Rock,[1] is a large sandstone formation in the southern part of the Northern Territory in Australia.

This boilerplate project helps when you need to start a new rails based project at any kickoff context.

## Dependencies

You'll need:

- [Ruby](https://www.ruby-lang.org/en/) it's advised to use [RVM](https://rvm.io/) or [rbenv](https://github.com/rbenv/rbenv) and current version is placed on `.ruby-version`
  rvm install ruby 3.1.0
  rbenv install 3.1.0

- [NodeJS](https://nodejs.oirg/) it's advised to use [NVM](https://github.com/nvm-sh/nvm) and current version is placed on `.nvmrc
- [yarn](https://yarnpkg.com/en/docs/install)

:warning: Also sure to setup these local services:

- [PostgreSQL](https://www.postgresql.org/)
- [Redis](https://redis.io/)

:robot: Also remember to check if you have some libraries installed on your O.S.:

- [imagemagick](https://imagemagick.org/) (Search binaries for your platform)

Tip: Use [Docker](https://www.docker.com/) with the official [MongoDB](https://hub.docker.com/_/mongo/) and [Redis](https://hub.docker.com/_/redis/) images. If you have [Docker Compose](https://docs.docker.com/compose/) installed, running `docker-compose up -d` will start these services.

Tip: Install bundler based on your Gemfile.lock to avoid warnings
  gem install bundler -v "$(grep -A 1 "BUNDLED WITH" Gemfile.lock | tail -n 1)"  

To test e-mail sending in development, you should use [MailCatcher](https://mailcatcher.me/).
1. Install the gem:
    ```$
    gem install mailcatcher
    ```
2. Run it:
    ```$
    mailcatcher
    ```
3. Go to http://127.0.0.1:1080/ to see the emails being sent.

### Doppler Setup

- If you're using Windows Linux Subsystem (WSL), read `WSL Preparation` below.
- Install [Doppler CLI](https://docs.doppler.com/docs/cli)
- Logout from Doppler web dashboard if you're logged in
- Run:
```bash
doppler login # If Doppler opens multiple tabs on your browser, be sure to use the last one.
doppler configure set project esteira
doppler configs create dev_[your_name_or_nickname]
doppler setup # Select `esteira` and your recently created configuration branch
```

#### Doppler Aliases
In order to make development easier, a few aliases to run rails with doppler may prove useful. Run the following commands in your terminal in order to set them up.

###### Start Rails server

```
alias drails=‘doppler run -- rails s’
```

###### Start Rails console
```
alias crails=‘doppler run -- rails c’
```

##### Start sidekiq instance with access to Doppler env variables
```
alias dsidekiq=‘doppler run -- bundle exec sidekiq’
```

## Setup steps

1. Setup [Doppler](https://doppler.com/) to configure your environment variables. Read more below.
2. Run `docker-compose up -d` to start MongoDB and Redis instances if you are using Docker.
3. Run `bundle install` to fetch all necessary gems.
3. Run `rails db:seed` to prepare your local database with development data.
4. Run `yarn install`
5. Run `doppler run -- bundle exec sidekiq` to give sidekiq access to Doppler environment variables.
6. Run `doppler run -- rails s` to start rails server and start development :)

#### Run tests faster
```
  # setup
  bundle exec rake parallel:create
  bundle exec rake parallel:prepare

  # run after migrations
  bundle exec rake parallel:migrate

  # run faster
  bundle exec rake parallel:spec
  bundle exec rake "parallel:spec[spec/lib]" # similar to 'rspec spec/lib' (quotes needed for zsh)
```

#### WSL Preparation

This will install needed dependencies and setup gnome-keyring, which is necessary to store Doppler secrets.

```
sudo apt-get install xdg-utils gnome-keyring dbus-x11
cat <<-EOS >> ~/.bash_profile

ssh-add -l &>/dev/null
if [ "$?" == 2 ]; then
  test -r ~/.gnome-keyring && \
    source ~/.gnome-keyring && \
    export DBUS_SESSION_BUS_ADDRESS GNOME_KEYRING_CONTROL SSH_AUTH_SOCK GPG_AGENT_INFO GNOME_KEYRING_PID

  ssh-add -l &>/dev/null
  if [ "$?" == 2 ]; then
    (umask 066; echo `dbus-launch --sh-syntax` > ~/.gnome-keyring; gnome-keyring-daemon >> ~/.gnome-keyring)
    source ~/.gnome-keyring && \
    export DBUS_SESSION_BUS_ADDRESS GNOME_KEYRING_CONTROL SSH_AUTH_SOCK GPG_AGENT_INFO GNOME_KEYRING_PID
  fi
fi
EOS
source ~/.bash_profile
```

## Webapp.io

We use [Webapp.io](https://webapp.io/) as our continuous integration service. After each push in a opened pull request the code from given
branch will be run in our CI pipeline. You may need to access the progress of that processing and it can be done logging in LayerCI using
your github account and them accessing [our projects page](layerci.com/bxblue).

## Issues

##### On Ubuntu 19.04

- You could get an error related to wicked pdf that has no binary to the platform above.
  Notice that ~/.rvm/gems/ruby-2.6.4/gems/wkhtmltopdf-binary-0.12.5.1/bin has no binary for ubuntu 19.04.

  Fix: just cp from wkhtmltopdf_ubuntu_18.04_amd64.gz to wkhtmltopdf_ubuntu_19.04_amd64.gz

  Now you can get your stuff running!


- When running tests if you get an error related to ImageMagick like this: attempt to perform an operation not allowed by the security policy

  * You must edit the file at /etc/ImageMagick-6/policy and replace

    => policy domain="coder" rights="none" pattern="PDF"

    with

      => policy domain="coder" rights="read/write" pattern="PDF"

- If you run in an error in your Devise related to SassC syntax, we recommend deleting your repository and cloning again(seriously).

##### On Ubuntu 21.04

- You could get an error related to wicked pdf that has no binary to the platform above.
  Notice that ~/.rvm/gems/ruby-2.6.4/gems/wkhtmltopdf-binary-0.12.5.1/bin has no binary for ubuntu 21.04.

  Fix: just cp from wkhtmltopdf_ubuntu_20.04_amd64.gz to wkhtmltopdf_ubuntu_21.04_amd64.gz

  Now you can get your stuff running!


- When running tests if you get an error related to ImageMagick like this: attempt to perform an operation not allowed by the security policy

  * You must edit the file at /etc/ImageMagick-6/policy and replace

    => policy domain="coder" rights="none" pattern="PDF"

    with

      => policy domain="coder" rights="read/write" pattern="PDF"

- If you run in an error in your Devise related to SassC syntax, we recommend deleting your repository and cloning again(seriously).

**Note**: if you use rbenv you want to look inside ` ~/.rbenv/versions/2.6.8/lib/ruby/gems/2.6.0/gems`

##### On macOS

- When installing, if you run into the following error:
  ```
  can't find gem railties (>= 0.a) with executable rails
  ```
It probably means your rails installation is present in the system but corrupted. Run
  ```
    gem install rails
  ```
  in order to update your rails install.
