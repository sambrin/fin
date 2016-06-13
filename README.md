#About fin:
- It’s 38 lines of bash, no dependencies
- Tasks are all represented by their own text files, with filenames being task names and file contents being any metadata or content you want to associate with the task.
- Tasks are created in a folder called outstanding\_tasks. When you complete a task, its file is moved to another folder called completed_tasks.
- Cloud syncing is trivial via dropbox or google drive.
- Shell tab completion enables quick referencing of existing tasks.

Installation:

- Create folders: ~/TODO/outstanding\_tasks, ~/TODO/completed\_tasks
- For Google Drive syncing: ln -s ~/Google\ Drive/TODO ~/TODO
- For Dropbox syncing: ln -s ~/TODO ~/Dropbox/TODO
- Paste this shell script into your ~/.profile :

```BOLD_AND_UNDERLINED="\e[1;4m"
GREEN="\e[32m"
STANDARD="\e[0m"
TODO_LIST_LABEL="\n -----------------------TODO-------------------------\n"
TODO_LIST_END=" ----------------------------------------------------\n\n"
TASKS_DIR=~/TODO/outstanding_tasks/
COMPLETED_DIR=~/TODO/completed_tasks/

function todo {
 if [ -z "$1" ]
 then
   printf "${TODO_LIST_LABEL}"
   find ${TASKS_DIR} -not -path '*/\.*' -type f -execdir echo '{}' ';' | nl -s '[] ' -b n
   printf "${TODO_LIST_END}"
 else
   touch ${TASKS_DIR}"${*}"
   todo
 fi
}


_fin () {
   IFS=$'\n' tmp=( $(compgen -W "$(ls "${TASKS_DIR}")" -- "${COMP_WORDS[$COMP_CWORD]}" ))
   COMPREPLY=( "${tmp[@]// /\ }" )
   IFS=$' '
}


function fin {
 if ! [ -z "$1" ] && [ -a ${TASKS_DIR}"${*}" ]
 then
   mv ${TASKS_DIR}"$1" ${COMPLETED_DIR}
   todo
   printf " ${GREEN}DONE ${STANDARD}with ${BOLD_AND_UNDERLINED}$1${STANDARD}\n\n"
 else
   echo -e "\nusage: fin [existing task to complete]\n"
 fi
}

complete -o default -F _fin fin
```

#Example Usage:

```
dope:~ todo
-----------------------TODO-------------------------
  [] show fin to the team
----------------------------------------------------

dope:~ todo gather feedback on fin

-----------------------TODO-------------------------
[] gather feedback on fin
[] show fin to the team
----------------------------------------------------

dope:~ fin show\ fin\ to\ the\ team

-----------------------TODO-------------------------
[] gather feedback on fin
DONE with show fin to the team
----------------------------------------------------

dope:~
```
