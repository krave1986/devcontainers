# Workflow Comparison

## Workflow options available with volume as source folder

1. [Entire source tree](https://code.visualstudio.com/remote/advancedcontainers/improve-performance#_use-a-named-volume-for-your-entire-source-tree)

   Combine both `workspaceMount` and `workspaceFolder` properties to override the default behavior of bind mount. 

   `.devcontainer` folder will not be copied into container volume.

   - pros: 
     - Clean volume free to use. No `.devcontainer` inside.
     - You can put repos as many as you widh into this single volume easily.

   - cons:
     - You can not open a subfoleder which is not located in the volume. For example, if I have these two properties in my `devcontainer.json`
       ```json
       "workspaceMount": "source=source-volume,target=/workspace,type=volume",
       "workspaceFolder": "/workspace/prjectA"
       ```
       Remote - Containers extension will fail to open it at first time with an empty volume.
     - `.devcontainers` floder is not very helpful in shring projects. It only contains info about devcontainer. In practice, we may need to set up a repo just for `.devcontainers` to share container info while other repos with project source code.

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

     Next, if we could have selections of projects (including the monorepo as a selection) to pick in VS Code UI, I can start one container for development and others for debugging if necessary without messing around all the different repos and containers. Meanwhile, all the projects share only one volume which means it is very easy to acess assets from different projects.

     Also, hopefully we can automatically create non-existing folders in the volume before we open VS Code window - `/workspace/prjectA` in this case.

     