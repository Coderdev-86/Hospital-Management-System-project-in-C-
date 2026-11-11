
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX 100

struct Patient
{
    int id;
    char name[50];
    int age;
    char gender[10];
    char disease[50];
    char phone[20];
};

struct Appointment
{
    int patientId;
    char doctor[50];
    char date[20];
    char time[20];
};

struct Patient patients[MAX];
struct Appointment appointments[MAX];

int patientCount = 0;
int appointmentCount = 0;

// Save patient details to file
void savePatients()
{
    FILE *fp = fopen("patients.dat", "wb");

    if (fp == NULL)
    {
        printf("File error!\n");
        return;
    }

    fwrite(&patientCount, sizeof(int), 1, fp);
    fwrite(patients, sizeof(struct Patient),
           patientCount, fp);

    fclose(fp);
}

// Load patient details from file
void loadPatients()
{
    FILE *fp = fopen("patients.dat", "rb");

    if (fp == NULL)
        return;

    fread(&patientCount, sizeof(int), 1, fp);

    if (patientCount < 0 || patientCount > MAX)
    {
        patientCount = 0;
        fclose(fp);
        return;
    }

    fread(patients, sizeof(struct Patient),
          patientCount, fp);

    fclose(fp);
}

// Save appointments
void saveAppointments()
{
    FILE *fp = fopen("appointments.dat", "wb");

    if (fp == NULL)
        return;

    fwrite(&appointmentCount, sizeof(int), 1, fp);
    fwrite(appointments, sizeof(struct Appointment),
           appointmentCount, fp);

    fclose(fp);
}

// Load appointments
void loadAppointments()
{
    FILE *fp = fopen("appointments.dat", "rb");

    if (fp == NULL)
        return;

    fread(&appointmentCount, sizeof(int), 1, fp);

    if (appointmentCount < 0 ||
        appointmentCount > MAX)
    {
        appointmentCount = 0;
        fclose(fp);
        return;
    }

    fread(appointments, sizeof(struct Appointment),
          appointmentCount, fp);

    fclose(fp);
}

// Register patient
void registerPatient()
{
    if (patientCount >= MAX)
    {
        printf("Patient limit reached!\n");
        return;
    }

    struct Patient p;

    p.id = patientCount + 1;

    printf("\nEnter Patient Name: ");
    scanf(" %49[^\n]", p.name);

    printf("Enter Age: ");
    scanf("%d", &p.age);

    if (p.age < 0 || p.age > 130)
    {
        printf("Invalid age!\n");
        return;
    }

    printf("Enter Gender: ");
    scanf(" %9s", p.gender);

    printf("Enter Disease: ");
    scanf(" %49[^\n]", p.disease);

    printf("Enter Phone Number: ");
    scanf(" %19s", p.phone);

    patients[patientCount] = p;
    patientCount++;

    savePatients();

    printf("\nPatient Registered Successfully!\n");
    printf("Patient ID: %d\n", p.id);
}

// Display patients
void displayPatients()
{
    if (patientCount == 0)
    {
        printf("\nNo patient records found!\n");
        return;
    }

    printf("\n===== PATIENT DETAILS =====\n");

    for (int i = 0; i < patientCount; i++)
    {
        printf("\nPatient ID: %d", patients[i].id);
        printf("\nName: %s", patients[i].name);
        printf("\nAge: %d", patients[i].age);
        printf("\nGender: %s", patients[i].gender);
        printf("\nDisease: %s", patients[i].disease);
        printf("\nPhone: %s\n", patients[i].phone);
    }
}

// Search patient
void searchPatient()
{
    int id, found = 0;

    printf("\nEnter Patient ID: ");
    scanf("%d", &id);

    for (int i = 0; i < patientCount; i++)
    {
        if (patients[i].id == id)
        {
            printf("\nPatient Found!\n");
            printf("Name: %s\n", patients[i].name);
            printf("Age: %d\n", patients[i].age);
            printf("Gender: %s\n", patients[i].gender);
            printf("Disease: %s\n", patients[i].disease);
            printf("Phone: %s\n", patients[i].phone);

            found = 1;
            break;
        }
    }

    if (!found)
        printf("Patient Not Found!\n");
}

