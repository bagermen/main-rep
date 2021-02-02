# Git submodules and git subtree
Submodules and subtree are used to resolve similar tasks but use different approach. Let's compare them.

1. [Git Submodules](#Git-Submodules)
    + [Add submodule](#Add-submodule)
    + [Pull the project with submodule](#Pull-the-project-with-submodule)
    + [Working with a project with submodule modification](#Working-with-a-project-with-submodule-modification)
2. [Git Subtree](#Git-Subtree)
3. [Conclusion](#Conclusion)
3. [Links](#Links)

## Git Submodules
The idea of submodules is to keep 2 repository next to each other. Main repository is bound to child repository by commit ID. That's why, any time you make change at child repository you will have changed state of parent repository.
It looks like nice idea as it keeps parent and children repository bound and, at the same time, parent repository doesn't manage child repository. But it brings problems as well. Many developers over internet have noticed that this adds some complexity to main repository management.
If we started to use 'git modules', we would have to write and follow some kind of agreement: update child repository first (including PR), then parent repository. (it doesn't looks like our case)

> Submodule is a better fit for component-based development, where your main project depends on a fixed version of another component (repo)

Please, scroll down to [Git Subtrees](#Git-Subtree), unless you are interested at git submodules.
### Add submodule
```
> git submodule add --name child git@github.com:bagermen/child-rep.git child
```
This command adds file **.gitmodules** and defines submodule named 'child' at child directoy.

Next, we need to push changes for main repository.
```
> git commit -am "add submodule"
> git push
```

### Pull the project with submodule

- Pull project initially (for new developers)
    ```
    > git clone --recurse-submodules git@github.com-bagermen:bagermen/main-rep.git main-rep2
    ```
    Notice ```--recurse-submodules``` flag
- Pull submodule at existing project (our case)
    ```
    > git pull
    > git submodule update --init
    ```
    "git submodule update" loads submodule at specified commit at main repository and leave submodule at **detached HEAD** state because main repository tracks submodules by commit id only.

### Working with a project with submodule modification
- Create a branch

    ```
    > git checkout -b DV-2
    > git submodule foreach 'git checkout -b DV-2'
    ```

    We create branch DV-2 for every submodule as well using 'foreach' statement

- Checkout a branch
    ```
    > git checkout DV-2
    ```
    Submodules are not updated at this state. We have 2 options:

    1. fetch changes and update modules to relevant commit id
        ```
        > git submodule update --remote
        ```

    2. manually change branch of submodules (but we need to know which branch it should be on)
        ```
        > cd child
        > git fetch
        > git checkout DV-2
        ```

- Do changes

    Create branches (we create similar named branches for each submodule)
    ```
    > git checkout -b 'DV-2'
    > git submodule foreach 'git checkout -b \"DV-2\"'
    ```

    To commit changes we can do separate commits at every project or do following thing
    ```
    > git submodule foreach 'git commit -am \"DV-2 add note style\"'
    > git commit -am 'DV-2 add note style'
    ```

- Push changes
    We can push all at once with following command
    ```
    > git push --recurse-submodules=on-demand --set-upstream origin DV-2
    ```

    Notice flag '--recurse-submodules=on-demand' which pushes submodules before main project

If you need to push a change to back-end, it's a good option to use ```git push --recurse-submodules=check``` command as it won't let you make a push until submodule is pushed.

## Git Subtree
On contrary to submodules, Git Subtree keep child repository as a folder inside main project and as part of main project. Child reposiory history is merged into main repository but it's possible to make pushes and pulls from child repository. This approach is convenient because it does not bring any compexity into main project and allows users to work as usual. I need to note that 'git subtrees' has lack of documentation and users should check of it availability at local git installation.

> subtree is more like a system-based development, where your all repo contains everything at once, and you can modify any part.

Subtrees usage.
- Add remote using standard git command
    ```
    > git remote add -f subtree-rep git@github.com-bagermen:bagermen/subtree-rep.git
    ````
    **subtree-rep** is the child repository name
- Add child project into folder
    ```
    > git subtree add --prefix subtree subtree-rep main [--squash]
    ```
    - **--prefix** is a folder name (mandatory for every subtree command)
    - **subtree-rep** is added repository above
    - **main** branch at child repository to take changes from
    - **--squash** is used if you want to remove history of child repository
- We can do **ANY** code manipulation. (Good practice is to split commits into child repository and main repository but it's not mandatory)
- Push changes to child repository
    ```
    > git subtree push --prefix subtree subtree-ref main
    ```
    Note. We can point out any branch at child repository even not exited one.
- Pull changes from child repository
    ```
    > git subtree pull --prefix=subtree subtree-rep main
    ```
    This can be used to update current child repository

## Conclusion
Since we are working with Topo project mainly and possible child repository is gonna relay on Topo App repository only it's better to avoid complexity and select easier solution which is the **git subtree**.
Look at documentation links for more information

## Links
- [Git Tools Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
- [Git subtree: the alternative to Git submodule](https://www.atlassian.com/git/tutorials/git-subtree)
- [Git Subtree: official documentation](https://github.com/git/git/blob/master/contrib/subtree/git-subtree.txt)
- [Differences between git submodule and subtree](https://stackoverflow.com/questions/31769820/differences-between-git-submodule-and-subtree)