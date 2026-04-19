#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct Account {
    int accNo;
    char name[50];
    float balance;
};

// Function declarations
void createAccount();
void deposit();
void withdraw();
void checkBalance();

int main() {
    int choice;

    do {
        printf("\n===== BANK MANAGEMENT SYSTEM =====\n");
        printf("1. Create Account\n");
        printf("2. Deposit Money\n");
        printf("3. Withdraw Money\n");
        printf("4. Check Balance\n");
        printf("5. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch(choice) {
            case 1: createAccount(); break;
            case 2: deposit(); break;
            case 3: withdraw(); break;
            case 4: checkBalance(); break;
            case 5: printf("Exiting...\n"); break;
            default: printf("Invalid choice!\n");
        }

    } while(choice != 5);

    return 0;
}

// Create Account
void createAccount() {
    struct Account acc;
    FILE *fp = fopen("accounts.dat", "ab");

    if(fp == NULL) {
        printf("Error opening file!\n");
        return;
    }

    printf("Enter Account Number: ");
    scanf("%d", &acc.accNo);

    printf("Enter Name: ");
    scanf(" %[^\n]", acc.name);

    printf("Enter Initial Balance: ");
    scanf("%f", &acc.balance);

    fwrite(&acc, sizeof(acc), 1, fp);
    fclose(fp);

    printf("Account Created Successfully!\n");
}

// Deposit Money
void deposit() {
    FILE *fp = fopen("accounts.dat", "rb+");
    struct Account acc;
    int accNo, found = 0;
    float amount;

    if(fp == NULL) {
        printf("File not found!\n");
        return;
    }

    printf("Enter Account Number: ");
    scanf("%d", &accNo);

    printf("Enter amount to deposit: ");
    scanf("%f", &amount);

    while(fread(&acc, sizeof(acc), 1, fp)) {
        if(acc.accNo == accNo) {
            acc.balance += amount;

            fseek(fp, -sizeof(acc), SEEK_CUR);
            fwrite(&acc, sizeof(acc), 1, fp);

            printf("Amount Deposited! New Balance: %.2f\n", acc.balance);
            found = 1;
            break;
        }
    }

    if(!found)
        printf("Account not found!\n");

    fclose(fp);
}

// Withdraw Money
void withdraw() {
    FILE *fp = fopen("accounts.dat", "rb+");
    struct Account acc;
    int accNo, found = 0;
    float amount;

    if(fp == NULL) {
        printf("File not found!\n");
        return;
    }

    printf("Enter Account Number: ");
    scanf("%d", &accNo);

    printf("Enter amount to withdraw: ");
    scanf("%f", &amount);

    while(fread(&acc, sizeof(acc), 1, fp)) {
        if(acc.accNo == accNo) {
            if(acc.balance >= amount) {
                acc.balance -= amount;

                fseek(fp, -sizeof(acc), SEEK_CUR);
                fwrite(&acc, sizeof(acc), 1, fp);

                printf("Withdrawal Successful! Remaining Balance: %.2f\n", acc.balance);
            } else {
                printf("Insufficient Balance!\n");
            }
            found = 1;
            break;
        }
    }

    if(!found)
        printf("Account not found!\n");

    fclose(fp);
}

// Check Balance
void checkBalance() {
    FILE *fp = fopen("accounts.dat", "rb");
    struct Account acc;
    int accNo, found = 0;

    if(fp == NULL) {
        printf("File not found!\n");
        return;
    }

    printf("Enter Account Number: ");
    scanf("%d", &accNo);

    while(fread(&acc, sizeof(acc), 1, fp)) {
        if(acc.accNo == accNo) {
            printf("\n--- Account Details ---\n");
            printf("Account No: %d\n", acc.accNo);
            printf("Name: %s\n", acc.name);
            printf("Balance: %.2f\n", acc.balance);
            found = 1;
            break;
        }
    }

    if(!found)
        printf("Account not found!\n");

    fclose(fp);
}
