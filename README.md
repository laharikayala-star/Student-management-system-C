#include<stdio.h>
#include <stdlib.h>
#include <string.h>

struct student {
    int roll;
    char name[50];
    float marks;
};

void addStudent();
void viewStudents();
void searchStudent();
void updateStudent();
void deleteStudent();

int main() {
    int choice;

    while (1) {
        printf("\n===== STUDENT MANAGEMENT SYSTEM =====\n");
        printf("1. Add Student\n");
        printf("2. View All Students\n");
        printf("3. Search Student\n");
        printf("4. Update Student\n");
        printf("5. Delete Student\n");
        printf("6. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: addStudent(); break;
            case 2: viewStudents(); break;
            case 3: searchStudent(); break;
            case 4: updateStudent(); break;
            case 5: deleteStudent(); break;
            case 6: exit(0);
            default: printf("Invalid choice!\n");
        }
    }
    return 0;
}

void addStudent() {
    struct student s;
    FILE *fp = fopen("students.dat", "ab");

    printf("Enter Roll Number: ");
    scanf("%d", &s.roll);
    printf("Enter Name: ");
    scanf(" %[^\n]", s.name);
    printf("Enter Marks: ");
    scanf("%f", &s.marks);

    fwrite(&s, sizeof(s), 1, fp);
    fclose(fp);

    printf("Student added successfully!\n");
}

void viewStudents() {
    struct student s;
    FILE *fp = fopen("students.dat", "rb");

    if (fp == NULL) {
        printf("No records found!\n");
        return;
    }

    printf("\nRoll\tName\t\tMarks\n");
    while (fread(&s, sizeof(s), 1, fp)) {
        printf("%d\t%s\t\t%.2f\n", s.roll, s.name, s.marks);
    }
    fclose(fp);
}

void searchStudent() {
    struct student s;
    int roll, found = 0;
    FILE *fp = fopen("students.dat", "rb");

    printf("Enter roll number to search: ");
    scanf("%d", &roll);

    while (fread(&s, sizeof(s), 1, fp)) {
        if (s.roll == roll) {
            printf("Record Found!\n");
            printf("Roll: %d\nName: %s\nMarks: %.2f\n", s.roll, s.name, s.marks);
            found = 1;
            break;
        }
    }
    if (!found)
        printf("Student not found!\n");

    fclose(fp);
}

void updateStudent() {
    struct student s;
    int roll, found = 0;
    FILE *fp = fopen("students.dat", "rb+");

    printf("Enter roll number to update: ");
    scanf("%d", &roll);

    while (fread(&s, sizeof(s), 1, fp)) {
        if (s.roll == roll) {
            printf("Enter new name: ");
            scanf(" %[^\n]", s.name);
            printf("Enter new marks: ");
            scanf("%f", &s.marks);

            fseek(fp, -sizeof(s), SEEK_CUR);
            fwrite(&s, sizeof(s), 1, fp);
            found = 1;
            printf("Record updated successfully!\n");
            break;
        }
    }
    if (!found)
        printf("Student not found!\n");

    fclose(fp);
}

void deleteStudent() {
    struct student s;
    int roll, found = 0;
    FILE *fp = fopen("students.dat", "rb");
    FILE *temp = fopen("temp.dat", "wb");

    printf("Enter roll number to delete: ");
    scanf("%d", &roll);

    while (fread(&s, sizeof(s), 1, fp)) {
        if (s.roll != roll) {
            fwrite(&s, sizeof(s), 1, temp);
        } else {
            found = 1;
        }
    }

    fclose(fp);
    fclose(temp);
    remove("students.dat");
    rename("temp.dat", "students.dat");

    if (found)
        printf("Record deleted successfully!\n");
    else
        printf("Student not found!\n");
}
