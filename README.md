
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
