# Manejo de Archivos en C# — para alguien que viene de C

## 1. Archivos y directorios: el concepto no cambia

Un **directorio** sigue siendo lo mismo que en C: un contenedor de archivos y subdirectorios.
Un **archivo** sigue siendo una colección de bytes guardada en disco, con un nombre.

Lo único que cambia es **quién maneja los detalles sucios**. En C vos administrás todo a mano
(punteros, buffers, `malloc`). En C# hay clases que ya hacen ese trabajo por vos.

## 2. Tipos de archivo: texto plano vs binario

Es exactamente la misma distinción que hacías en C con `fopen(archivo, "r")` (texto) vs
`fopen(archivo, "rb")` (binario):

- **Texto plano**: bytes que representan caracteres legibles (letras, números, `\n`, `\t`). Ej: `.txt`, `.csv`.
- **Binario**: bytes que representan cualquier otra cosa codificada (imágenes, ejecutables, audio). Hay que
  saber de antemano **cómo está organizado** el archivo para poder interpretarlo (igual que cuando
  definías un `struct` en C y lo volcabas a disco con `fwrite`).

## 3. Formatos de archivo: header, cuerpo, EOF

Un archivo binario suele tener:

- **Header (cabecera)**: bytes al principio que indican qué tipo de archivo es y datos relevantes
  (dimensiones, fechas, tipo de compresión, etc.). Muchas veces empieza con un **magic number** /
  firma hexadecimal fija. Ejemplos de las firmas que vimos en la teoría:

  | Extensión | Firma (hex) | En ASCII |
  |---|---|---|
  | .PNG | `89 50 4E 47` | PNG |
  | .PDF | `25 50 44 46` | %PDF |
  | .MP3 (tag) | `49 44 33` | ID3 |
  | .ZIP | `50 4B 03 04` | PK |

- **Cuerpo**: los datos en sí.
- **EOF**: marca de fin de archivo (en algunos formatos).

Esto es igual a cuando en C leías un `struct` con campos en posiciones fijas: si conocés el
"layout" del archivo, podés saltar (`fseek`) directamente a la posición que te interesa.

## 4. La librería System.IO — el equivalente a `stdio.h`

En C usabas funciones sueltas: `fopen`, `fread`, `fwrite`, `fseek`, `fclose`.
En C# eso se organiza en clases dentro del namespace `System.IO`:

| C | C# (equivalente conceptual) |
|---|---|
| `fopen("archivo", "r")` | `File.Open(...)` / `new FileStream(...)` |
| `fread(buffer, size, n, fp)` | `FileStream.Read(buffer, offset, count)` o `BinaryReader.Read...()` |
| `fwrite(...)` | `FileStream.Write(...)` o `StreamWriter.Write(...)` |
| `fseek(fp, offset, origen)` | `FileStream.Seek(offset, SeekOrigin)` |
| `fclose(fp)` | `Stream.Close()` o, mejor, `using(...)` |
| chequear si existe con `access()`/intentar abrir | `File.Exists(path)` / `Directory.Exists(path)` |
| recorrer un directorio (`opendir`/`readdir` en C) | `Directory.GetFiles(path)` / `Directory.GetDirectories(path)` |

### Clases clave

- **`Directory`** (métodos *estáticos*, no necesitás crear un objeto):
  - `Directory.Exists(path)` → bool
  - `Directory.GetDirectories(path)` → array de carpetas
  - `Directory.GetFiles(path)` → array de archivos
- **`File`** (métodos estáticos):
  - `File.Exists(path)`
  - `File.ReadAllLines(path)` / `File.ReadAllText(path)`
  - `File.WriteAllLines(path, lineas)`
  - `File.Copy(...)`, `File.Delete(...)`
- **`FileInfo`** (es un *objeto*, lo instanciás con `new FileInfo(ruta)`): te da datos de UN archivo
  puntual, como `.Length` (tamaño en bytes), `.Name`, `.LastWriteTime`, `.Extension`.
  Es el equivalente a llenar un `struct stat` en C con `stat()`.
