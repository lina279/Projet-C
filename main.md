#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_CHAMBRES 100
#define MAX_CLIENTS 100
#define MAX_NOM 50
#define MAX_DATE 11

typedef struct {
    int numero;
    int capacite;
    float tarif;
    int occupee;
} Chambre;

typedef struct {
    char nom[MAX_NOM];
    char prenom[MAX_NOM];
    char telephone[MAX_NOM];
    char dateArrivee[MAX_DATE];
    char dateDepart[MAX_DATE];
} Client;

typedef struct {
    Client client;
    Chambre chambre;
    char dateDebut[MAX_DATE];
    char dateFin[MAX_DATE];
} Reservation;

Chambre chambres[MAX_CHAMBRES];
Reservation reservations[MAX_CLIENTS];
int nombreChambres = 0, nombreReservations = 0;

void initialiserChambres();
void creerReservation();
void annulerReservation();
void genererRapports();
void configurerTarifs();
void afficherMenu();

int main() {
    int choix;

    initialiserChambres();

    while (1) {
        afficherMenu();
        printf("Votre choix : ");
        scanf("%d", &choix);

        switch (choix) {
            case 1:
                creerReservation();
                break;
            case 2:
                annulerReservation();
                break;
            case 3:
                genererRapports();
                break;
            case 4:
                configurerTarifs();
                break;
            case 5:
                exit(0);
            default:
                printf("Choix invalide. Veuillez r\u00e9essayer.\n");
        }
    }
    return 0;
}

void initialiserChambres() {
    int capacites[] = {1, 2, 2, 3, 4};
    float tarifs[] = {50.0, 70.0, 70.0, 90.0, 120.0};
    int nombreInitial = sizeof(capacites) / sizeof(capacites[0]);

    for (int i = 0; i < nombreInitial; i++) {
        chambres[i].numero = i + 1;
        chambres[i].capacite = capacites[i];
        chambres[i].tarif = tarifs[i];
        chambres[i].occupee = 0;
        nombreChambres++;
    }
    printf("%d chambres initialisées avec succès.\n", nombreChambres);
}

void creerReservation() {
    if (nombreReservations >= MAX_CLIENTS) {
        printf("Capacité maximale de réservations atteinte.\n");
        return;
    }

    int chambreId;
    printf("Entrez le numéro de la chambre : ");
    scanf("%d", &chambreId);

    if (chambreId <= 0 || chambreId > nombreChambres || chambres[chambreId - 1].occupee) {
        printf("Numéro de chambre invalide ou déjà occupée.\n");
        return;
    }

    Reservation *res = &reservations[nombreReservations];
    res->chambre = chambres[chambreId - 1];

    printf("Entrez le nom du client : ");
    scanf("%s", res->client.nom);
    printf("Entrez le prénom du client : ");
    scanf("%s", res->client.prenom);
    printf("Entrez la date de début (JJ/MM/AAAA) : ");
    scanf("%s", res->dateDebut);
    printf("Entrez la date de fin (JJ/MM/AAAA) : ");
    scanf("%s", res->dateFin);

    res->chambre.occupee = 1;
    nombreReservations++;

    printf("Réservation effectuée avec succès pour la chambre %d !\n", chambreId);
}

void annulerReservation() {
    if (nombreReservations == 0) {
        printf("Aucune réservation existante.\n");
        return;
    }

    int chambreId;
    printf("Entrez le numéro de la chambre à annuler : ");
    scanf("%d", &chambreId);

    for (int i = 0; i < nombreReservations; i++) {
        if (reservations[i].chambre.numero == chambreId) {
            reservations[i].chambre.occupee = 0;
            reservations[i] = reservations[nombreReservations - 1];
            nombreReservations--;
            printf("Réservation pour la chambre %d annulée avec succès.\n", chambreId);
            return;
        }
    }
    printf("Aucune réservation trouvée pour cette chambre.\n");
}

void genererRapports() {
    int chambresOccupees = 0;
    float revenus = 0.0;

    for (int i = 0; i < nombreChambres; i++) {
        if (chambres[i].occupee) {
            chambresOccupees++;
            revenus += chambres[i].tarif;
        }
    }

    printf("\n--- Rapport ---\n");
    printf("Chambres occupées : %d\n", chambresOccupees);
    printf("Chambres libres : %d\n", nombreChambres - chambresOccupees);
    printf("Revenus totaux : %.2f\n", revenus);
}

void configurerTarifs() {
    int jour;
    float nouveauTarif;

    printf("Entrez le jour (1-7, 1 = Lundi) : ");
    scanf("%d", &jour);
    printf("Entrez le nouveau tarif pour toutes les chambres : ");
    scanf("%f", &nouveauTarif);

    for (int i = 0; i < nombreChambres; i++) {
        chambres[i].tarif = nouveauTarif;
    }

    printf("Tarifs mis à jour pour le jour %d.\n", jour);
}

void afficherMenu() {
    printf("\n--- Menu ---\n");
    printf("1. Créer une réservation\n");
    printf("2. Annuler une réservation\n");
    printf("3. Générer des rapports\n");
    printf("4. Configurer les tarifs\n");
    printf("5. Quitter\n");
}
