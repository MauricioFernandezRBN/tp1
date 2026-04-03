# Trabajo Práctico N° 1
**Alumno:** Fernandez Mauricio Damian  
*(Se utilizará este espacio para explicar la resolución de los ejercicios del TP 1)*

## Temas: Git, GitHub, debugging

### _Punto 2️⃣_
En este punto se nos pide analizar y corregir los archivos **codigo_misterioso.c** y **codigo_sin_funcionar.c** utilizando las herramientas de **debugging** instaladas en VS Code.

### **codigo_misterioso.c 🕵🏻‍♂️**
Se nos dice de antemano:
```
codigo_misterioso.c: Contiene código ofuscado (con nombres genéricos y poco descriptivos).
Su tarea es usar el depurador para observar elcomportamiento de las variables en memoria, deducir
 qué hace lógicamente y renombrar las funciones y variables de forma adecuada.
```
- **_Correccion 1_**:
  El primer error a corregir es en el nombre de la funcion **f_alpha**, la cual invierte el numero ingresado por parametro.
  Por lo que se procedio a cambiarle el nombre a **invertir_numero**. Ademas de esto se agrego un **print** que muestra por   pantalla el numero invertido (_Linea 11_).
  ```
   printf("El numero invertido es: %d\n",*p); 
  ```

- **_Correccion 2_**:
  La segunda correccion fue realizada a la funcion **f_beta**, renombrandola como **calcular_mitad**. Ademas se le agrego     un **print** que mustra por pantalla el calculo (_linea 16_).
  ```
  printf("La mitad del numero es :%d\n",*p);
  ```
- **_Correccion 3_**:
  La tercera correccion se realizo a la funcion **f_gamma**, renombrandola como **sumar_digitos**. Ademas se le agregó un **print** que muestra por pantalla la suma de el numero recibido por parametro.
  ```
      printf("Iniciando depuracion con el valor: %d\n", dato_secreto);
  ```
  
  
  

 
