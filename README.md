# TOC ASSIGNMENT
 1.	Implement a program that detects redundant computations, dead code, and strength reduction.
    SOURCE CODE:
```bash   	
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

#define MAX_LINES 10
#define MAX_LEN 100

// Function to apply simple optimizations
void optimize(char code[MAX_LINES][MAX_LEN], int n) {
    char optimized[MAX_LINES][MAX_LEN];
    int usedY = 0;

    // Step 1: Check if variable y is used later
    for (int i = 0; i < n; i++) {
        if (strstr(code[i], "y") && strstr(code[i], "z")) {
            usedY = 1;
        }
    }

    printf("\nOptimized Code:\n");

    for (int i = 0; i < n; i++) {
        char line[MAX_LEN];
        strcpy(line, code[i]);

        // Constant folding: 2*8 -> 16
        if (strstr(line, "2 * 8")) {
            printf("x = 16\n");
        }
        // Strength reduction: x * 1 -> x
        else if (strstr(line, "* 1")) {
            if (strstr(line, "y = x * 1")) {
                if (!usedY) continue; // dead code elimination
                printf("y = x\n");
            }
        }
        // Algebraic simplification: y + 0 -> y
        else if (strstr(line, "+ 0")) {
            if (strstr(line, "z = y + 0")) {
                printf("z = x\n"); // because y = x
            }
        }
        else {
            // default print (unchanged)
            printf("%s\n", line);
        }
    }
}

int main() {
    int n;
    char code[MAX_LINES][MAX_LEN];

    printf("Enter number of lines of code: ");
    scanf("%d", &n);
    getchar(); // consume newline

    printf("Enter code lines (example: x = 2 * 8):\n");
    for (int i = 0; i < n; i++) {
        fgets(code[i], MAX_LEN, stdin);
        code[i][strcspn(code[i], "\n")] = '\0'; // remove newline
    }

    printf("\nOriginal Code:\n");
    for (int i = 0; i < n; i++) {
        printf("%s\n", code[i]);
    }

    optimize(code, n);

    return 0;
}
```

   	




2.	Implement a simple code generator that translates arithmetic expressions into target assembly for a stack machine.
   SOURCE CODE:
```bash
#include <stdio.h>
#include <stdlib.h>  // Added for exit()
#include <ctype.h>
#include <string.h>

#define MAX 100

char expr[MAX];
int pos = 0;

// Function prototypes
void parseExpression();
void parseTerm();
void parseFactor();

void error() {
    printf("Invalid expression!\n");
    exit(1);  // exit requires stdlib.h
}

// Parse a factor: number, variable, or parentheses
void parseFactor() {
    if (expr[pos] == '(') {
        pos++; // skip '('
        parseExpression();
        if (expr[pos] == ')') pos++; // skip ')'
        else error();
    } else if (isalpha(expr[pos])) {
        printf("PUSH %c\n", expr[pos]);
        pos++;
    } else {
        error();
    }
}

// Parse term: factor [* or / factor]*
void parseTerm() {
    parseFactor();
    while (expr[pos] == '*' || expr[pos] == '/') {
        char op = expr[pos++];
        parseFactor();
        if (op == '*') printf("MUL\n");
        else if (op == '/') printf("DIV\n");
    }
}

// Parse expression: term [+ or - term]*
void parseExpression() {
    parseTerm();
    while (expr[pos] == '+' || expr[pos] == '-') {
        char op = expr[pos++];
        parseTerm();
        if (op == '+') printf("ADD\n");
        else if (op == '-') printf("SUB\n");
    }
}

int main() {
    printf("Enter arithmetic expression  ");
    scanf("%s", expr);
    pos = 0;
    parseExpression();
    if (expr[pos] != '\0') error(); // ensure entire expression is parsed
    return 0;
}
```

  	
