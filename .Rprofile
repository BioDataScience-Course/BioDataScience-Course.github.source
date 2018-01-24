# Install a git hook to push the coupled site repo at the same time as this one
# (use publishDir key in config.toml)
# Copyright (c) 2018, Philippe Grosjean (phgrosjean@sciviews.org), license MIT
install_git_hook <- function(repo_dir = ".", warn.exists = FALSE) {
  git_dir <- file.path(repo_dir, ".git")
  if (!file.exists(git_dir) || !file.info(git_dir)$isdir)
    stop("'repo_dir' does not seem to be a git repository (subdir .git not found)")
  hook_path <- file.path(git_dir, "hooks", "pre-push")
  if (file.exists(hook_path)) { # Do not overwrite it!
    if (isTRUE(warn.exists))
      warning("Hook .git/hooks/pre-push already exist and will not be overwritten")
      return(FALSE)
  }
  # Install the hook (different versions for Windows or Unix-like)
  if (.Platform$OS.type == "unix") {
    cat('#!/bin/sh
trim() {
  local var="$*"
  # remove leading whitespace characters and possible quote
  var="${var#"${var%%[![:space:]\\\"]*}"}"
  # remove possible quote and trailing whitespace characters
  var="${var%"${var##*[!\\\"[:space:]]}"}"
  echo "$var"
}

read_web_repo_dir() {
  blogdownPublishDir=""
  local blogdownConfigFile="config.toml"
  while IFS="=" read -r key value; do
    case $(trim "$key") in
      "publishDir") blogdownPublishDir=$(trim "$value") ;;
    esac
  done < "$blogdownConfigFile"
}
read_web_repo_dir

if [ "$blogdownPublishDir" = "" ]
then
  exit 0
else
  echo "Automatic commit and push for $blogdownPublishDir"
  cd "$blogdownPublishDir" &&
  git add --all &&
  git commit -m "automatic web site update" &&
  git push origin master
  exit 0
fi
', file = hook_path)
    # Also make sure this file is executable
    Sys.chmod(hook_path, "733")
  } else {
    if (isTRUE(warn.exists)) stop("Hook not available yet for Windows")
  }
  # Return invisibly if the file exists
  invisible(file.exists(hook_path))
}
install_git_hook()
rm(install_git_hook)