// Book appointment
void bookAppointment()
{
    if (appointmentCount >= MAX)
    {
        printf("Appointment limit reached!\n");
        return;
    }

    int id, found = 0;

    printf("\nEnter Patient ID: ");
    scanf("%d", &id);

    for (int i = 0; i < patientCount; i++)
    {
        if (patients[i].id == id)
        {
            found = 1;
            break;
        }
    }

    if (!found)
    {
        printf("Patient ID not found!\n");
        return;
    }

    appointments[appointmentCount].patientId = id;

    printf("Enter Doctor Name: ");
    scanf(" %49[^\n]",
          appointments[appointmentCount].doctor);

    printf("Enter Appointment Date (DD-MM-YYYY): ");
    scanf(" %19s", appointments[appointmentCount].date);

    printf("Enter Appointment Time: ");
    scanf(" %19s", appointments[appointmentCount].time);

    appointmentCount++;

    saveAppointments();

    printf("\nAppointment Booked Successfully!\n");
}

// Display appointments
void displayAppointments()
{
    if (appointmentCount == 0)
    {
        printf("\nNo appointments found!\n");
        return;
    }

    printf("\n===== APPOINTMENT DETAILS =====\n");

    for (int i = 0; i < appointmentCount; i++)
    {
        printf("\nPatient ID: %d",
               appointments[i].patientId);

        printf("\nDoctor: %s",
               appointments[i].doctor);

        printf("\nDate: %s",
               appointments[i].date);

        printf("\nTime: %s\n",
               appointments[i].time);
    }
}

// Generate bill
void generateBill()
{
    int id, found = 0;
    float consultation, medicine, room, total;

    printf("\nEnter Patient ID: ");
    scanf("%d", &id);

    for (int i = 0; i < patientCount; i++)
    {
        if (patients[i].id == id)
        {
            found = 1;

            printf("\nPatient Name: %s\n",
                   patients[i].name);

            printf("Consultation Charge: ");
            scanf("%f", &consultation);

            printf("Medicine Charge: ");
            scanf("%f", &medicine);

            printf("Room Charge: ");
            scanf("%f", &room);

            if (consultation < 0 || medicine < 0 ||
                room < 0)
            {
                printf("Invalid charge!\n");
                return;
            }

            total = consultation + medicine + room;

            printf("\n===== HOSPITAL BILL =====\n");
            printf("Patient ID: %d\n", id);
            printf("Patient Name: %s\n", patients[i].name);

            printf("----------------------------\n");
            printf("Consultation: Rs. %.2f\n", consultation);
            printf("Medicine:     Rs. %.2f\n", medicine);
            printf("Room Charge:  Rs. %.2f\n", room);

            printf("----------------------------\n");
            printf("Total Bill:   Rs. %.2f\n", total);
            printf("============================\n");

            break;
        }
    }

    if (!found)
        printf("Patient Not Found!\n");
}

// Main function
int main()
{
    int choice;

    loadPatients();
    loadAppointments();

    while (1)
    {
        printf("\n\n");
        printf("================================\n");
        printf("     HOSPITAL MANAGEMENT\n");
        printf("================================\n");

        printf("1. Register Patient\n");
        printf("2. Display All Patients\n");
        printf("3. Search Patient\n");
        printf("4. Book Appointment\n");
        printf("5. Display Appointments\n");
        printf("6. Generate Hospital Bill\n");
        printf("7. Exit\n");

        printf("================================\n");
        printf("Enter Your Choice: ");

        if (scanf("%d", &choice) != 1)
        {
            printf("Invalid input!\n");
            break;
        }

        switch (choice)
        {
            case 1:
                registerPatient();
                break;

            case 2:
                displayPatients();
                break;

            case 3:
                searchPatient();
                break;

            case 4:
                bookAppointment();
                break;

            case 5:
                displayAppointments();
                break;

            case 6:
                generateBill();
                break;

            case 7:
                printf("\nThank You!\n");
                printf("Hospital Management System Closed.\n");
                return 0;

            default:
                printf("\nInvalid Choice!\n");
        }
    }

    return 0;
}
