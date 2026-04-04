# Trabajo Práctico N° 1
**Alumno:** Fernandez Mauricio Damian  
*(Se utilizará este espacio para explicar la resolución de los ejercicios del TP 1)*

## Temas: Git, GitHub, debugging

### _Punto 2️⃣_
En este punto se nos pide analizar y corregir los archivos **codigo_misterioso.c** y **codigo_sin_funcionar.c** utilizando las herramientas de **debugging** instaladas en VS Code.

### **codigo_misterioso.c 🕵🏻‍♂️**
Se nos dice de antemano:
```
Contiene código ofuscado (con nombres genéricos y poco descriptivos).
Su tarea es usar el depurador para observar elcomportamiento de las variables en memoria, deducir
qué hace lógicamente y renombrar las funciones y variables de forma adecuada.
```
- **_Correccion 1_**:
  El primer error a corregir es en el nombre de la funcion **"f_alpha"**, la cual invierte el numero ingresado por parametro, renombrandola como **"invertir_numero"**. Ademas de esto se agrego un **print** que muestra por   pantalla el numero invertido (_Linea 11_).
  ```
   printf("El numero invertido es: %d\n",*p); 
  ```

- **_Correccion 2_**:
  La segunda correccion fue realizada a la funcion **"f_beta"**, la cual divide en 2 el numero recibido por parametro, renombrandola como **"calcular_mitad"**. Ademas se le agrego un **print** que mustra por pantalla el calculo (_linea 16_).
  ```
  printf("La mitad del numero es :%d\n",*p);
  ```
- **_Correccion 3_**:
  La tercera correccion se realizo a la funcion **"f_gamma"**, la cual suma los digitos del numero recibido por parametro, renombrandola como **"sumar_digitos"**. Ademas se le agregó un **print** que muestra por pantalla la suma de el numero recibido por parametro.
  ```
      printf("Iniciando depuracion con el valor: %d\n", dato_secreto);
  ```
### **codigo_sin_funcionar.c ⚠️**
Se nos dice:
```
Haga una lista detallando los errores
específicos que encontró (sintaxis, scanf, lógica de punteros) y explique
cómo los solucionó.
```
- **_Correccion 1_**:
  - En primer lugar se agregó la librería faltante **stdio.h** al inicio del codigo. Sin esta librería, el codigo seria imposuble de compilar.
  -- (!) Se agregó el** * **a las variables **numero**, la cual entra como parametro por referencia en la funcion _duplicar_numero_. Esto con la finalidad de poder modificar el valor de la variable que "entregó" su direccion de memoria a la funcion.
  -- (!) Se agregó el** * **a la variable **numero** dentro de la funcion _duplicar_numero_.
  Resultado:
    ```
    void duplicar_numero(int *numero) { // add * , apunta a la direccion de memoria ingresada
    *numero = *numero * 2;       // add *
    }
    ```

  
  

 
