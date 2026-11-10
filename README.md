# Análisis de Algoritmos · Equipo 404

Aplicación web con dos módulos, sin servidor y sin dependencias que instalar:

| Página | Qué hace |
|---|---|
| `index.html` | **Huffman Web Compressor**: busca un libro en Project Gutenberg o sube un `.txt`, lo comprime con Huffman, mide el ahorro, inspecciona el archivo `.huff` y lo descomprime verificando su integridad. |
| `animacion.html` | **Visualizador de algoritmos de ordenamiento**: Bubble, Selection, Insertion, Gnome, Exchange, Stooge, Merge y Quick Sort, paso a paso, con métricas y comparación A vs B sobre los mismos datos. |

- **URL del deployment:** `[completar: https://usuario.github.io/repositorio/]`
- **GitHub Projects:** `[completar: URL del tablero]`

## Integrantes

| Nombre | Responsabilidad principal |
|---|---|
| `[completar]` | `[p. ej. núcleo de Huffman y formato .huff]` |
| `[completar]` | `[p. ej. inspector e interfaz]` |
| `[completar]` | `[p. ej. integración con Project Gutenberg]` |
| `[completar]` | `[p. ej. pruebas, README y deployment]` |

## Flujo de la aplicación

`BUSCAR LIBRO / SUBIR TXT → ANALIZAR → CONSTRUIR HUFFMAN → COMPRIMIR → INSPECCIONAR → DESCARGAR / DESCOMPRIMIR`

Un indicador en la parte superior de `index.html` marca cada paso conforme ocurre.

## Descripción del algoritmo

Huffman es un algoritmo **voraz** que asigna códigos binarios de longitud variable: los bytes más frecuentes reciben códigos cortos y los raros, códigos largos.

1. **Frecuencias:** se cuenta cuántas veces aparece cada byte del texto (UTF-8, 256 valores posibles).
2. **Árbol:** con una cola de prioridad (un arreglo que se reordena por peso en cada vuelta) se toman los dos nodos de menor frecuencia y se fusionan en uno nuevo cuyo peso es la suma. Se repite hasta que queda un solo nodo, la raíz. La interfaz muestra estas fusiones paso a paso.
3. **Códigos:** la profundidad de cada hoja da la longitud de su código. Con esas longitudes se generan **códigos canónicos**, de modo que el archivo solo necesita guardar longitudes, no el árbol.
4. **Codificación:** cada byte del texto se sustituye por su código y los bits se empaquetan en bytes. Los bits sobrantes del último byte se rellenan con ceros.
5. **Decodificación:** con la tabla de longitudes se reconstruyen los mismos códigos y el árbol, y se recorre el flujo de bits hasta recuperar tantos bytes como indica el encabezado.

Complejidad: contar frecuencias y codificar son O(n) sobre el tamaño del texto; construir el árbol depende solo del alfabeto (a lo más 256 símbolos). Un texto de un solo símbolo usa un código de 1 bit.

El algoritmo está implementado a mano (`buildLengths`, `canonicalBits`, `huffmanCompress`, `huffmanDecompress` en `index.html`). No se usa ninguna biblioteca de compresión.

## Formato propio `.huff` (versión 2)

Todos los enteros son *big-endian*.

| Offset | Bytes | Campo | Descripción |
|---:|---:|---|---|
| 0 | 4 | Marca y versión | `48 55 46 02` = «HUF» + versión 2 |
| 4 | 4 | Tamaño original | bytes del texto original (u32) |
| 8 | 2 | `n` | número de símbolos distintos (u16) |
| 10 | 1 | Bits válidos | bits usados del último byte de datos (1–8; 0 si no hay datos) |
| 11 | 4 | CRC32 | CRC32 del texto original (u32), para verificar la integridad |
| 15 | 2·`n` | Tabla de códigos | `n` pares (byte, longitud del código), ordenados por byte |
| 15+2·`n` | resto | Datos comprimidos | códigos canónicos concatenados |

Decisiones de diseño:

- **Códigos canónicos:** los símbolos se ordenan por (longitud, valor) y se les asigna el primer código disponible de esa longitud. Así basta con guardar longitudes y la tabla ocupa 2 bytes por símbolo.
- **Bits válidos:** indica cuántos bits del último byte son datos reales; el resto es relleno.
- **Número total de bits** = (bytes de datos − 1) × 8 + bits válidos. El descompresor comprueba que coincida exactamente con los bits que consumió.
- **CRC32:** permite comprobar la integridad al descomprimir **sin tener el `.txt` original**.
- **No se guarda el texto original** dentro del archivo, solo longitudes de código y datos codificados.
- Si la marca no es «HUF», la versión no es 2, la tabla no cumple la desigualdad de Kraft o faltan datos, el archivo se rechaza con un mensaje claro.

Ejemplo real: `abracadabra` (11 bytes) →

```
48 55 46 02 | 00 00 00 0b | 00 05 | 07 | 17 ea f9 b7 | 61 01 62 03 63 03 64 03 72 03 | 4e ac 9c
 "HUF" v2     original=11   n=5     7    CRC32          a:1 b:3 c:3 d:3 r:3             datos
```

Códigos: `a=0`, `b=100`, `c=101`, `d=110`, `r=111`. Son 23 bits, es decir 3 bytes, y en el último solo cuentan 7 bits.

En textos muy pequeños el archivo comprimido puede pesar **más** que el original, porque los 15 bytes de cabecera y la tabla pesan más que lo que se ahorra. La interfaz lo muestra como ahorro negativo.

