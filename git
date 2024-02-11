#!/bin/bash

# examples:
# ./git -r /home/username/Projects/MyProject -m "commit message"
# ./git -r /home/username/Projects/MyProject -m "commit message" -n
# ./git -r /home/username/Projects/MyProject
# ./git -r Projects/MyProject
# ./git -r Projects/MyProject -n

# Define colors for better visual output
GREEN='\033[0;32m'
RED='\033[0;31m'
YELLOW='\033[0;33m'
LIGHT_BLUE='\033[1;34m'
NC='\033[0m' # No Color

# Function to display script usage information
show_help() {
    echo -e "Usage: ./$(basename "$0") [OPTIONS]"
    echo -e "Options:"
    echo -e "  -h: Display this help message"
    echo -e "  -r: Repository path (default: current directory)"
    echo -e "  -m: Commit message (default: random 8-character string)"
    echo -e "  -n: do not open firefox (default: opens firefox)"
    exit 0
}

# Default values
REPO_PATH=""
COMMIT_MESSAGE=$(openssl rand -hex 4)
OPENFIREFOX=true

# Parse options
while getopts "hr:m:n" opt; do
    case $opt in
        h)
            show_help
            ;;
        r)
            REPO_PATH="$OPTARG"
            ;;
        m)
            COMMIT_MESSAGE="$OPTARG"
            ;;
        n)
            OPENFIREFOX=false
            ;;
        \?)
            echo -e "${RED}Invalid option: -$OPTARG${NC}" >&2
            exit 1
            ;;
        :)
            echo -e "${RED}Option -$OPTARG requires an argument.${NC}" >&2
            exit 1
            ;;
    esac
done

#check if r arg is provided:
if [ -z "$REPO_PATH" ]; then
    echo -e "${RED}Error: Repository path is not provided${NC}"
    exit 1
fi

cd $REPO_PATH &> /dev/null
# catch cd error:
if [ $? -ne 0 ]; then
    echo -e "${RED}Error: Directory $REPO_PATH does not exist${NC}"
    exit 1
fi

# Check if the repository is a git repository
if [ ! -d ".git" ]; then
    echo -e "${RED}Error: Directory $REPO_PATH is not a git repository${NC}"
    exit 1
fi

# Add all files to the staging area
git add . && git status &> /dev/null

if git status | grep "nothing to commit, working tree clean" &> /dev/null; then
    echo -e "${YELLOW}No changes to commit!${NC}"
    exit 1
else
    echo -e "${LIGHT_BLUE}Changes to commit:${NC}"
    git status -s
    echo -e "${NC}"
fi

# wait for user to press enter to continue, also display message: "commit?"
echo "commit?"
echo "[press any key to commit, press ^C to cancel]"
read -p "---------------------------------------------" -n 1 -r

git commit -m "$COMMIT_MESSAGE" &> /dev/null && git push && echo -e "${GREEN}Commited with message $COMMIT_MESSAGE${NC}"

# open commit in firefox
if [ "$OPENFIREFOX" = false ]; then
    exit 0
fi
GIT_URL=$(git remote -v | grep fetch | awk '{print $2}' | head -n 1)
#echo -e "${GREEN}Git URL: $GIT_URL${NC}"
GIT_URL=${GIT_URL::-4}
firefox $GIT_URL/commits/main &> /dev/null

exit 0
