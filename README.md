#include <stdio.h>
#include <stdlib.h>
#include <string.h>

//Jose Luis Portes Cuervo

#define NOMBRE_LEN 30
#define DEPORTE_LEN 50

typedef struct Jugador {
    int id;
    char nombre[NOMBRE_LEN];
    int edad;
    char genero;
    char deporte[DEPORTE_LEN];
    struct Jugador *sig;
} Jugador;

Jugador *inicioCola = NULL, *finCola = NULL;
Jugador *topePila = NULL;

void agregarJugador() {
    Jugador *nuevo = (Jugador *)malloc(sizeof(Jugador));
    printf("Ingrese ID: "); scanf("%d", &nuevo->id);
    printf("Ingrese nombre: "); scanf("%s", nuevo->nombre);
    printf("Ingrese edad: "); scanf("%d", &nuevo->edad);
    printf("Ingrese genero (M/F): "); scanf(" %c", &nuevo->genero);
    printf("Ingrese deporte: "); scanf("%s", nuevo->deporte);
    nuevo->sig = NULL;

    int hombres = 0, mujeres = 0;
    Jugador *aux = inicioCola;
    while (aux) {
        if (aux->genero == 'M') hombres++;
        else if (aux->genero == 'F') mujeres++;
        aux = aux->sig;
    }

    if (nuevo->edad < 15) {
        printf("\n El jugador debe tener al menos 15 años.\n");
        free(nuevo);
        return;
    }

    if (abs(hombres - mujeres) >= 5) {
        printf("\n Diferencia de genero mayor a 4. No se puede agregar hasta equilibrar.\n");
        free(nuevo);
        return;
    }

    if (finCola) finCola->sig = nuevo;
    else inicioCola = nuevo;
    finCola = nuevo;
    printf("\n Jugador agregado a la cola.\n");
}

void mostrarCola() {
    Jugador *aux = inicioCola;
    printf("\n--- Jugadores en espera ---\n");
    while (aux) {
        printf("ID: %d | Nombre: %s | Edad: %d | Genero: %c | Deporte: %s\n",
               aux->id, aux->nombre, aux->edad, aux->genero, aux->deporte);
        aux = aux->sig;
    }
}

void contarCola() {
    int count = 0;
    Jugador *aux = inicioCola;
    while (aux) {
        count++;
        aux = aux->sig;
    }
    printf("\n Total de jugadores en espera: %d\n", count);
}

void permitirParticipacion() {
    if (!inicioCola) {
        printf("\n No hay jugadores en espera.\n");
        return;
    }

    Jugador *jugador = inicioCola;
    inicioCola = inicioCola->sig;
    if (!inicioCola) finCola = NULL;

    jugador->sig = topePila;
    topePila = jugador;

    printf("\n Jugador %s ha participado.\n", jugador->nombre);
}

void mostrarHistorial() {
    Jugador *aux = topePila;
    printf("\n--- Historial de participación ---\n");
    while (aux) {
        printf("ID: %d | Nombre: %s | Edad: %d | Genero: %c | Deporte: %s\n",
               aux->id, aux->nombre, aux->edad, aux->genero, aux->deporte);
        aux = aux->sig;
    }
}

void deshacerParticipacion() {
    if (!topePila) {
        printf("\n No hay participación para deshacer.\n");
        return;
    }
    Jugador *jugador = topePila;
    topePila = topePila->sig;
    jugador->sig = NULL;

    if (finCola) finCola->sig = jugador;
    else inicioCola = jugador;
    finCola = jugador;

    printf("\n Participación de %s deshecha. Vuelve a la cola.\n", jugador->nombre);
}

int main() {
    int opcion;
    do {
        printf("\n\n=== Menú del Torneo ===\n");
        printf("1. Agregar jugador a la cola\n");
        printf("2. Mostrar jugadores en espera\n");
        printf("3. Contar jugadores en espera\n");
        printf("4. Permitir participación del siguiente jugador\n");
        printf("5. Mostrar historial de participación\n");
        printf("6. Deshacer última participación\n");
        printf("7. Salir\n");
        printf("Seleccione una opción: ");
        scanf("%d", &opcion);

        switch (opcion) {
            case 1: agregarJugador(); break;
            case 2: mostrarCola(); break;
            case 3: contarCola(); break;
            case 4: permitirParticipacion(); break;
            case 5: mostrarHistorial(); break;
            case 6: deshacerParticipacion(); break;
            case 7: printf("Saliendo...\n"); break;
            default: printf("Opción inválida.\n");
        }
    } while (opcion != 7);

    return 0;
}
