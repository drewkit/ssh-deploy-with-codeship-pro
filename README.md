# setting-ssh-private-key-in-pro

## Initialize Project

- Clone this project
- Initialize as a new git repo -- `rm -rf .git && git init && git add . && git commit -m 'first commit'`

## Selecting a Private Key

### Option A -- Generate a public and private ssh key

- Modify the following command to your own email address and run in the project directory:

```
docker run -it --rm -v $(pwd):/keys/ codeship/ssh-helper generate "<YOUR_EMAIL>"
```

### Option B -- Use your own pre-existing private ssh key

- Copy key to project directory and rename to `codeship_deploy_key`

## Prepare the Environment Variables file

- Run the following command from the project directory:

```
docker run -it --rm -v $(pwd):/keys/ codeship/ssh-helper prepare
```

- Process will store `SSH_PRIVATE_KEY` value as a one liner entry into the `codeship.env` file. If `codeship.env` already exists, the `SSH_PRIVATE_KEY` entry will be appended to it.
- Remove the `codeship_deploy_key` (!)
- Add `codeship.env` to your `.gitignore` file (!)

## Encrypt the Environment Variables file

- Install our [jet cli tool](https://documentation.codeship.com/pro/jet-cli/installation/)
- From your Project Settings > General page, scroll down to AES key section and click 'Download Key'
- Move downloaded key to your project directory and rename to `codeship.aes`
- Add `codeship.aes` to your `.gitignore` file (!)
- Add [any additional environment variables](https://documentation.codeship.com/pro/builds-and-configuration/environment-variables/#encrypting-your-environment-variables) to the `codeship.env` file
- Run `jet encrypt codeship.env codeship.env.encrypted`
- The `codeship.env.encrypted` will be safe to check into your git repository

## Run `jet steps`

- run `jet steps`
- steps should pass, demonstrating that `/root/.ssh/id_rsa` is now accessible to the main app via volumes
- Be sure to modify the volume pathing to `.ssh` in the `codeship-services.yml` if the container user is not `root`
- Add `.ssh` directory to your `.gitignore` file (!)
