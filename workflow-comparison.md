# Workflow Comparison

## Workflow options available with volume as source folder

1. [Entire source tree](https://code.visualstudio.com/remote/advancedcontainers/improve-performance#_use-a-named-volume-for-your-entire-source-tree)

   Combine both `workspaceMount` and `workspaceFolder` properties to override the default behavior of bind mount. 

   `.devcontainer` folder will not be copied into container volume.

   - pros: 
     - Clean volume free to use. No `.devcontainer` inside.
     - You can put repos as many as you wish into this single volume easily.

   - cons:
     - Manually `git clone` repo into container
     - You can not open a subfoleder which is not located in the volume. For example, if I have these two properties in my `devcontainer.json`
       ```json
       "workspaceMount": "source=source-volume,target=/workspace,type=volume",
       "workspaceFolder": "/workspace/prjectA"
       ```
       Remote - Containers extension will fail to open it at first time with an empty volume.
     - `.devcontainers` folder is not very helpful in sharing projects. It only contains info about devcontainer. In practice, we may need to set up a repo just for `.devcontainers` to share container info while other repos with project source code.

   - Idea:
     I really think this option would be a good solution to monorepo. For example we have such file structure in the mono repo.

     ```
     ├── .devcontainers
     │   ├── projectA
     │   │   ├── .devcontainers
     │   ├── projectB
     │   │   ├── .devcontainers
     ```
     
     If `.devcontainers` folders here could extend just like how docker compose files extend. We can put common configurations into the top `.devcontainers` folder, such as `"workspaceMount": "source=source-volume,target=/workspace,type=volume"`. Then we can put configurations specific to one project into sub `.devcontainers` folder such as `"workspaceFolder": "/workspace/prjectA"`.

     Next, if we could have selections of projects (including the monorepo as a selection) to pick in VS Code UI, I can start one container for development automatically and other containers for debugging if necessary without messing around all the different repos and containers. Meanwhile, all the projects share only one volume which means it is very easy to access assets from different projects.

     Also, hopefully we can automatically create non-existing folders in the volume before we open VS Code window - `/workspace/prjectA` in this case.

2. [Clone Repository in Named Container Volume](https://code.visualstudio.com/remote/advancedcontainers/improve-performance#_use-a-targeted-named-volume)

   Use straightforward command in VS Code to clone a repository into a named volume.

   - pros:
     - Automatically `git clone` repo into container
     - Can share one volume with other repos if you select the same volume when you run the command

   - cons:
     - Not able to customize a start point of docker image and select it every time you clone a repo without `.devcontainer` inside. I would like to have the ability to create a dev container definition and reuse it again and again for most the repos.
     - `.devcontainer` folder will be copied into the source tree. I understand this is a recommended method to advocate container sharing. But in some cases, `.devcontainer` is not necessary. For example, I want to make some contribution to an open source project. So I fork that repo and clone the fork into a container. Make some changes and push it for PR review. But the creator of original doesn't want to add dev container to his project. So he refuse my PR. I have to delete `.devcontainer` folder before I push.

   - Idea:
     It would be great to have my customized container definition in VS Code to reuse. Furthermore, an option to decide whether `.devcontainer` should be added to the source code would be appreciated very much.