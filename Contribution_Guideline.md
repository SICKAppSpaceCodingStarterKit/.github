## Contribution guideline

### What do I need to know to help?

We are happy if you are interested in supporting the further development of the SICK AppSpace Coding Starter Kit by e.g. discussing improvements, fixing bugs, contributing improvements for existing modules or even providing new modules. 
If you don't feel ready to make a code contribution yet, no problem!

If you are interested in making a code contribution and would like to learn more about the CSK, check out the [CSK documentation](https://github.com/SICKAppSpaceCodingStarterKit/.github/blob/main/docu/SICKAppSpaceCodingStarterKit_Documentation.md).

### How do I make a contribution?

You are wondering how to contribute to this project? Here's a quick rundown:

1. Find an issue that you are interested in addressing or a feature that you would like to add.  
(**Info**: If you want to add a brand new feature which does not exist so far, please make use of the [discussions](https://github.com/SICKAppSpaceCodingStarterKit/.github/discussions/new?category=ideas) and let us know about the new feature idea, so that we can create a new repo for this, which will include the basic app content for a CSK module based on the 'blank' module templates (see [creationTemplate](https://github.com/SICKAppSpaceCodingStarterKit/csk_module_creationtemplate) or [creationMultiTemplate](https://github.com/SICKAppSpaceCodingStarterKit/csk_module_creationmultitemplate))
2. Fork the repository associated with the issue to your local GitHub organization. This means that you will have a copy of the repository under your-GitHub-username/repository-name.
3. Clone the repository to your local machine using
    ```
    git clone https://github.com/<your-GitHub-username>/<repository-name>.git
    ```
4. Create a new branch for your fix using
    ```
    git checkout -b <branch-name-here>
    ```
5. Make the appropriate changes for the issue you are trying to address or the feature that you want to add.
6. Consider the following aspects while developing:
    - [ ] Module is coded and structured according to the "Developing guideline for modules" described within the [CSK documentation](https://github.com/SICKAppSpaceCodingStarterKit/.github/blob/main/docu/SICKAppSpaceCodingStarterKit_Documentation.md).
    - [ ] All functions/events/parameters are documented within the manifest documentation
    - [ ] The manifest description of the main CROWN includes the central information about the purpose of the module and how to use it in general
    - [ ] API docu based on the manifest was created and stored within the "docu"-folder of the repository
    - [ ] Internal LUA code documentation exists for variables and non served functions
    - [ ] All relevant infos are logged via the SharedLogger 'ModuleLogger'
    - [ ] Module supports persistent data feature based on 'CSK_Module_PersistentData'
    - [ ] Module supports user management based on 'CSK_Module_UserManagement'
    - [ ] No open "ToDos" within the code or at least clearly explained comments why they exist...
    - [ ] "Version" key in app manifest was updated following semantic versioning (use '0.x.y' for test / experimental modules which are not yet ready to be officially released)
    - [ ] Meaningful IDs used for UI elements
    - [ ] Module was tested on a SICK AppSpace device (at least on SICK AppEngine) with no error message
    - [ ] README.md is up to date (incl. info of device + firmware the module was tested with)
    - [ ] CHANGELOG.md is up to date
7. Use
    ```
    git add <insert-paths-of-changed-files-here>
    ```
    to add the file contents of the changed files to the "snapshot" git uses to manage the state of the project, also known as the index.
8. Use
    ```
    git commit -m "<Insert a short message of the changes made here>"
    ```
    to store the contents of the index with a descriptive message.
9. Push the changes to the remote repository using
    ```
    git push origin <branch-name-here>
    ```
10. Submit a pull request to the upstream repository.
11. Title the pull request with a short description of the changes made and the issue or bug number associated with your change. For example, you can title an issue "Added more log outputting to resolve #42".
12. In the description of the pull request, explain the changes that you made, any issues you think exist with the pull request you made, and any questions you have for the maintainer.

    Mostly it makes sense to state:
    - Release version x.x.x
    - New features
      - ...
    - Improvements
      - ...
    - Bugfixes
      - ...
    - Known issues
      - ...

    Additionally please check if you have already covered the aspects mentioned in step 6. The list of these aspects will be provided automatically in the pull request to check the status of the module.  
    It's OK if your pull request is not perfect (no pull request is), the reviewer will be able to help you fix any problems and improve it!
13. Wait for the pull request to be reviewed by a maintainer.
14. Make changes to the pull request if the reviewing maintainer recommends them.
15. Celebrate your success after your pull request is merged!
16. According to the used "Version" key in the app manifest we will give it a new Git Release / Tag to be used in other projects.

### How to use CSK modules in other Git projects?

Currently our recommendation is to use CSK modules within your projects via Git subtrees.  
By doing this, it is possible to use specific versions of the modules within your project.  
Additionally you can directly download the complete project (incl. the used modules) as a ZIP file from GitHub to implement it easily via Drag&Drop in SICK AppStudio.

This can be done via:

1. Adding the subtree as a remote to be able to refer to it:  
    ```
    git remote add -f <remoteName> URL  
    (e.g.: git remote add -f PersistentDataModule https://github.com/SICKAppSpaceCodingStarterKit/CSK_Module_PersistentData )
    ```
2. Now add the subtree by referring to the remote:  
    ```
    git subtree add --prefix <folderName> <remoteName> <release/tag> --squash  
    (e.g.: git subtree add --prefix CSK_Module_PersistentData PersistentDataModule v3.0.0 --squash )
    ```
    
If you want to update the module included in your project to a newer version of it, you can do it like this:

1. First delete the module subtree:  
    ```
    git rm -r <subtree>
    (e.g.: git rm -r CSK_Module_PersistentData )
    ```
2. Commit it:  
    ```
    git commit
    ```
3. Add the subtree again as a remote and add subtree as mentioned above but now with updated 'release/tag'...

***WARNING***  
Please state within the README.md of your repository which folders were added as subtrees to avoid that there will be further development within this copied modules, as this would create a parallel development beside the original repository.  
If you want to update / improve a used module for your project, please make use of the original repository of this module and only update the subtree to this module within your project as mentioned above.

### Attribution

This contribution guideline is adapted from the https://opensource.com/life/16/3/contributor-guidelines-template-and-tips licensed under a Creative Commons Attribution-Share Alike 4.0 International License (see https://creativecommons.org/licenses/by-sa/4.0/ )
