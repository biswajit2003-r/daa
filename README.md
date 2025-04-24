#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

void printSolution(int *board, int n) {
    printf("One of the solutions:\n");
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (board[i] == j)
                printf("Q\t");
            else
                printf(".\t");
        }
        printf("\n");
    }
    printf("\n");
}

bool isSafe(int *board, int row, int col) {
    for (int i = 0; i < row; i++) {
        if (board[i] == col || board[i] - i == col - row || board[i] + i == col + row)
            return false;
    }
    return true;
}

void solveNQueen(int *board, int row, int n, bool *foundSolution) {
    if (row == n) {
        printSolution(board, n);
        *foundSolution = true;
        return;
    }

    for (int col = 0; col < n; col++) {
        if (isSafe(board, row, col)) {
            board[row] = col;
            solveNQueen(board, row + 1, n, foundSolution);
        }
    }
}

int main() {
    int n;
    printf("Enter the value of N: ");
    scanf("%d", &n);

    int *board = (int *)malloc(n * sizeof(int));
    if (board == NULL) {
        printf("Memory allocation failed.\n");
        return 1;
    }

    bool foundSolution = false;
    solveNQueen(board, 0, n, &foundSolution);

    if (!foundSolution) 
        printf("No solution exists for N = %d\n", n);

    free(board);
    return 0;
}
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------
#include <stdio.h>
#include <limits.h>

void printParenthesis(int i, int j, int bracket[][20]) {
    if (i == j) {
        printf("A%d", i);
        return;
    }
    
    printf("(");
    
    int k = bracket[i][j];  
    printParenthesis(i, k, bracket);
    printParenthesis(k + 1, j, bracket);
    
    printf(")");
}

void printTable(const char *title, int table[][20], int n, int showDiagonal) {
    printf("\n==== %s ====\n\n", title);

    printf("\t");
    for (int j = 1; j < n; j++)
        printf("%d\t", j);
    printf("\n");

    for (int i = 1; i < n; i++) {
        printf("%d\t", i);
        for (int j = 1; j < n; j++) {
            if (i > j)
                printf("-\t");
            else if (!showDiagonal && i == j)
                printf("-\t");
            else
                printf("%d\t", table[i][j]);
        }
        printf("\n");
    }
}

void matrixChainOrder(int p[], int n) {
    int m[20][20] = {0}; 
    int bracket[20][20] = {0}; 

    for (int L = 2; L < n; L++) {
        for (int i = 1; i < n - L + 1; i++) {
            int j = i + L - 1;
            m[i][j] = INT_MAX;
            for (int k = i; k < j; k++) {
                int q = m[i][k] + m[k + 1][j] + p[i - 1] * p[k] * p[j];
                if (q < m[i][j]) {
                    m[i][j] = q;
                    bracket[i][j] = k;  
                }
            }
        }
    }

    printTable("Dynamic Programming Table (m)", m, n, 1);

    printTable("K Table (bracket)", bracket, n, 0);

    printf("\n Minimum number of scalar multiplications: %d\n", m[1][n - 1]);
    printf("Optimal Parenthesization: ");
    printParenthesis(1, n - 1, bracket);
    printf("\n");
}

int main() {
    int n;
    printf("Enter the number of matrices: ");
    scanf("%d", &n);
    
    int p[n + 1]; 
    int row, col;

    printf("Enter the dimensions of matrices (row column format):\n");
    for (int i = 0; i < n; i++) {
        printf("Matrix %d (rows cols): ", i + 1);
        scanf("%d %d", &row, &col);

        if (i == 0) {
            p[i] = row;
        }

        if (p[i] != row) {
            printf("Error: Column of Matrix %d must match row of Matrix %d!\n", i, i + 1);
            return 1;
        }

        p[i + 1] = col;
    }

    matrixChainOrder(p, n + 1);
    return 0;
}
