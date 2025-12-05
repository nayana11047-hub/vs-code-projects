# vs-code-projects
This repository contains my VS Code projects.”  “C programs for college.”
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define STUDENT_FILE "students.txt"
#define CREDENTIAL_FILE "credential.txt"

// -------- STRUCT --------
struct Student {
    int roll;
    char name[50];
    float marks;
};

// -------- FUNCTION DECLARATIONS --------
int loginAdmin();
int loginStaff();
int loginUser();
int loginGuest();

void adminMenu();
void staffMenu();
void userMenu();
void guestMenu();

void addStudent();
void displayStudents();
void searchStudent();
void updateStudent();
void deleteStudent();


// -------- MAIN --------
int main() {
    int choice;

    while (1) {
        printf("\n===== LOGIN MENU =====\n");
        printf("1. Admin Login\n");
        printf("2. Staff Login\n");
        printf("3. User Login\n");
        printf("4. Guest Login\n");
        printf("5. Exit\n");
        printf("Enter choice: ");
        scanf("%d", &choice);

        switch(choice) {
            case 1: if (loginAdmin()) adminMenu(); else printf("Login Failed!\n"); break;
            case 2: if (loginStaff()) staffMenu(); else printf("Login Failed!\n"); break;
            case 3: if (loginUser()) userMenu(); else printf("Login Failed!\n"); break;
            case 4: if (loginGuest()) guestMenu(); else printf("Login Failed!\n"); break;
            case 5: printf("\nExiting Program...\n"); exit(0);
            default: printf("Invalid Option! Try Again.\n");
        }
    }
    return 0;
}


// -------- LOGIN FUNCTION (COMMON CODE REUSED) --------
int loginTemplate(char *roleName) {
    char username[20], password[20];
    char fileUser[20], filePass[20], fileRole[20];

    FILE *fp = fopen(CREDENTIAL_FILE, "r");
    if (!fp) {
        printf("Error: credential.txt not found!\n");
        return 0;
    }

    printf("\n===== %s LOGIN =====\n", roleName);
    printf("Username: ");
    scanf("%s", username);
    printf("Password: ");
    scanf("%s", password);

    while (fscanf(fp, "%s %s %s", fileUser, filePass, fileRole) == 3) {
        if (strcmp(username, fileUser)==0 &&
            strcmp(password, filePass)==0 &&
            strcmp(fileRole, roleName)==0) {
                fclose(fp);
                return 1;
        }
    }

    fclose(fp);
    return 0;
}

int loginAdmin() { return loginTemplate("ADMIN"); }
int loginStaff() { return loginTemplate("STAFF"); }
int loginUser()  { return loginTemplate("USER");  }
int loginGuest() { return loginTemplate("GUEST"); }


// -------- ADMIN MENU --------
void adminMenu() {
    int choice;

    while (1) {
        printf("\n===== ADMIN MENU =====\n");
        printf("1. Add Student\n");
        printf("2. Display Students\n");
        printf("3. Search Student\n");
        printf("4. Update Student\n");
        printf("5. Delete Student\n");
        printf("6. Logout\n");
        printf("Enter choice: ");
        scanf("%d", &choice);

        switch(choice) {
            case 1: addStudent(); break;
            case 2: displayStudents(); break;
            case 3: searchStudent(); break;
            case 4: updateStudent(); break;
            case 5: deleteStudent(); break;
            case 6: printf("Logging out...\n"); return;
            default: printf("Invalid option!\n");
        }
    }
}


// -------- STAFF MENU --------
void staffMenu() {
    int choice;

    while (1) {
        printf("\n===== STAFF MENU =====\n");
        printf("1. Display Students\n");
        printf("2. Search Student\n");
        printf("3. Logout\n");
        printf("Enter choice: ");
        scanf("%d", &choice);

        switch(choice) {
            case 1: displayStudents(); break;
            case 2: searchStudent(); break;
            case 3: printf("Logging out...\n"); return;
            default: printf("Invalid option!\n");
        }
    }
}


