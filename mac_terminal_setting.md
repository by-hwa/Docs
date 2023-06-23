# branch display
function parse_git_branch() {
    git branch 2> /dev/null | sed -n -e 's/^\* \(.*\)/[\1]/p'
}

COLOR_DEF=$'%f'
COLOR_USR=$'%F{250}'
COLOR_DIR=$'%F{250}'
COLOR_GIT=$'%F{39}'
setopt PROMPT_SUBST
export PROMPT='${COLOR_USR}%n@%m ${COLOR_DIR}%1~${COLOR_GIT}$(parse_git_branch)${COLOR_DEF} ${NEWLINE}%% '
