#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>
#include <ctype.h>

/* run this program using the console pauser or add your own getch, system("pause") or input loop */
void DOM ();
void DevP();
int main(int argc, char *argv[]) {
	int S;
	char F[20];
		printf("Bienvenido al programa de calculadora de multiples variables Ingrese su nombre o alias: \n");
			gets(F);
				printf("Hola %s Iniciamos el programa \n",F);
	printf("Ingrese el numero de la operacion que desee iniciar: \n");
	printf("***Dominio Ingresa 1:*** \n");
	printf("***Derivadas parciales Ingresa 2:*** \n");	
	scanf("%i",&S);
	while (S<0 ){
			printf("No se encontro el comando, ingresa un numero valido \n");
	scanf("%i",&S);
	}
		while (S>2 ){
			printf("No se encontro el comando, ingresa un numero valido: \n");
	scanf("%i",&S);
	}
	switch(S){
		case 1:{
	DOM ();
			break;
		}
		case 2:{

	DevP();	
			break;
		}
		default:{
				printf("No se encontro el comando, inicia nuevamente el programa \n");
			break;
		}
	}	
	return 0;
}

void DOM() {
    int n, i;
    char funcion[200]; 
    struct Variable {
        char nombre[50]; 
    } variables[20];

    printf("Cuantas variables tiene su funcion (maximo 20): ");
    scanf("%d", &n);
    getchar(); 

    if (n < 1 || n > 20) {
        printf("Numero de variables invalido. Intente nuevamente.\n");
        return;
    }

    printf("Ingrese los nombres de las variables:\n");
    for (i = 0; i < n; i++) {
        printf("Variable %d: ", i + 1);
        fgets(variables[i].nombre, sizeof(variables[i].nombre), stdin);
        variables[i].nombre[strcspn(variables[i].nombre, "\n")] = '\0'; // Eliminar salto de lï¿½nea
    }

    printf("\nIngrese su funcion matematica (ej. sin(x) + cos(y) + 1/(x+y)+ln(x)+sqrt(y)):\n");
    fgets(funcion, sizeof(funcion), stdin);
    funcion[strcspn(funcion, "\n")] = '\0'; 

    printf("\nFuncion ingresada: f(");
    for (i = 0; i < n; i++) {
        printf("%s", variables[i].nombre);
        if (i < n - 1) printf(", ");
    }
    printf(") = %s\n", funcion);

    printf("\n?? Analisis del dominio:\n");

    if (strstr(funcion, "ln(") || strstr(funcion, "log(")) {
        printf(" Restriccion: El argumento de ln() o log() debe ser mayor que 0.\n");
    }

    if (strstr(funcion, "sqrt(")) {
        printf("Restriccion: El argumento de sqrt() debe ser mayor o igual a 0.\n");
    }

    char* ptr = funcion;
    while ((ptr = strchr(ptr, '/')) != NULL) {
        char expr[100] = {0};
        int j = 0;
        ptr++; // Avanzar al denominador

        if (*ptr == '(') {
            ptr++; 
            int parentesis = 1;
            while (*ptr && parentesis > 0 && j < 99) {
                if (*ptr == '(') parentesis++;
                if (*ptr == ')') parentesis--;
                if (parentesis > 0) {
                    expr[j++] = *ptr;
                }
                ptr++;
            }
            expr[j] = '\0';
            if (strlen(expr) > 0) {
                printf("Restriccion: La expresion '%s' no debe ser igual a 0.\n", expr);
            }
        } else {

while (*ptr && *ptr != '+' && *ptr != '-' && *ptr != '*' && *ptr != '/' && j < 99) {
    expr[j++] = *ptr;
    ptr++;
}
expr[j] = '\0';

if (strlen(expr) > 0) {
    printf("Restriccion: '%s' no debe ser igual a 0.\n", expr);
}
        }
    }

    if (strstr(funcion, "sin(")) {
        printf("Dominio: El argumento de sin() puede tomar cualquier valor real.\n");
    }
    if (strstr(funcion, "cos(")) {
        printf("Dominio: El argumento de cos() puede tomar cualquier valor real.\n");
    }

    printf("\n Dominio: La funcion esta definida en todos los valores reales que cumplen las restricciones anteriores.\n");
}

