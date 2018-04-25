# Traefik Configuration

This took me far too long to figure out. Now that I know what I'm doing it was easy, but getting there was half the battle.

Maybe this will help someone else out?

## Configurations

There are two configurations. One for regular ole `http`(which is great for dev env's). The other is for lets encrypt ssl terminated certs.

### To run

Here is the key. You'll need to run this outside of docker compose. Compose by default will create a default network for the project, whcih is great, except traefik needs to add containers automagically, and in doing so will require you to add all the containers to the default network. Bummer. So you'll either need to be in with compose or regular ole docker. If you want to use compose, you'll need to ensure you're adding the correct traefik label to join the correct network.

`docker run -d -p 443:443 -p 8080:8080 -p 80:80 -v $PWD/traefik-https.toml:/etc/traefik/traefik.toml -v /var/run/docker.sock:/var/run/docker.sock --name traefik traefik`

Be sure to update your information in the toml files. If you do a search for `kcmerrill` and adjust according to your repo you should be ok.

### Last but not least ...

At this point, you should have a reverse proxy that will automagically add in containers as they come and go. Great, but how do you set a default container. If you're using something like `dev.kcmerrill.com`, or some other dns entry that points to `127.0.0.1` OR you just want something to serve traffic on your top level dns entry, in my case `https://kcmerrill.com`, you'll need to add a specific label to your docker run command. Notice the label:

`docker run -d -P -v $PWD:/var/www/html --name ${PWD##*/} --label 'traefik.frontend.rule=Host:kcmerrill.com' kcmerrill/base`