- **`Path`**: ayuda a armar/parsear rutas de forma portable (`Path.Combine`, `Path.GetFileName`,
  `Path.GetExtension`) — te evita concatenar strings con `/` o `\` a mano como harías en C.

**Tip importante para vos que venís de C**: `Directory` y `File` son **estáticos** → los usás
directo como `Directory.Exists(...)`, sin crear instancia. `FileInfo` y `DirectoryInfo` son
**clases normales** → necesitás `new FileInfo(ruta)` primero, y después usás sus propiedades.
Es la diferencia entre "una función que actúa sobre una ruta" (estático) y "un objeto que
representa ese archivo concreto" (instancia).

## 5. Stream: la abstracción detrás de todo

Un **Stream** es básicamente lo que en C imaginabas mentalmente cuando trabajabas con un `FILE*`:
un flujo de bytes que se puede leer y/o escribir, con una posición actual (el "puntero" del archivo).

`System.IO.Stream` es una clase abstracta. Todo lo que lee/escribe datos en C# hereda de ella:

- **`FileStream`**: lee/escribe bytes de un archivo físico. Es el más parecido a manejar
  `fopen`/`fread`/`fseek` en C, porque trabaja con **bytes crudos**.
- **`MemoryStream`**: igual, pero en memoria (no en disco).
- **`StreamReader` / `StreamWriter`**: son *ayudantes* (helpers) pensados para **texto**. Convierten
  bytes ↔ caracteres automáticamente. Son el equivalente a `fgets`/`fprintf`.
- **`BinaryReader` / `BinaryWriter`**: ayudantes para leer/escribir **tipos primitivos** (`int`,
  `float`, bytes) sin tener que hacer la conversión manual. Es el equivalente a leer un `struct`
  binario campo por campo en C, pero sin tener que calcular tamaños con `sizeof`.

### El combo clásico para leer binario con offsets fijos (lo que necesitás para leer cabeceras)

```csharp
using (FileStream fs = new FileStream(ruta, FileMode.Open, FileAccess.Read))
using (BinaryReader reader = new BinaryReader(fs))
{
    fs.Seek(offset, SeekOrigin.Begin);     // como fseek(fp, offset, SEEK_SET)
    byte[] datos = reader.ReadBytes(longitud); // como fread(buffer, 1, longitud, fp)
}
```

`SeekOrigin` tiene tres valores, igual que en `fseek`:
- `SeekOrigin.Begin` → desde el principio (como `SEEK_SET`)
- `SeekOrigin.Current` → desde la posición actual (como `SEEK_CUR`)
- `SeekOrigin.End` → desde el final (como `SEEK_END`) — **clave** cuando el dato que buscás
  está al final del archivo, como un tag ID3v1 de MP3.

## 6. Encoding: convertir bytes a texto

Cuando leés bytes crudos de un archivo binario y una parte de eso son en realidad texto
(por ejemplo, el título de una canción en un tag MP3), tenés que decirle a C# **cómo
interpretar esos bytes como caracteres**. Eso es lo que en C nunca pensabas explícitamente
porque `char` ya era 1 byte = 1 carácter ASCII. En C#:

```csharp
string texto = Encoding.GetEncoding("latin1").GetString(bufferDeBytes);
```

`latin1` (ISO-8859-1) es la codificación clásica para metadatos antiguos como ID3v1, donde cada
byte = un carácter, sin caracteres especiales raros de UTF-8.

## 7. Resumen mental para no perderte

1. ¿Necesito **listar/explorar** carpetas y archivos? → `Directory` y `FileInfo`.
2. ¿Necesito **leer/escribir texto plano** (líneas, CSV simple)? → `File.ReadAllLines`,
   `File.WriteAllLines`, o `StreamWriter`/`StreamReader`.
3. ¿Necesito **leer bytes en posiciones específicas** de un archivo binario (un header, un tag)?
   → `FileStream` + `Seek` + `BinaryReader`, y `Encoding.GetString` si esos bytes son texto.

Con esto ya tenés el mapa completo. Ahora vamos a la práctica.
