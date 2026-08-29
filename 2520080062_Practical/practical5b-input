#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    int pipefd[2];

    if (pipe(pipefd) == -1) {
        perror("pipe");
        exit(EXIT_FAILURE);
    }

    // First child: executes ls -l
    pid_t pid1 = fork();

    if (pid1 == -1) {
        perror("fork");
        exit(EXIT_FAILURE);
    }

    if (pid1 == 0) {
        // Child 1

        // Close unused read end
        close(pipefd[0]);

        // Redirect stdout to pipe
        dup2(pipefd[1], STDOUT_FILENO);

        // Close original pipe descriptor
        close(pipefd[1]);

        // Execute ls -l
        execlp("ls", "ls", "-l", (char *)NULL);

        // Only reached if exec fails
        perror("execlp ls");
        exit(EXIT_FAILURE);
    }

    // Second child: executes grep ".c"
    pid_t pid2 = fork();

    if (pid2 == -1) {
        perror("fork");
        exit(EXIT_FAILURE);
    }

    if (pid2 == 0) {
        // Child 2

        // Close unused write end
        close(pipefd[1]);

        // Redirect stdin to pipe
        dup2(pipefd[0], STDIN_FILENO);

        // Close original pipe descriptor
        close(pipefd[0]);

        // Execute grep ".c"
        execlp("grep", "grep", ".c", (char *)NULL);

        // Only reached if exec fails
        perror("execlp grep");
        exit(EXIT_FAILURE);
    }

    // Parent doesn't use the pipe
    close(pipefd[0]);
    close(pipefd[1]);

    // Wait for both children
    waitpid(pid1, NULL, 0);
    waitpid(pid2, NULL, 0);

    return 0;
}
