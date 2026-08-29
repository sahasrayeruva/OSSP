#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>
#include <time.h>

#define COUNT 100000

int main() {
    int pipefd[2];
    pid_t pid;

    if (pipe(pipefd) == -1) {
        perror("pipe");
        exit(EXIT_FAILURE);
    }

    pid = fork();

    if (pid == -1) {
        perror("fork");
        exit(EXIT_FAILURE);
    }

    // Parent = Producer
    if (pid > 0) {
        close(pipefd[0]);  // Close read end

        clock_t start = clock();

        for (int i = 1; i <= COUNT; i++) {
            if (write(pipefd[1], &i, sizeof(i)) == -1) {
                perror("write");
                exit(EXIT_FAILURE);
            }
        }

        close(pipefd[1]);  // Signal end of data
        wait(NULL);

        clock_t end = clock();

        double time_taken =
            (double)(end - start) / CLOCKS_PER_SEC;

        printf("\nProducer finished.\n");
        printf("Data produced: %d\n", COUNT);
        printf("Communication time: %.6f seconds\n", time_taken);

        if (time_taken > 0) {
            printf("Communication rate: %.2f values/sec\n",
                   COUNT / time_taken);
        }
    }

    // Child = Consumer
    else {
        close(pipefd[1]);  // Close write end

        int value;
        int consumed = 0;

        while (read(pipefd[0], &value, sizeof(value)) > 0) {
            consumed++;
        }

        close(pipefd[0]);

        printf("Consumer received: %d values\n", consumed);

        exit(EXIT_SUCCESS);
    }

    return 0;
}
