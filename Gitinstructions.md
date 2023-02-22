# Git Instructions 

## Written by Dr. Sean Burns

### Setup Git and GitHub

#### Setup GitHub

On GitHub.com:

1. Setup GitHub account

2. Make email address private (optional)

..* Go to Settings -> Emails

..* Choose Keep my email address private, if you want to keep your email private

3. Create an SSH key on your Linux server with the following command. Use the email you signed up with GitHub, surrounded with double quotes, in the command below:

```

ssh-keygen -t ed25519 -C "your_email@example.com"

```

4. Add SSH public key to GitHub. To add your public key to GitHub:

..* On GitHub, go to Settings SSH and GPG keys
..* Click on New SSH key
..* On Linux server, use nano to open the file: ~/.ssh/id_ed25519.pub.
..* Copy contents (mouse should work).
..* Paste contents into the New SSH key text box on GitHub.

5. Create a new repository. E.g., my repo is named syslib. You can name yours whatever you'd like.

Read more GitHub documentation here:

[docs.github.com](https://docs.github.com/en).
[How to create ssh key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent).


#### Setup Git

On your Linux server:

Configure ` git`  on your remote server.

..1. Configure your identity and some other defaults. From the command prompt:

```
git config --global user.name "First Name Last Name"
git config --global user.email "firstlast@example.com"
git config --global core.editor nano
git config --global init.defaultBranch main

```

..2. Check your settings:

```
git config --list

```
Read more `git` documentation here:

[git-scm.com](https://git-scm.com/).

#### Use git on remote server

On your Linux server:

1. Make new directory in your home directory. For example, I created a directory called repos on mine.

2. Change into the new directory with the `cd` command.

3. Get the `git` URL from the green **Code** dropdown box on your GitHub repo site. You will clone your repo using a command that looks ike:

```
git clone git@github.com:{your_user_name}/{your_repo_name}.git

```

4. Add files in cloned repo.

5. Track changes with:

```
git add .

```
6. Commit changes and add message with:

```
git commit -m "add message in quotes"

```

7. Push changes to remote repo with:

```
git push origin main

```
Going forward, each time you make edits or add new files, simply repeat the following commands to sync your local repo to the GitHub repo:

```
git add .
git commit -m "add message in quotes"
git push origin main

```

#### Editing on GitHub

If you edit a file on GitHub, to sync to your local repo on your server, run the following command:

```
git pull origin main

```

#### Markdown

Visit Markdown documentation here:

..* [Markdown Documentation](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).
..* [Markdown Cheatsheet](https://markdownguide.org/cheat-sheet/).

It's okay to only know Markdown basics. The whole point of Markdown is to simply writing text that can be converted to other formats, like HTML, PDF, DOCX, ODT, etc.
