# Creating your git repository

The easiest way to create a Rails application in a Git/GitHub repository is to start on GitHub, clone an empty repo to your machine, then build your Rails app in it.

So that's what we'll do.

  1. Open your browser and go to the GitHub account where you want to create your project repo.
  2. Click on the **Repositories** tab.
  3. Click on the **New** button to create a new repository.
  4. Add the name of your new repository. The best practice is to use "snake-case", which is all lowercase with words separated by a hyphen: *my-cool-rails-app*.
  5. Give a brief description. This will be used on GitHub and will also be the default text for your README.md file. Always complete this.
  6. Choose public or private.
  7. Check the "Initialize this repository with a README" checkbox. This will cause GitHub to do the initial commit for you and will make it easier to clone and use your app. And you always want a README file, so it's just good practice.
  8. Select a .gitignore file format. I prefer the GitHub version to the one that Rails provides, so I generally select the Github .gitignore file here.
  9. You can choose a license if you wish.
  10. Click **Create Repository** to create the repository.

  You should now have a new repository on GitHub for your project. Let's clone this to your machine.

  If you look to the right on the GitHub page for your new repository, just below the last navigation link for "Settings", you should see a "clone URL" text field and a small button with a clipboard icon next to it. Click the icon to copy the clone URL to your clipboard.

  You can choose to use HTTPS or SSH here. I generally choose SSH. You may need to set up an SSH key on your machine and add it to your GitHub account. If that's the case, try using HTTPS for now.

  Once you've copied the clone URL to your clipboard, open your terminal in the folder that holds your applications. I generally put a folder called **Workspace** in my home folder, and put all my applications in there. As I work with many languages, I have a **ruby** subfolder and I put my Ruby applications in there. So I'll do the following:

  ```
  cd ~/Workspace/ruby/
  ```

  To enter that folder. Then I'll clone the new repo into this folder, and will cd into it:

  ```
  git clone git@github.com:enspiral-dev-academy/tdc.git && cd tdc
  ```

  Finally, I'll open the *folder* in my text editor:

  ```
  atom .
  ```

  (You may use sublime or another editor.)

  If you follow this procedure, then you should see the README and .gitignore files now. You can check them out if you're curious.

  You'll also see a hidden folder called **.git**. **LEAVE THIS FOLDER ALONE**. That is your git repository. There is nothing in there that you'll ever need to mess with, and changing anything could destroy your local repository and make life miserable for a while. Leave it alone.

  ![New repository open in Atom](/images/new-repo.png)
