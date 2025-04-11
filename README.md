# wastemanagement

#include <stdio.h>
#include <string.h>

#define MAX_REQUESTS 100

typedef struct {
    int id;
    char name[50];
    char category[20];
    char location[100];
    int quantity;
    char status[20];
} WasteRequest;

WasteRequest requests[MAX_REQUESTS];
int requestCount = 0;

void addRequest() {
    if (requestCount >= MAX_REQUESTS) {
        printf("Maximum number of requests reached.\n");
        return;
    }

    WasteRequest req;
    req.id = requestCount + 1;

    printf("Enter waste name: ");
