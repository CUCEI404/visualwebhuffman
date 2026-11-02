# Visualizador web de algoritmos

## Integrantes
* Bryhan Francisco García Espinoza
* Roberto Hernández Zuñiga
* Emmanuel Alejandro Ortiz Segura

## Descripción
Es una página web interactiva diseñada para mostrar paso a paso cómo funcionan los distintos métodos de ordenamiento. La plataforma incluye una sección de comparativa en vivo (benchmark) para medir cuánto tardan los algoritmos en ejecutarse y una sección didáctica con animaciones. Estas animaciones ilustran cómo los elementos se comparan y cambian de posición en tiempo real utilizando barras de colores.

## Objetivo
Desarrollar y publicar una aplicación web interactiva que permita visualizar, ejecutar y comparar algoritmos de ordenamiento estudiados en clase. La aplicación deberá ayudar a una persona a entender visualmente qué hace cada algoritmo: qué elementos compara, cuáles intercambia, cómo avanza el proceso y cómo cambia el arreglo hasta quedar ordenado, tomando como referencia conceptual plataformas como Visualgo, pero desarrollando nuestra propia propuesta.

## Algoritmos implementados
Se incluyeron los siguientes algoritmos de ordenamiento, abarcando desde los métodos de fuerza bruta hasta los más eficientes:
* Bubble Sort
* Selection Sort
* Insertion Sort
* Gnome Sort
* Merge Sort
* Quick Sort
* Heap Sort
* Counting Sort / Radix Sort

## Tecnologías utilizadas
* **Frontend:** HTML5, CSS3, JavaScript .
* **Diseño:** Plantilla base "Strongly Typed".
* **Librerías:** Chart.js para la generación de la gráfica de comparación de tiempos.
* **Control de versiones:** Git y GitHub Desktop.
* **Gestión del proyecto:** GitHub Projects.

## Cómo ejecutar el proyecto
Al ser un proyecto web estático basado en tecnologías frontend clásicas, no requiere instalaciones complejas ni servidores locales.
1. Descarga el archivo ZIP del repositorio o clónalo usando GitHub Desktop.
2. Descomprime la carpeta en tu computadora.
3. Haz doble clic en el archivo `index.html` para abrirlo directamente en tu navegador web preferido (Chrome, Edge, Firefox, etc.).

## Uso de la aplicación
1. **Inicio y Comparativa:** Al abrir la página, verás una gráfica. Haz clic en "Ejecutar Pruebas" para ver en tiempo real cuánto tardan los diferentes algoritmos en ordenar arreglos de miles de elementos.
2. **Animaciones:** En el menú superior, pasa el cursor sobre las categorías de algoritmos (como O(n²)) y selecciona uno, por ejemplo, "Gnome Sort" o "Insertion Sort".
3. **Controles visuales:** En la nueva página de animación, haz clic en "Ejecutar Algoritmo" para iniciar la simulación paso a paso con las barras de colores, o usa "Generar Nuevos Datos" para crear un arreglo desordenado diferente.

## Deployment
El proyecto está publicado en un servicio de alojamiento gratuito para que pueda abrirse desde cualquier navegador mediante una URL pública.
* **Enlace de la aplicación:** `https://cucei404.github.io/visualweb/`

## Organización del equipo
Para organizar el trabajo, el equipo utilizó un tablero Kanban en **GitHub Projects**.
* Dividimos el proyecto en tareas específicas y las asignamos a cada integrante a través de las columnas: Backlog, Por hacer, En progreso, En revisión y Terminado.
* El código se manejó de forma colaborativa: cada integrante trabajó en su propia rama  y, al terminar una tarea, se abría un Pull Request para que otro compañero revisara los cambios antes de fusionarlos a la rama principal (`main`).

## Uso de IA
Se utilizaron herramientas de Inteligencia Artificial (Gemini) como apoyo durante el desarrollo técnico:
* **Traducción y adaptación de código:** Para pasar la lógica matemática de los algoritmos (Insertion y Gnome Sort) a sintaxis de JavaScript.
* **Lógica de animación asíncrona:** Para comprender cómo implementar el retraso de tiempo (`sleep`) en el ciclo de ejecución usando Promesas y `async/await`, permitiendo que el navegador pintara los cambios en el DOM sin bloquear la página.
* **Asistencia en control de versiones:** Para entender el flujo de trabajo colaborativo en GitHub Desktop, resolviendo dudas sobre cómo hacer *commits*, ramas y *pull requests* de manera segura.

## Aprendizajes y conclusiones
En este proyecto logramos comprender a fondo no solo la lógica y complejidad matemática detrás de cada método de ordenamiento, sino también el reto de manipular el DOM en JavaScript para representar esos procesos de forma visual. Aprendimos a sincronizar datos lógicos con interfaces gráficas y a utilizar herramientas profesionales como GitHub y tableros ágiles, lo cual mejoró significativamente nuestra capacidad para trabajar en equipo y gestionar un proyecto de software desde su planeación hasta su despliegue final.