// -------- USER MENU --------
void userMenu() {
    printf("\n===== USER MENU =====\n");
    displayStudents();
}

// -------- GUEST MENU --------
void guestMenu() {
    printf("\n===== GUEST MENU =====\n");
    displayStudents();
}


// -------- STUDENT OPERATIONS --------
void addStudent() {
    FILE *fp = fopen(STUDENT_FILE, "a");
    struct Student st;

    if (!fp) {
        printf("Error opening student file!\n");
        return;
    }

    printf("\nEnter Roll: ");
    scanf("%d", &st.roll);

    printf("Enter Name: ");
    scanf(" %[^\n]", st.name);   // <-- FIX: allows full name with spaces

    printf("Enter Marks: ");
    scanf("%f", &st.marks);

    fprintf(fp, "%d %s %.2f\n", st.roll, st.name, st.marks);
    fclose(fp);

    printf("Student Added Successfully!\n");
}

void displayStudents() {
    FILE *fp = fopen(STUDENT_FILE, "r");
    struct Student st;

    if (!fp) {
        printf("No student data found!\n");
        return;
    }

    printf("\nROLL\tNAME\t\tMARKS\n");
    printf("----------------------------------\n");

    while (fscanf(fp, "%d %s %f", &st.roll, st.name, &st.marks) == 3) {
        printf("%d\t%-10s\t%.2f\n", st.roll, st.name, st.marks);
    }

    fclose(fp);
}

void searchStudent() {
    FILE *fp = fopen(STUDENT_FILE, "r");
    struct Student st;
    int roll, found = 0;

    if (!fp) {
        printf("File not found!\n");
        return;
    }

    printf("\nEnter Roll to Search: ");
    scanf("%d", &roll);

    while (fscanf(fp, "%d %s %f", &st.roll, st.name, &st.marks) == 3) {
        if (st.roll == roll) {
            printf("\nRecord Found:\n");
            printf("Roll: %d\nName: %s\nMarks: %.2f\n", st.roll, st.name, st.marks);
            found = 1;
            break;
        }
    }

    if (!found) printf("Record Not Found!\n");

    fclose(fp);
}

void updateStudent() {
    FILE *fp = fopen(STUDENT_FILE, "r");
    FILE *temp = fopen("temp.txt", "w");
    struct Student st;

    int roll, found = 0;

    if (!fp || !temp) {
        printf("Error opening file!\n");
        return;
    }

    printf("\nEnter Roll to Update: ");
    scanf("%d", &roll);

    while (fscanf(fp, "%d %s %f", &st.roll, st.name, &st.marks) == 3) {
        if (st.roll == roll) {
            found = 1;
            printf("Enter New Name: ");
            scanf(" %[^\n]", st.name);
            printf("Enter New Marks: ");
            scanf("%f", &st.marks);
        }
        fprintf(temp, "%d %s %.2f\n", st.roll, st.name, st.marks);
    }

    fclose(fp);
    fclose(temp);

    remove(STUDENT_FILE);
    rename("temp.txt", STUDENT_FILE);

    if (found) printf("Record Updated Successfully!\n");
    else printf("Record Not Found!\n");
}


void deleteStudent() {
    FILE *fp = fopen(STUDENT_FILE, "r");
    FILE *temp = fopen("temp.txt", "w");
    struct Student st;

    int roll, found = 0;

    if (!fp || !temp) {
        printf("Error opening file!\n");
        return;
    }

    printf("\nEnter Roll to Delete: ");
    scanf("%d", &roll);

    while (fscanf(fp, "%d %s %f", &st.roll, st.name, &st.marks) == 3) {
        if (st.roll == roll) {
            found = 1;
            continue;  // skip writing the matched record
        }
        fprintf(temp, "%d %s %.2f\n", st.roll, st.name, st.marks);
    }

    fclose(fp);
    fclose(temp);

    remove(STUDENT_FILE);
    rename("temp.txt", STUDENT_FILE);

    if (found) printf("Record Deleted Successfully!\n");
    else printf("Record Not Found!\n");
}