## Interfaz y métricas

Después de comprimir se muestra: tamaño original y comprimido (bytes y KB/MB), ahorro neto, porcentaje de reducción, razón de compresión, símbolos distintos y tiempo de proceso. Además se ofrecen la tabla de frecuencias y códigos, el árbol de Huffman y la construcción paso a paso.

**Inspector:** al cargar solo un `.huff` muestra nombre y extensión, versión, tamaños, ahorro, reducción, símbolos, bits totales y válidos, CRC32, un mapa proporcional del archivo (cabecera, tabla, datos), una vista parcial en binario de los datos, los códigos almacenados y el árbol reconstruido. Después, el botón **Descomprimir** muestra el texto recuperado, verifica el CRC32 y permite descargarlo como `.txt`.

**Integración con Project Gutenberg:** el buscador consulta [Gutendex](https://gutendex.com), una API pública y gratuita sobre el catálogo de Project Gutenberg (búsqueda por título, autor o palabras clave, con filtro de idioma). Al elegir un libro se descarga su texto plano directamente en el navegador. Como `gutenberg.org` normalmente no permite leer sus archivos desde otra página (CORS), se intenta primero la descarga directa y, si falla, a través de proxies públicos gratuitos. Si ambos fallan, la interfaz indica abrir el libro en Gutenberg, descargar el `.txt` y subirlo manualmente.

## Instrucciones de ejecución

No hace falta instalar nada.

1. Clona el repositorio y abre `index.html` en el navegador, o sírvelo con `python3 -m http.server` y entra a `http://localhost:8000`.
2. El buscador de Gutenberg necesita conexión a internet. El resto funciona sin conexión; la página de ordenamiento carga Chart.js desde un CDN.
3. Deployment: GitHub Pages desde la rama `main` (Settings → Pages), que es gratuito.

## Pruebas y evidencias

Las pruebas con funciones se ejecutaron en Chrome, llamando a las mismas funciones de `index.html`.

| # | Prueba | Resultado |
|---|---|---|
| 1 | **Texto pequeño** `abracadabra`: frecuencias a=5, b=2, r=2, c=1, d=1 | Códigos `0`, `100`, `111`, `101`, `110`; 23 bits; archivo de 28 bytes; se recupera `abracadabra`. Coincide con el cálculo manual. |
| 2 | **Texto grande**: texto sintético de 2 431 373 bytes (50 símbolos) | Comprimido 1 313 808 bytes; ahorro 1 117 565 bytes (**46.0 %**); razón 1.85 : 1; compresión 73 ms y descompresión 98 ms; idéntico byte a byte; CRC32 `0BD46A1D`. |
| 3 | **Integridad**: 404 entradas (aleatorias de 1 a 400 bytes, alfabeto de 3 símbolos, 1 solo símbolo, 1 000 bytes iguales, UTF-8 con acentos y emoji, 50 000 bytes aleatorios) | 404 de 404 se recuperan idénticas; 0 fallos. CRC32 de `123456789` = `CBF43926` (valor de referencia). |
| 4 | **Entrada nueva**: pangrama `El veloz murciélago hindú comía feliz cardillo y kiwi.` (57 bytes), no usado durante el desarrollo | Recuperado idéntico; archivo de 98 bytes (más grande que el original, por el peso de la tabla en textos tan cortos). |
| 5 | **Archivos dañados** | Un bit alterado en los datos → falla el CRC32. Marca distinta → «falta la marca HUF». Versión distinta → «versión no soportada». Datos truncados → «faltan datos». Longitudes inválidas → «no forman un árbol válido». |
| 6 | **Libro de Project Gutenberg** | `[completar con tu prueba en vivo: título, id, tamaño original, comprimido, % de reducción, tiempo y verificación]` |

El libro de la prueba 6 debe probarse en el deployment real, porque la descarga depende de la red.

Para repetir cualquier prueba a mano: comprime la entrada, usa **Inspeccionar este archivo**, pulsa **Descomprimir** y comprueba el mensaje de integridad en verde.

## Tecnologías utilizadas

HTML, CSS y JavaScript sin frameworks. Chart.js 4.4.1 (solo en `animacion.html`). Gutendex como API de catálogo. Fuentes de Google Fonts. GitHub Pages para el deployment.

## Uso de IA y herramientas

Se usó **Claude** (Anthropic) como apoyo para el desarrollo del buscador de Gutenberg, el formato `.huff` v2 con CRC32, el inspector, el indicador de flujo, el visualizador de ordenamiento y este README.

Validación: pruebas automáticas en Chrome (tabla anterior), revisión manual de los códigos de `abracadabra` contra el cálculo a mano y revisión del código por el equipo. `[completar: qué integrante revisó qué parte]`.

El equipo es responsable de poder explicar todo el código entregado.

## Limitaciones conocidas

- Tamaño máximo de entrada: 10 MB. Se trabaja sobre bytes UTF-8, no sobre caracteres.
- En textos muy pequeños el archivo comprimido puede ser mayor que el original.
- El formato guarda longitudes de código, no frecuencias, así que el inspector dibuja el árbol sin pesos.
- La descarga de libros depende de Gutendex y de los proxies públicos gratuitos; pueden estar lentos o caídos. Existe la alternativa manual de subir el `.txt`.
- Los archivos de versiones anteriores del formato (`HUF1`) no se pueden abrir.
- El CRC32 detecta errores accidentales, no manipulaciones intencionales.
