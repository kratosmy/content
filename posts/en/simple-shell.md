---
title: "Implementing a Simple Shell"
title-en: "Implementing a Simple Shell"
date: "2022-09-12"
tags: ["c"]
summary: " "
draft: false
author: kratos
---

# Overview

A simple Shell implementation based on Stephen Brennan's Tutorial.

## The Life Cycle of a Shell

A Shell's life cycle consists of three basic parts:

- Initialize: The Shell reads and executes configuration files;
- Interpret: The Shell reads standard input (interactive input or files) and executes;
- Terminate: After command execution is complete, the Shell executes termination commands, frees memory, and terminates;

Therefore, we can get the basic structure of the main function:

```c
int main() {
  ls_loop();
  return EXIT_SUCCESS;
}
```

## Basic Loop in Shell

The loop has three steps:

- Read: Read commands from standard input;
- Parse: Split the input command string into program and arguments;
- Execute: Run the parsed command.

This gives us the basic logic within the loop body:

```c
void lsh_loop(void)
{
  char *line;
  char **args;
  int status;

  do {
    printf("> ");
    line = lsh_read_line();
    args = lsh_split_line(line);
    status = lsh_execute(args);

    free(line);
    free(args);
  } while (status);
}
```

## Built-in Commands

Why do we need built-in commands? Take the `cd` command as an example - its purpose is to change directories. If the Shell passes this command to a child process for execution, it can only change the child process's directory, which effectively does nothing for the user. This is obviously not what we want. So we need to implement such commands as built-in commands.

## Complete Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>

#define LSH_RL_BUF_SIZE 1024
char *lsh_read_line_old(void) {
  int buf_size = LSH_RL_BUF_SIZE;
  int position = 0;
  char *buffer = malloc(sizeof(char) * buf_size);
  int c;

  if (!buffer) {
    fprintf(stderr, "lsh: allocation error\n");
    exit(EXIT_FAILURE);
  }

  while (1) {
    // Read a character
    c = getchar();

    // if we hit EOF, replace it with a null character and return
    if (c == EOF || c == '\n') {
        buffer[position] = '\0';
        return buffer;
    } else {
        buffer[position] = c;
    }
    position++;

    // if we have exceeded the buffer, reallocate
    if (position >= buf_size) {
        buf_size += LSH_RL_BUF_SIZE;
        buffer = realloc(buffer, buf_size);
        if (!buffer) {
            fprintf(stderr, "lsh: allocation error\n");
            exit(EXIT_FAILURE);
        }
    }
  }
}

char *lsh_read_line_new(void) {
    char* line = NULL;
    ssize_t buf_size = 0; // have getline allocate a buffer for us

    if (getline(&line, &buf_size, stdin) == -1) {
        if (feof(stdin)) {
            exit(EXIT_SUCCESS); // we received an EOF
        } else {
            perror("readline");
            exit(EXIT_FAILURE);
        }
    }
    return line;
}

#define LSH_TOK_BUF_SIZE 64
#define LSH_TOK_DELIM " \t\r\n\a"
char **lsh_split_line(char *line) {
    int buf_size = LSH_TOK_BUF_SIZE, position = 0;
    char **tokens = malloc(buf_size * sizeof(char*));
    char *token;

    if (!tokens) {
        fprintf(stderr, "lsh: allocation error\n");
        exit(EXIT_FAILURE);
    }

    token = strtok(line, LSH_TOK_DELIM);
    while (token != NULL) {
        tokens[position] = token;
        position++;

        if (position >= buf_size) {
            buf_size += LSH_RL_BUF_SIZE;
            tokens = realloc(tokens, buf_size * sizeof(char*));
            if (!tokens) {
                fprintf(stderr, "lsh: allocation error\n");
                exit(EXIT_FAILURE);
            }
        }

        token = strtok(NULL, LSH_TOK_DELIM);
    }
    tokens[position] = NULL;
    return tokens;
}

int lsh_launch(char** args) {
    pid_t pid, wpid;
    int status;

    pid = fork();
    if (pid == 0) {
        // Child process
        if (execvp(args[0], args) == -1) {
            perror("lsh child process execute error!");
        }
        exit(EXIT_FAILURE);
    } else if (pid < 0) {
        // Error in forking
        perror("lsh fork error!");
    } else {
        // Parent process
        do {
            wpid = waitpid(pid, &status, WUNTRACED);
        } while (!WIFEXITED(status) && !WIFSIGNALED(status));
    }
    return 1;
}

/*
 * Function declaration for builtin shell commands:
 */
int lsh_cd(char **args);
int lsh_help(char **args);
int lsh_exit(char **args);

/*
 * List of builtin commands, followed by their corresponding functions.
 */
char* builtin_str[] = {
        "cd",
        "help",
        "exit"
};

int (*builtin_func[]) (char**) = {
        &lsh_cd,
        &lsh_help,
        &lsh_exit
};

int lsh_num_builtins() {
    return sizeof(builtin_str) / sizeof(char*);
}

/*
 * Builtin function implementation
 */
int lsh_cd(char** args) {
    if (args[1] == NULL) {
        fprintf(stderr, "lsh: expected argument to \"cd\"\n");
    } else {
        if (chdir(args[1]) != 0) {
            perror("lsh change directory error!");
        }
    }
    return 1;
}

int lsh_help(char** args) {
    int i;
    printf("LSH from scratch\n");
    printf("Type program names and arguments, and hit enter.\n");
    printf("The following are built in:\n");

    for (i = 0; i < lsh_num_builtins(); i++) {
        printf(" %s\n", builtin_str[i]);
    }

    printf("Use the man command for information and other programs.\n");
    return 1;
}

int lsh_exit(char** args) {
    return 0;
}

int lsh_execute(char **args) {
    int i;
    if (args[0] == NULL) {
        // An empty command was entered.
        return 1;
    }
    // Find the matched builtin command and execute it
    for (i = 0; i < lsh_num_builtins(); i++) {
        if (strcmp(args[0], builtin_str[i]) == 0) {
            return (*builtin_func[i])(args);
        }
    }
    return lsh_launch(args);
}

void ls_loop(void) {
    char *line;
    char **args;
    int status;

    do {
        printf("> ");
        line = lsh_read_line_new();
        args = lsh_split_line(line);
        status = lsh_execute(args);
    } while (status);
}

int main() {
  ls_loop();
  return EXIT_SUCCESS;
}
```