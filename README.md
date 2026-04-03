# Trabajo Práctico N° 1
**Alumno:** Fernandez Mauricio Damian  
*(Se utilizará este espacio para explicar la resolución de los ejercicios del TP 1)*

## Temas: Git, GitHub, debugging

### Punto 2
En este punto se nos pide analizar y corregir los archivos **codigo_misterioso.c** y **codigo_sin_funcionar.c** utilizando las herramientas de **debugging** instaladas en VS Code.

#### **codigo_misterioso.c**
Se nos dice de antemano:
```
codigo_misterioso.c: Contiene código ofuscado (con nombres genéricos y poco descriptivos).
Su tarea es usar el depurador para observar elcomportamiento de las variables en memoria, deducir
 qué hace lógicamente y renombrar las funciones y variables de forma adecuada.
```
El codigo en cuestion es:
```
#include <stdio.h>

void f_alpha(int *p) {
    int temp = *p;
    int rev = 0;
    while (temp > 0) {
        rev = (rev * 10) + (temp % 10);
        temp = temp / 10;
    }
    *p = rev;
}

void f_beta(int *p) {
    *p = *p / 2;
}

void f_gamma(int *p) {
    int temp = *p;
    int suma = 0;
    while (temp > 0) {
        suma = suma + (temp % 10);
        temp = temp / 10;
    }
    *p = *p + suma;
}

void procesar_enigma(int *valor_referencia) {
    f_alpha(valor_referencia);
    f_beta(valor_referencia);
    f_gamma(valor_referencia);
}

int main() {
    int dato_secreto = 452;
    
    printf("Iniciando depuracion con el valor: %d\n", dato_secreto);
    
    // Instrucción para el alumno: 
    // Pon un breakpoint aquí (F9) y usa F11 (Step Into) para entrar a cada función.
    procesar_enigma(&dato_secreto);
    
    printf("Resultado final del enigma: %d\n", dato_secreto);
    
    return 0;
}

```
- Correccion 1:
  El primer error a corregir es en el nombre de la funcion **f_alpha**, la cual invierte el numero ingresado por parametro.
 
