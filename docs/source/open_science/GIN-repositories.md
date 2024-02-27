(target-create-gin-repo)=
# Create a GIN repository for your dataset

[GIN](https://gin.g-node.org/G-Node/Info/wiki) (hosted by the German Neuroinformatics Node) is a free and open data management system designed for neuroscientific data.

 It is web-accessible, based on [`git`](https://git-scm.com/) and [`git-annex`](https://git-annex.branchable.com/), and allows you to keep your data in sync, backed up and easily accessible.

Below we explain the main user workflows in GIN.


## Preparatory steps - do only once

We need to do these steps only the first time we use GIN on our machine.

1. Create [a GIN account](https://gin.g-node.org/user/sign_up).
2. [Download GIN CLI](https://gin.g-node.org/G-Node/Info/wiki/GIN+CLI+Setup#setup-gin-client) and set it up by running:
   ```
   $ gin login
   ```
   You will be prompted for your GIN username and password.
3. Confirm that everything is working properly by typing:
   ```
   $ gin --version
   ```

## Create a GIN repository (local and remote)

1. **Log in to the GIN server**

   Before running any `gin` commands, make sure you are logged in to your account by running:

   ```
   $ gin login
   ```
   <!-- - To list the repositories available to your account: `gin repos --all`. -->

:::{tip}
In Unix-like systems (Ubuntu, Mac), you may need `sudo` permissions for some of the following `gin` commands. If so, remember to prepend all commands with `sudo`.
:::

2. **Initialise a GIN repository**

   - **Option 1: in a new directory**

     - Create a new GIN repository locally and on the GIN server by running:

       ```
       $ gin create <remote-repository-name>
       ```

        <details><summary> <b> OR alternatively: </b> </summary>

       Create a repository in the GIN server [from the browser](https://gin.g-node.org/repo/create), and download it locally to your local workspace by running:

       ```
       $ gin get <username>/<remote-repository-name>
       ```
        You can also copy this command from the GIN browser application, by clicking on the clipboard icon in the repository main site (see red highlight):

        ![](../_static/gin-clipboard.png)
        </details>

     - Once the repository has been initialised, add data to the new local GIN repository by moving or copying the relevant files to the directory.


   - **Option 2: in an existing directory**
     - Move to the relevant directory using `cd`.

     - Create a new repository on the GIN server and locally in the current working directory, by running:

        ```
        $ gin create --here <remote-repository-name>
        ```
        This will create a repository named `<remote-repository-name>` on the GIN server under your user account.

        <details><summary> <b> OR, to do each step independently: </b> </summary>

       - Initialise the current working directory as a GIN repository by running:

          ```
          $ gin init
          ```

       - Then add a remote for your GIN local repository by running:

          ```
          $ gin add-remote <remote-name> <remote-repository-location>
          ```

          where `<remote-name>` is the name you want to give to the remote (e.g. `origin`) and `<remote-repository-location>` is the location of the data store, which should be in the form of alias:path or server:path (e.g. `gin add-remote origin gin:<username>/<remote-repository-name>`).

        - If the remote GIN repository doesn't exist, you will be prompted to either create it, add simply the remote address, or abort.
        - To show the remotes accessible to your GIN account run `gin remotes`.
       </details>

:::{note}
Initialising the GIN local repository (with `gin create` or `gin init`) will create a hidden `.git` subdirectory. You can see it on the terminal by running `ls -la` from the local repository. The local repository excluding this `.git` folder is what we will later call the _working directory_.
:::

:::{tip}
To create a GIN repository on a `ceph` directory:
 - The fastest way is to log to SWC's HPC cluster (specifically, its [gateway node](https://howto.neuroinformatics.dev/_images/swc_hpc_access_flowchart.png) `hpc-gw1`), which has the GIN CLI client installed, and work from there. This is faster than mounting the `ceph` directory in your laptop, since the HPC cluster is in the same network as `ceph` and has a faster connection to it.

 - Alternatively, you can mount the `ceph` directory in your local machine and work from there. To do this temporarily (i.e., until the next reboot), follow [this guide](target-mount-ceph-ubuntu-temp). To do this permanently, follow [this one](target-mount-ceph-ubuntu-perm). You may need to add an exception for git for the mounted directory. To do so, run:
   ```
   $ git config --global --add safe.directory /mnt/<path-to-the-mounted-directory
   ```

:::

3. **Add files to the GIN remote repository**

   It is good practice to keep a record of the changes in the repository through commit messages. To keep a useful and clean commit history, it is also recommended to make small commits by selecting a subset of the files.

   - To make a record of the current state of a local repository, run:

     ```
     $ gin commit --message <message> <filename>
     ```

     You can replace the `filename` above by an expression with a wildcard (e.g., `*.png` to include all png files). It can also be a list of files separated by whitespaces. A filename equal to `.` will include all files with changes. See the full syntax for `gin commit` [here](https://gin.g-node.org/G-Node/Info/wiki/GIN+CLI+Help#record-changes-in-local-repository).

   - To upload all local changes to the remote GIN repository, run:

     ```
     $ gin upload <filename>
     ```

     `filename` accepts the same inputs as `gin commit`. Again, the recommended practice would be to upload data in small-ish chunks. You can run an upload command after a few commits (so not necessarily after each commit).

     You can add `--to <remote name>` to upload the changes to a specific remote. To show the remotes accessible to your GIN account, run `gin remotes`. See the full syntax for `gin upload` [here](https://gin.g-node.org/G-Node/Info/wiki/GIN+CLI+Help#upload-local-changes-to-a-remote-repository).

     If the set of files in the `gin upload` command includes files that have been changed locally but have not been committed, they will be automatically committed when uploading.

     After running `gin upload`, the data will be uploaded to the GIN server and it will be possible to retrieve it later from there. However, notice the upload command sends all changes made in the directory to the GIN server, including deletions, renames, etc. Therefore, if you delete files from the directory on your computer and perform a `gin upload`, the deletion will also be sent and the file will be removed from the server as well. Such changes can be synchronized without uploading any new files by not specifying any files or directories (i.e. simply running `git upload`). See further details in [the docs](https://gin.g-node.org/G-Node/Info/wiki/GIN+CLI+Usage+Tutorial#basic-workflow-only-using-gin).

4. **Consider whether to lock the data**

   You may want to lock the data to save space locally or to prevent editing in the future — see the section on [File locking](#file-locking) for further details.

:::{tip}
 - Use `gin ls` to check on the current status of the GIN repository - this is somewhat equivalent to `git status`. The acronyms used in the output for the different status of the files are described [here](https://gin.g-node.org/G-Node/Info/wiki/GIN+CLI+Help#list-the-sync-status-of-files-in-the-local-repository).
 - Use `gin sync` to sync the changes bi-directionally between the local and the remote GIN repository.
 - If the output from `gin ls` doesn't look right (e.g., files already uploaded to the GIN server appear under `Locally modified (unsaved)`), try running `gin sync` and check the status again.
:::

### Is this repository public or private?

By default, all newly-created GIN repos are private.

To make a GIN repository public:

1. Go to the homepage of the remote repository. You can see the URLs for the repositories you have access to by running `gin repos --all`.
1. Click on _Settings_ (top right).
1. Unselect the _Private_ checkbox under the _Basic settings_ section (see red box below).

![](../_static/gin-privacy-settings.png)

## Update a dataset hosted in GIN

1. To clone (retrieve) a repository from the GIN remote server to a local machine, run:

   ```
   $ gin get <remote-repository-location>
   ```

   You can also copy this command from the GIN browser application, by clicking on the clipboard icon in the repository homepage.

   To see the `<remote-repository-location>`s accessible from your GIN account, run `gin repos --all`.

2. Add files to the directory where the local repository is, and commit them:

   ```
   $ gin commit -m <message> <filename>
   ```

3. Upload the committed changes to the GIN server with:
   ```
   $ gin upload <filename>
   ```

## Download the dataset locally

### If the repository doesn't exist locally:

1. Clone (retrieve) the repository from the GIN remote server to your local machine:

   ```
   $ gin get <remote-repository-location>
   ```

   This command will download the large files in your dataset as lightweight placeholders.

2. To download the content of the placeholder files locally, run:
   ```
   $ gin download --content
   ```
   If the large files in the dataset are _locked_, this command will download the content to the git annex subdirectory, and turn the placeholder files in the working directory into symlinks that point to the content.

   If the files are _unlocked_, this command will replace the placeholder files in the working directory by the full-content files and **also** download the content to the git annex locally. See the section on [File locking](#file-locking) for further details.

### If the repository already exists locally:

1. Download any changes from the remote repository to the local clone by running (from the GIN local repository):

   ```
   $ gin download
   ```

   This command will create new files that were added remotely, delete files that were removed, and update files that were changed.

   With the `--content` flag, it optionally downloads the content of all files in the repository. If `--content` is not specified, new files are added as empty placeholders.

2. The content of individual files can be retrieved using:
   ```
   $ gin get-content <filename>
   ```
   and later removed with:
   ```
   $ gin remove-content <filename>
   ```

   See [the GIN docs](https://gin.g-node.org/G-Node/Info/wiki/GIN+CLI+Help#download-all-new-information-from-a-remote-repository) for further details.

### To download the data programmatically in your Python code:

We recommend using [pooch](https://www.fatiando.org/pooch/latest/index.html) to easily download data from the GIN repo's URL. Pooch also has some other nice functionalities like caching the downloaded data, verifying cryptographic hashes or unzipping files upon download.

## File locking

[File locking](https://gin.g-node.org/G-Node/Info/wiki/GIN+CLI+Usage+Tutorial#file-locking) is an important point in GIN and git-annex repositories which surprisingly comes up quite late in the GIN docs. Below, are the main ideas behind this.

- Files in a GIN repo can be _locked_ or _unlocked_.

- The lock state relates to the nature of the placeholder files we get in the working directory when we fetch the remote repository via `gin get <remote-repository-location>`:

  - **on Unix-like systems** (Mac, Ubuntu):
    - if a file is _locked_, its corresponding placeholder file will be a _symlink_. These symlinks point to the annexed content (under `.git/annex/objects`). With the symlinks we can open the file but not modify it.
    - If a file is _unlocked_, the placeholder file in the working directory is an _ASCII text file with a path_. The path is approximately where the content of the file will be downloaded to when we request it.
  - **on Windows**:
    - if a file is _locked_, the placeholder file is a _plain text file_ pointing to the content in the git annex.
    - If a file is _unlocked_, the behaviour should be the same as in Unix-like systems (I haven't tested this directly).

- The lock state of a file is _persistent_. This means that if I clone a GIN repo whose files are unlocked, I lock them in my local copy, and then upload that to the GIN server, the next time someone clones from the GIN repo the files they fetch will be locked.

- Unlocked files can be edited. If the data is unlocked and the full content of the dataset is downloaded locally, the file in the working directory has content, and so does its copy under git annex.

:::{caution}
Notice that if we download locally the content of unlocked, the disk usage of the files checked into the repo doubles, because the content exists both in the working directory and under the git annex. But in exchange users can modify and revert files to previous commits.
:::

- Locked files cannot be edited. For example, if we open a locked image with Preview in MacOS and try to edit it, we will be asked if we wish to unlock the file. However, even if we do unlock it, we won't be able to save any changes because we don't have writing permissions.

- Files need to be committed before locking.

- We can switch the state for one or more files with:
    ```
    $ gin lock <filename>
    ```
    and
    ```
    $ gin unlock <filename>
    ```
    After changing the state, remember to record the new state with a `gin commit`!

- **Recommendations from the GIN docs on when to lock / unlock data:**
  - Keep files _unlocked_ if the workflow requires editing large files and keeping snapshots of the progress. But keep in mind this will increase storage use with every commit of a file.
  - Keep files _locked_ if using the repo mainly as long term storage, as an archive, if files are only to be read and if the filesystem supports symlinks. This will save extra storage of keeping two copies of the same file.

## Some under-the-hood details...

- GIN is a wrapper around [git-annex](https://git-annex.branchable.com/).

- The high-level idea behind git-annex is:
  - `git` is designed to track small text files, and doesn't cope well with large binary files.
  - `git-annex` bypasses this by using git only to track the names and metadata (hashes) of these large binary files, but not their content.
  - The content of these files is only retrieved on demand.

- How? Case for an unlocked dataset

  - When we `gin download` a repository from the GIN server, we get a local "copy" (clone) of the dataset in our machine. It is not strictly a copy, because the large binary files that make up this dataset will only be downloaded as placeholders.

  - These placeholder files have the same filenames (and paths) as the corresponding original files, but are instead simply ASCII text files (if the data is unlocked). If we open these placeholder files, we see they contain a path. This path is where the actual content of the corresponding file will be downloaded to, when we request it.

  - For example, if the placeholder ASCII text file with name `image.png` points to this path:
    ```
    /annex/objects/MD5-s15081575--f0a21c00672ab7ed0733951a652d4b49
    ```
    it means that when we specifically request for this file's content with:
    ```
    gin get-content image.png
    ```
    the actual png file will be downloaded to:
    ```
    .git/annex/objects/Xq/7G/MD5-s15081575--f0a21c00672ab7ed0733951a652d4b49/MD5-s15081575--f0a21c00672ab7ed0733951a652d4b49
    ```
    Notice that the path in the ASCII file and the actual path are somewhat different, since the actual path contains some subdirectories under `objects`.

    We can actually verify this file is the actual image by opening it with an image viewer, e.g. in Mac by running:
    ```
    open -a Preview .git/annex/objects/Xq/7G/MD5-s15081575--f0a21c00672ab7ed0733951a652d4b49/MD5-s15081575--f0a21c00672ab7ed0733951a652d4b49
    ```

- How? Case for a locked dataset

  - When we `gin download` a repository from the GIN server, we get a local "copy" (clone) of the dataset in our machine. It is not strictly a copy, because the large binary files that make up this dataset will only be downloaded as placeholders.
  - If the data is locked and no content has been downloaded, the symlinks in the working directory will be broken (since there is no data in the git annex to retrieve).
  - To get the actual content in the git annex, we need to run `gin download --content`. This will fetch the content from the GIN server. After this, the symlinks in the working directory should work.

- And in an existing directory?

  - After initialising the GIN repo in the current directory and adding a remote, we would commit the data. When committing, the data is "copied" to the git annex. You can verify this by checking the size of the `.git` folder before and after running `git commit`.
  - When we lock the data with `gin lock <path-to-data>`, the files in the working directory are replaced with symlinks to the git annex content.
  - After locking the data we should commit the state change and upload the changes to the GIN server. This way the files will be locked for any future clones of the repository.

<!-- - Useful tools for inspecting how all this works:

  - `file` shows the type of file (by inspecting the file, rather than plainly looking at the extension like Finder does).
  - `open -a Preview <>` to open a png file that has no extension.
  - `ls -l <path-to-symlink>` to check the path a symlink points to. -->

## Other useful tips

- To [unannex a file](https://gin.g-node.org/G-Node/Info/wiki/FAQ+Troubleshooting#how-to-unannex-files), that is, to remove a file from the GIN tracking before uploading:

  ```
  $ gin git annex unannex [path/filename]
  ```

- To stop tracking the GIN repo locally, delete the `.git` directory.

  :::{note}
  If in the GIN repo the files are locked, remember to unlock them before removing the `.git` directory! Otherwise we won't be able to delete the `.git/annex` content.
  :::

- To delete a GIN repository but keep the git repo:

  1. Delete the repository in the GIN server via the browser.
  2. Delete the GIN local repository with `git annex uninit`.
    - This command removes relevant bits in `.git/annex` and `.git/objects`, but some pre-commits may need to be edited by hand (see this [SO post](https://stackoverflow.com/questions/24447047/remove-git-annex-repository-from-file-tree)).

## Useful GIN resources

- [GIN CLI Usage tutorial](https://gin.g-node.org/G-Node/Info/wiki/GIN+CLI+Usage+Tutorial): includes a description of a basic workflow and examples of multi-user workflows.
- [GIN commands cheatsheet](https://gin.g-node.org/G-Node/Info/wiki/GIN+CLI+Help).
- [Troubleshooting](https://gin.g-node.org/G-Node/Info/wiki/FAQ%20Troubleshooting).
- [GIN CLI Recipes](https://gin.g-node.org/G-Node/Info/wiki/GIN+CLI+Recipes).

## References

- https://movement.neuroinformatics.dev/community/contributing.html#adding-new-data
- https://gin.g-node.org/G-Node/info/wiki#how-do-i-start
- https://gin-howto.readthedocs.io/en/latest/gin-repositories.html
- On GIN and its relation to `git-annex` (very high-level): https://gin.g-node.org/G-Node/Info/wiki/GIN+Advantages+Structure
