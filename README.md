# Hello CTF Builder!

_Any questions/queries can be directed to C_Sto on the challenge development Slack_

This is some basic documentation on how to build your challenge(s) so they work in harmony with the others and in the production Docker environment. This repo demonstrates how the entire games directory structure is to look like. We've provided you a set of sample files that represent different challenges to show how your contribution is expected to fit in with the other ones!

This assumes a few things, and because you've been approached to contribute challenge(s), we're sure you can handle it.

- That you know what Docker is, or, that you are capable of googling,
- You understand you can't run a Windows container on an Ubuntu host Docker,
- You understand there will only be one container per challenge and that the game organisers won't be expected to revert it when it gets rooted and `rm -rf`'ed,
- You can follow instructions to install Docker good, and
- You know what category and tier challenge(s) you are working on

## Challenge Difficulty

We've all played CTFs where the challenges have been super edge case or confusing and have left us frustrated. WACTF hopes to bring an element of realism into the CTF by modeling challenges from scenarios that aren't so far fetched. If you have a CTF idea that involves "z-base-32 encoded hex strings" it's unlikely going to be a good fit for WACTF. Some other things to keep in mind:

- Avoid letting players get root (reasoning mentioned above)
- TBD

The tier of challenge(s) you're developing correspond to the level of difficulty it's expected to be. As we have a defined scope for the players of this CTF we have provided some guidance to the level of difficulty below:

| Tier | Expected Difficulty|
|----|----|
|1| Solvable by anyone with a decent level of problem solving skill. High school students should fare well|
|2| Solvable by those with limited cyber security skill, such as early Comp Sci / Cyber Sec University / TAFE students or people who have completed a CTF (such as Pico) before|
|3| Solvable by students close to completing higher education (or recent graduates) and who have participated in CTFs or have taken cyber security training before|
|4| Solvable by people new to the cyber security industry (up to 1 year of fulltime employment)|

## Expected deliverable

Refer to `Challenge Documentation Standard v*.docx` in this repo. Ensure your deliverable meets the requirements!

## Docker, Docker Compose & Host AppArmor

The Docker host will be in the AWS cloud. The base OS will be Ubuntu `16.04.2 LTS`

**Prod version of Docker:**
```
docker-ce | 17.03.1~ce-0~ubuntu-xenial | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
```

Follow Docker instructions on how to get this version on the Docker website. (eg https://docs.docker.com/engine/installation/linux/ubuntu/ )

You can get this on Kali by doing the following:

```
echo \
 "deb [arch=amd64] \
 https://download.docker.com/linux/debian \
 stretch stable" > \
 /etc/apt/sources.list.d/docker.list
curl -fsSL \
 https://download.docker.com/linux/debian/gpg | \
 sudo apt-key add -
apt update
apt install docker-ce=17.03.0*
```

Note that AppArmor on Debian may require a different setup process than what is included in this guide!

**Prod version of docker-compose:**
```
version 1.13.0
```
```
version 3.0 YML syntax
```

You can get the correct version of docker-compose by running the following (or clone it from GitHub):
```
pip install docker-compose==1.13.0
```

**Prod AppArmor profile:**
https://github.com/moby/moby/tree/master/contrib/apparmor
Commit `4bf7a84c969b9309b0534a61af55b8bb824acc0a`

Docker containers use a default AppArmor profile ( https://docs.docker.com/engine/security/apparmor/ ) but we are taking an additional defense in depth step by also applying an AppArmor profile to the Docker host itself, this has the added benefit of better visibility of security events occurring on the host during the game. You're going to want to configure your dev environment to use the Docker host AppArmor profile to ensure it will work when merged into the production environment.

Simply, grab the `Go` files from the repo above, spend some time getting Go configured on your dev machine, fix the dependency issues in the Go script by cloning some other repos. Compile and run it, get an AppArmor profile and enforce it on the Docker host daemon. **Better instructions coming... maybe**


 Get these versions of Docker/docker-compose/AppArmor profile to ensure that your challenge(s) will work as expected in the game environment. Other versions of Docker *may* work, but don't be that guy.
 _If a challenge doesn't work in QA, and we find out it's because of Docker versioning, we may choose to just scrap it from the game. Don't be that guy/girl._


 ## Autonomy

 Your challenge(s) are expected to work out-of-the-box.
 A game organiser should only need to run `docker-compose up` and your Dockerfile/docker-compose file should execute any startup scripts necessary to bring the challenge to its fully working state.

## Port Ranges

To make (our) life easier, we have decided to allocate ports based on challenge type:

| Port | Challenge Category|
|----|----|
|1000| Web|
|2000| Forensics|
|3000| Crypto|
|4000| Exploitation|
|5000| Misc|

And also on challenge 'tier'. The second digit in the port number will correspond do the tier. So, for example;

|Port | Challenge|
|---|---|
|1100 |Web tier 1 |
|4400| Exploitation tier 4|

If you need more than 100 ports, let us know and we can work something out.

## Flag Format

Flag format is: `WACTF{FLAG_OF_YOUR_CHOICE}` - For challenges that players may be able to `strings` (or similar) the flag when you don't want them to, you can specify your own flag format, ensure this is documented in the challenge doco provided with your deliverable.

## What Now?

Go read the included `docker-compose` file and the `README` and `Dockerfile` in the `your-challenge` directory.

Now here's some handy commands that will make your docker-dev life better:

|Command|What it does|
|---|---|
| docker-compose up | This starts the Docker containers. You can use the `some-other-challenge` and `some-other-challenge-2` Docker images so that you can make sure that your container plays nice with others. |
| docker-compose up -d | This does the same as above, but launches in daemon mode so you don't have to do the "How do I exit VIM???" thing |
| docker-compose stop | This will stop the Docker containers |
| docker-compose rm | This removes the containers, you will want to do this if you are devving things (you won't have to download the base images again) |
| docker-compose build | This will re-build the Docker containers (if you make a change, rm then do this) |
