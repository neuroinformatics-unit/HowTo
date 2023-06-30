# Update cookiecutter-generated files with cruft

Ref: [original docs](https://cruft.github.io/cruft/) and [github repo](https://github.com/cruft/cruft).

## Get started
Install: `pip install cruft`.   

If this is your first time using `cruft`, you have to specify the URL of your cookiecutter on GitHub.   

You will also need to specify the cookiecutter commit hash, i.e. the older version of cookiecutter you used to generate the repo. For now, we have to do this manually by searching the history. Example commit hash: `9daec12bffbc32da6a252605c5e67c90028fefc0`.   

Once you have the hash, you can run `cruft link https://github.com/neuroinformatics-unit/python-cookiecutter`. This will create a `.cruft.json` file in your repo containing the URL and the commit hash. Please track the changes to this file with git.

## Next time you use `cruft`...
If you already have used `cruft link` you should find a `.cruft.json` file, containing the latest commit hash used.

## Diffs
Now you can run `cruft diff` to see the changes between your repo and the template. If you are happy with the changes, you can run `cruft update` to update your repo. This will not overwrite all changes you have made, but only the files that have been changed in the template.

In the case `cruft update` is not able to apply all the changes, it will create a `your_file.rej` file. You can then manually apply the changes to the files. This is a bit tedious, but it's the only way I have found to do it so far.

> **Note**
> I have found that although the link to the repo is correct (`github_repository_url` field), the command `cruft diff` fails, so you might have to change it manually in the `.cruft.json` file to "provide later". 🤷🏻‍♀️

`cruft check` will check if your repo is up to date with the template, and basically will just give you a boolean response.

## Commit
Once you are satisfied, commit the changes to a new branch and open a PR to merge it into `main`. 

Now, check if your GitHub actions fail... 🤦🏻‍♀️ If everything is fine, you can merge the PR. 🎉 If not, you will have to fix the errors and repeat the process. 😱
