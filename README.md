KiCAD Notes
===========

The following guide decribes how to setup a new project in KiCAD which will allow source version control in a git repository and on demand access to the latest Devtank libaries over a network connection. Master repostiories for both Devtank libraries and individual KiCAD design projects will be retained on the Devtank server PC and the following instructions allow easy syncronisation/backup with these folders using git whilst allowing local development on each user’s PC.

KiCad File formats described here:

<http://kicad-pcb.org/help/file-formats/>

How to use git sub modules

<https://github.com/freetronics/freetronics_kicad_library/wiki/Library-Management>

To start a new git managed Kicad project:
-----------------------------------------

The following steps provide a completely controlled source method for starting a new kicad project whilst allowing shared use and collaboration of the Devtank libraries.


Step 1
------

Create git controlled folder for new project and add common Kicad libraries.

**(go to the folder where you want to store your projects, eg. cd Projects)**


    mkdir my_new_project
    cd my_new_project
    git init
    
    git submodule add ssh.devtank.co.uk:/git/kicad_devtank_common common


Step 2
------

This step creates a file used by git to filter out unneccesary files being archived.


    echo "# export files for BOM
    *.tsv
    # backup files
    *.bak
    *.bck
    *.kicad_pcb-bak
    ~*" > .gitignore
    git add .gitignore\
    git commit -a -m "Setup common libraries for my Kicad project."


Step 3
--------

Create a new Kicad project from common template.

**(Copy generic project template files to your new project folder (named appropriately, CrazyCircuitProject for example). Your kicad project files will live in this folder but using this template as a starting point saves time and provides default setup to devtank libaries, standard design rules, devtank drawing templates etc...**


    cp -r common/NewProject_template CrazyProject
    
    git add CrazyProject
    git commit -a -m "Add Kicad project files."*


Step 4
--------

Add git remote repository for backup and sharing.

**(Ask Joe/IT for new git repo on the server. In this example it's "my_new_kicad_project")**

    git remote add origin ssh.devtank.co.uk:/git/my_new_kicad_project
    git push -u origin master # Push new git project to new empty git on server.


Cloning an existing project
---------------------------

To checkout an existing project afresh do:

    git clone ssh.devtank.co.uk:/git/my_new_kicad_project
    cd my_new_kicad_project
    git submodule init
    git submodule update

Updating an existing project
----------------------------

**(You may want to do this if for example another user has a project under development and they are pushing changes to the server. You will need to pull down the latest files using the folllowing commands.)**

    git pull
    git submodule update

Updating the common Devtank libaries used
-----------------------------------------

**(This should only be done if you want to update an existing project with the latest Devtank libaries or the project is still under development. If you just want to view an existing project then then this step is not required.)**


    git submodule sync
    # Open Kicad do anything required.
    git commit -a -m "Updated to latest common libraries for feature foobar."


Updating the master Project
---------------------------

To commit local changes to the project do:

**(Note: Your push maybe refused if you have made changes to a version older than in the remote git repository. If multiple people are working on a project, best pull often or work on separate branches. If after the fact, you must do a rebase or merge and resolve the differences.)**

**(This includes changing the version of the common libraries used as done above.)**

    git add * # Make sure any new files are added.
    git commit -a -m "My changes to my project."
    git push # Push changes to central repo.*



Updating master common Library
------------------------------

To commit local changes to the common libraries do:

**(The following steps push local changes to the master (server) git repository and should be done in the order below, e.g. common libraries first and then project files if you are baselining an entire project. If you just want to push library changes so that other users can see them then you only need to follow the steps for common library updates.)**

**(Note: Your push maybe refused if you have made changes to a version older than in the remote git repository. If multiple people are working on a project, best pull often or work on separate branches. If after the fact, you must do a rebase or merge and resolve the differences.)**

**(Note: the git push command pushes the local changes to the master server version so that others can access the changes and use them.)**

    cd common
    git add * # Make sure any new files are added.
    git commit -a -m "What my changes to the common libraries where."
    git push # Push changes to central repo.
