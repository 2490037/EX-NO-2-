## EX. NO:2 IMPLEMENTATION OF PLAYFAIR CIPHER

 

## AIM:
 

 

To write a C program to implement the Playfair Substitution technique.

## DESCRIPTION:

The Playfair cipher starts with creating a key table. The key table is a 5×5 grid of letters that will act as the key for encrypting your plaintext. Each of the 25 letters must be unique and one letter of the alphabet is omitted from the table (as there are 25 spots and 26 letters in the alphabet).

To encrypt a message, one would break the message into digrams (groups of 2 letters) such that, for example, "HelloWorld" becomes "HE LL OW OR LD", and map them out on the key table. The two letters of the diagram are considered as the opposite corners of a rectangle in the key table. Note the relative position of the corners of this rectangle. Then apply the following 4 rules, in order, to each pair of letters in the plaintext:
1.	If both letters are the same (or only one letter is left), add an "X" after the first letter
2.	If the letters appear on the same row of your table, replace them with the letters to their immediate right respectively
3.	If the letters appear on the same column of your table, replace them with the letters immediately below respectively
4.	If the letters are not on the same row or column, replace them with the letters on the same row respectively but at the other pair of corners of the rectangle defined by the original pair.
## EXAMPLE:

![image](https://github.com/Hemamanigandan/EX-NO-2-/assets/149653568/e6858d4f-b122-42ba-acdb-db18ec2e9675)

 

## ALGORITHM:

STEP-1: Read the plain text from the user.
STEP-2: Read the keyword from the user.
STEP-3: Arrange the keyword without duplicates in a 5*5 matrix in the row order and fill the remaining cells with missed out letters in alphabetical order. Note that ‘i’ and ‘j’ takes the same cell.
STEP-4: Group the plain text in pairs and match the corresponding corner letters by forming a rectangular grid.
STEP-5: Display the obtained cipher text.



## Program:
~~~
#include <stdio.h>
#include <string.h>
#include <ctype.h>


#define SIZE 5


void generateKeyMatrix(char key[], char keyMatrix[SIZE][SIZE]) {
    int dict[26] = {0};
    int i, j, k = 0;
    
   
    for (i = 0; key[i]; i++) {
        char ch = toupper(key[i]);
        if (ch == 'J') ch = 'I';
        if (!dict[ch - 'A']) {
            keyMatrix[k / SIZE][k % SIZE] = ch;
            dict[ch - 'A'] = 1;
            k++;
        }
    }

    
    for (i = 0; i < 26; i++) {
        if (i + 'A' == 'J') continue; 
        if (!dict[i]) {
            keyMatrix[k / SIZE][k % SIZE] = i + 'A';
            dict[i] = 1;
            k++;
        }
    }
}


void findPosition(char keyMatrix[SIZE][SIZE], char ch, int *row, int *col) {
    if (ch == 'J') ch = 'I';
    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {
            if (keyMatrix[i][j] == ch) {
                *row = i;
                *col = j;
                return;
            }
        }
    }
}

int prepareText(char input[], char output[]) {
    int len = strlen(input);
    int k = 0;
    for (int i = 0; i < len; i++) {
        char ch = toupper(input[i]);
        if (ch < 'A' || ch > 'Z') continue; 
        if (ch == 'J') ch = 'I';
        
        output[k++] = ch;
    }
    output[k] = '\0';

    
    char temp[200];
    int j = 0;
    for (int i = 0; i < k; i++) {
        temp[j++] = output[i];
        if (i + 1 < k && output[i] == output[i + 1]) {
            temp[j++] = 'X';
        }
    }
    if (j % 2 != 0) temp[j++] = 'X'; 
    temp[j] = '\0';

    strcpy(output, temp);
    return j;
}


void encrypt(char prepared[], char keyMatrix[SIZE][SIZE], char cipher[]) {
    int len = strlen(prepared);
    int k = 0;
    for (int i = 0; i < len; i += 2) {
        int r1, c1, r2, c2;
        findPosition(keyMatrix, prepared[i], &r1, &c1);
        findPosition(keyMatrix, prepared[i + 1], &r2, &c2);

        if (r1 == r2) {
            cipher[k++] = keyMatrix[r1][(c1 + 1) % SIZE];
            cipher[k++] = keyMatrix[r2][(c2 + 1) % SIZE];
        } else if (c1 == c2) {
            cipher[k++] = keyMatrix[(r1 + 1) % SIZE][c1];
            cipher[k++] = keyMatrix[(r2 + 1) % SIZE][c2];
        } else {
            cipher[k++] = keyMatrix[r1][c2];
            cipher[k++] = keyMatrix[r2][c1];
        }
    }
    cipher[k] = '\0';
}


void decrypt(char cipher[], char keyMatrix[SIZE][SIZE], char plain[]) {
    int len = strlen(cipher);
    int k = 0;
    for (int i = 0; i < len; i += 2) {
        int r1, c1, r2, c2;
        findPosition(keyMatrix, cipher[i], &r1, &c1);
        findPosition(keyMatrix, cipher[i + 1], &r2, &c2);

        if (r1 == r2) {
            plain[k++] = keyMatrix[r1][(c1 + SIZE - 1) % SIZE];
            plain[k++] = keyMatrix[r2][(c2 + SIZE - 1) % SIZE];
        } else if (c1 == c2) {
            plain[k++] = keyMatrix[(r1 + SIZE - 1) % SIZE][c1];
            plain[k++] = keyMatrix[(r2 + SIZE - 1) % SIZE][c2];
        } else {
            plain[k++] = keyMatrix[r1][c2];
            plain[k++] = keyMatrix[r2][c1];
        }
    }
    plain[k] = '\0';
}

int main() {
    char key[100], text[200], prepared[200], cipher[200], decrypted[200];
    char keyMatrix[SIZE][SIZE];

    printf("Enter the key: ");
    scanf("%s", key);

    generateKeyMatrix(key, keyMatrix);

    printf("Key Matrix:\n");
    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {
            printf("%c ", keyMatrix[i][j]);
        }
        printf("\n");
    }

    printf("Enter the text to encrypt: ");
    scanf("%s", text);

    int len = prepareText(text, prepared);
    encrypt(prepared, keyMatrix, cipher);
    printf("Encrypted Text: %s\n", cipher);

    decrypt(cipher, keyMatrix, decrypted);
    printf("Decrypted Text: %s\n", decrypted);

    return 0;
}

~~~
## Output:
<img width="1919" height="1149" alt="Screenshot 2025-09-13 090126" src="https://github.com/user-attachments/assets/00da16f0-0e73-4c2d-8642-0f67287146f5" />

## Result:

Program has been executed successfully
