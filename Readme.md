# Backdating GitHub Commits for Fun and Profit | Analysis of gitfiti.py

Analysis by Jonas Pertschy

`tl;dr.` Backdating GitHub commits is a way to make a GitHub profile look more active. Commits should not be trusted as a measure of actual activity, as their timestamps can be easily 
spoofed and backdated.

## gitfiti
According to the project description, gitfiti is a noun and stands for carefully crafted graffiti in a GitHub commit history graph.

The project is hosted on GitHub and can be found [here](https://github.com/gelstudios/gitfiti). Gitfiti is creating graphics on GitHub profiles by backdating commits.

The project is written in Python and is a single script that can create commits with arbitrary dates. The script analyses the GitHub history of an account and then creates as many 
commits as needed to display bitmap art on the GitHub profile page.

The Python script creates a Bash or PowerShell script based on the following two templates:

```bash
'#!/usr/bin/env bash\n'
'REPO={0}\n'
'git init $REPO\n'
'cd $REPO\n'
'touch README.md\n'
'git add README.md\n'
'touch gitfiti\n'
'git add gitfiti\n'
'{1}\n'
'git branch -M main\n'
'git remote add origin {2}:{3}/$REPO.git\n'
'git pull origin main\n'
'git push -u origin main\n'
```

The main part of the script that creates the backdated commits is actually a one liner:
```bash
GIT_AUTHOR_DATE={0} GIT_COMMITTER_DATE={1}
git commit --allow-empty -m "gitfiti" > /dev/null\n
```

### Example usage of gitfiti.py in a Docker container

In the following tutorial, I will show how to use the gitfiti script to create a bash script that creates a repository with backdated commits. The resulting script is called 
`gitfiti.sh` and stored in the `gitfiti` directory. For security reasons, we execute all code in a Docker container.

This will start a new container and open a shell:
```bash
docker run -it --rm alpine
```

 We will now install the required dependencies:
```bash
apk add git python3
```

We clone the gitfiti repository:
```bash
git clone https://github.com/gelstudios/gitfiti
```

We execute the gitfiti script:
```bash
python3 ./gitfiti/gitfiti.py

 ____ _(_) /_/ __(_) /_(_)
 / __ `/ / __/ /_/ / __/ /
 / /_/ / / /_/ __/ / /_/ /
 \__, /_/\__/_/ /_/\__/_/
/____/

Enter GitHub URL (leave blank to use https://github.com/):
Enter your GitHub username: [Your GitHub username]
/gitfiti # ame of the repository to use by gitfiti: gitfiti
/gitfiti # umber of weeks to offset the image (from the left):
/gitfiti # gitfiti.py matches the darkest pixel to the highest
/gitfiti # clears found in your GitHub commit/activity calendar,

Currently this is: 0 commits

Enter the word "gitfiti" to exceed your max
(this option generates WAY more commits)
Any other input will cause the default matching behavior
> default
Enter file(s) to load images from (blank if not applicable)
>
Enter the image name to gitfiti
Images: kitty, oneup, oneup2, hackerschool, octocat, octocat2, hello, heart1, heart2, hireme, oneup_str, beer, gliders, heart, heart_shiny
> hackerschool
Enter the target shell (bash or powershell): bash
gitfiti.sh saved.
Create a new(!) repo named gitfiti at https://github.com/ and run the script
````

Once the script completes, we have a `gitfiti.sh` script that we can use to create a repository with backdated commits. The logic looks like this

```bash
#!/usr/bin/env bash
REPO=gitfiti
git init $REPO
cd $REPO
touch README.md
git add README.md
touch gitfiti
git add gitfiti
GIT_AUTHOR_DATE=2022-01-16T12:00:00 GIT_COMMITTER_DATE=2022-01-16T12:00:00 git commit --allow-empty -m "gitfiti" > /dev/null
GIT_AUTHOR_DATE=2022-01-16T12:00:00 GIT_COMMITTER_DATE=2022-01-16T12:00:00 git commit --allow-empty -m "gitfiti" > /dev/null
GIT_AUTHOR_DATE=2022-01-16T12:00:00
[...]
GIT_AUTHOR_DATE=2022-02-26T12:00:00 GIT_COMMITTER_DATE=2022-02-26T12:00:00 git commit --allow-empty -m "gitfiti" > /dev/null

git branch -M main
git remote add origin git@github.com:YourUsername/$REPO.git
git pull origin main
git push -u origin main
```

### Executing the script

We can now execute the script. Please note that it can take up to one day for the commits to appear on the GitHub profile page as a graph.

```bash
bash ./gitfiti.sh
```


### Detecting backdated commits and gitfiti activity
Indicators of backdated commits are:

- Similar commit messages, such as `gitfiti`.
- Multiple commits on the same day at the same time.
- Commits in a repository with an empty `README.md` file and no other files with the exception of an `gitfiti` file.


## Conclusion
Trust but verify. Inspect code and the commit messages. 