void DevP() {
    int n, i, sumas;
    char funcion[200];
    struct Variable {
        char nombre[50];
    } variables[20];

    printf("Cuantas variables tiene su funcion (maximo 20): ");
    scanf("%d", &n);
    getchar(); 

    if (n < 1 || n > 20) {
        printf("Numero de variables invalido.\n");
        return;
    }

    for (i = 0; i < n; i++) {
        printf("Variable %d: ", i + 1);
        fgets(variables[i].nombre, sizeof(variables[i].nombre), stdin);
        variables[i].nombre[strcspn(variables[i].nombre, "\n")] = '\0'; 
    }

    printf("\nCuantas sumas tiene la funcion (ej. para x^2 + ln(y), ): ");
    scanf("%d", &sumas);
    getchar();

    printf("\nIngrese su funcion matematica (ej. 3x^1y^2 + ln(y) + sin(x)):\n");
    fgets(funcion, sizeof(funcion), stdin);
    funcion[strcspn(funcion, "\n")] = '\0'; 

    char *src = funcion, *dst = funcion;
    while (*src) {
        if (!isspace((unsigned char)*src)) *dst++ = *src;
        src++;
    }
    *dst = '\0';

    printf("\nFuncion ingresada: f(");
    for (i = 0; i < n; i++) {
        printf("%s", variables[i].nombre);
        if (i < n - 1) printf(", ");
    }
    printf(") = %s\n", funcion);

    printf("\nDerivadas parciales:\n");

    for (i = 0; i < n; i++) {
        printf("\nf/%s:\n", variables[i].nombre);
        char funcion_copia[200];
        strcpy(funcion_copia, funcion);

        char *term = strtok(funcion_copia, "+");
        int encontrados = 0;

        while (term != NULL) {
            if (strstr(term, variables[i].nombre)) {
                encontrados++;
                int coef = 1; 
                if (strstr(term, "x^") && strstr(term, "y^")) {
                    char coef_str[20] = "1", exp_x[10] = "1", exp_y[10] = "1";
                    sscanf(term, "%[^x]x^%[^y]y^%s", coef_str, exp_x, exp_y);
                    coef = atoi(coef_str);
                    if (coef == 0) coef = 1;

                    int ex = atoi(exp_x);
                    int ey = atoi(exp_y);

                    if (strcmp(variables[i].nombre, "x") == 0) {
                        printf("Derivada de %s respecto a %s: %d*x^%d*y^%d\n", term, variables[i].nombre, coef * ex, ex - 1, ey);
                    } else if (strcmp(variables[i].nombre, "y") == 0) {
                        printf("Derivada de %s respecto a %s: %d*x^%d*y^%d\n", term, variables[i].nombre, coef * ey, ex, ey - 1);
                    }
                }

                else if (strchr(term, '^')) {
                    char base[50] = "", exp_str[10] = "";
                    if (sscanf(term, "%[^'^']^%s", base, exp_str) == 2) {
                        char *vpos = strstr(base, variables[i].nombre);
                        if (vpos && vpos != base) {
                            char temp[20] = "";
                            strncpy(temp, base, vpos - base);
                            coef = atoi(temp);
                            if (coef == 0) coef = 1;
                        }
                        int e = atoi(exp_str);
                        printf("Derivada de %s respecto a %s: %d*%s^%d\n", term, variables[i].nombre, coef * e, variables[i].nombre, e - 1);
                    } else {
                        printf("No se pudo analizar el tï¿½rmino '%s'\n", term);
                    }
                }

                else if (strstr(term, "ln(")) {
                    char arg[50] = {0};
                    sscanf(strstr(term, "ln(") + 3, "%[^)]", arg);
                    printf("Derivada de %s respecto a %s: 1/%s\n", term, variables[i].nombre, arg);
                }

                else if (strstr(term, "sin(")) {
                    char arg[50] = {0};
                    sscanf(strstr(term, "sin(") + 4, "%[^)]", arg);
                    printf("Derivada de %s respecto a %s: cos(%s)\n", term, arg, arg);
                }

                else if (strstr(term, "cos(")) {
                    char arg[50] = {0};
                    sscanf(strstr(term, "cos(") + 4, "%[^)]", arg);
                    printf("Derivada de %s respecto a %s: -sin(%s)\n", term, arg, arg);
                }
                else {
                    printf("? No se pudo analizar el termino ya que no esta en la biblioteca de derivadas (se nos olvido ponerla perdon profe)'%s'\n", term);
                }
            }
            term = strtok(NULL, "+");
        }

        if (encontrados == 0) {
            printf("No hay dependencia de %s. Derivada: 0\n", variables[i].nombre);
        }
    }

    printf("\n? Derivadas parciales completadas.\n");
}
