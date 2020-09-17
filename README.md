# git

### "Help, I keep getting a 'Permission Denied (publickey)' error when I clone/push!"

This means, on your local machine, you haven't made any SSH keys. Not to worry. Here's how to fix:

1. Type `cd ~/.ssh`. 

2. Within the `.ssh` folder, there should be these two files: `id_rsa` and `id_rsa.pub`. These are the files that tell your computer how to communicate with GitHub, BitBucket, or any other Git based service. Type `ls` to see a directory listing. If those two files don't show up, proceed to the next step. __NOTE:__ Your SSH keys must be named `id_rsa` and `id_rsa.pub` in order for Git, GitHub, and BitBucket to recognize them by default.

3. To create the SSH keys, type `ssh-keygen -t rsa -C "your_email@example.com"`. This will create both `id_rsa` and `id_rsa.pub` files.

4. Now open `id_rsa.pub`

5. Copy the contents--exactly as it appears, with no extra spaces or lines--of `id_rsa.pub` and paste it into GitHub and/or BitBucket under the Account Settings > SSH Keys.
__NOTE:__ I like to give the SSH key a descriptive name, usually with the name of the workstation I'm on along with the date.

6. Now that you've added your public key to Github and/or BitBucket, try to `git push` again and see if it works. It should!

More help available from [GitHub on creating SSH Keys](https://help.github.com/articles/generating-ssh-keys) and [BitBucket Help](https://confluence.atlassian.com/bitbucket/set-up-ssh-for-git-728138079.html).

