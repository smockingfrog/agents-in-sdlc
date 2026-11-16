---
title: "Ejercicio 2 - Añadir funcionalidades con el modo agente de Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

Incluso las actualizaciones más sencillas de una aplicación suelen requerir cambios en varios archivos y operaciones como ejecutar pruebas. El flujo habitual de un desarrollador puede consistir en localizar todos los archivos necesarios, hacer los cambios, ejecutar las pruebas, depurar, averiguar qué archivo se ha pasado por alto y volver a modificarlo. Y así sucesivamente.

Para eso sirve el modo agente de Copilot.

El modo agente de Copilot está diseñado para trabajar con más autonomía en el IDE. Se comporta de forma parecida a un desarrollador: primero explora la estructura del proyecto, después realiza los cambios necesarios y ejecuta tareas como las pruebas, y corrige automáticamente los errores que encuentra. Veamos cómo puedes usar el modo agente para añadir funcionalidades al sitio.

> [!NOTE]
> Aunque los nombres se parecen, el modo agente y el agente en la nube ofrecen experiencias distintas. El modo agente trabaja en el IDE, por lo que permite obtener comentarios e interactuar con rapidez. El agente en la nube está diseñado para trabajar de forma asíncrona como un miembro del equipo, interactuando contigo mediante incidencias y solicitudes de incorporación de cambios.

En este ejercicio aprenderás cómo:

- el modo agente de Copilot puede explorar el proyecto, identificar los archivos pertinentes y coordinar cambios.
- el modo agente de GitHub Copilot puede implementar funcionalidades en la interfaz y la capa de datos.
- revisar los cambios y las pruebas generados por el modo agente de Copilot antes de fusionarlos en el código base.

## Escenario

A medida que crece la lista de juegos, quieres que las personas puedan filtrarlos por categoría y editor. En el ejercicio anterior ya creaste un asistente para obtener editores; ahora terminarás el trabajo restante en la capa de datos, la interfaz y las pruebas con el modo agente de Copilot.

## Ejecuta el sitio web de Tailspin Toys

Antes de hacer cambios, exploremos el sitio web de Tailspin Toys para conocer sus funciones actuales.

El sitio web es una plataforma de financiación colectiva para juegos de mesa con temática de desarrollo. Permite mostrar una lista de juegos y ver sus detalles. Es una única aplicación Astro que genera páginas HTML estáticas durante la compilación. Las páginas consultan directamente una base de datos SQLite local desde su frontmatter mediante Drizzle ORM; no hay una API de backend independiente ni un framework de interfaz en el cliente. Los asistentes reutilizables para acceder a los datos están en `src/lib/`, y la interactividad se añade mediante un pequeño `<script>` de Astro con ámbito propio que usa las API DOM estándar.

### Inicia el sitio web

Para que sea más sencillo ejecutar el sitio web, se ha incluido una tarea `npm run dev` que inicia el único servidor de desarrollo de Astro. Sigue estos pasos para ejecutarla en tu GitHub Codespace:

1. Vuelve a tu codespace. Después del ejercicio anterior, deberías estar en `main`.
2. Abre una ventana de terminal nueva en el Codespace pulsando <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux).
3. Actualiza `main` y crea una rama para trabajar en el filtrado:

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. Ejecuta este comando para iniciar el sitio web:

   ```bash
   npm run dev
   ```

   Cuando el servidor de desarrollo de Astro esté listo, verás un mensaje con la URL, parecido al siguiente:

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> Si aparece un cuadro de diálogo que te pregunta si quieres abrir un navegador para `http://localhost:4321`, ciérralo seleccionando **x**.

5. Abre el sitio manteniendo pulsada la tecla <kbd>Command</kbd> (Mac) o <kbd>Ctrl</kbd> (Windows/Linux) y seleccionando la dirección del servidor Astro `http://localhost:4321` en el terminal.

> [!NOTE]
> En un codespace, al seleccionar el enlace local desde el terminal se te redirige automáticamente a `https://<your-codespace-name>-4321.app.github.dev/`. Es un túnel privado al codespace, que ahora aloja el servidor web.

### Explora el sitio web

Con el sitio en ejecución, puedes explorar sus funciones. Entre ellas:

- **Página de inicio**: muestra una lista de juegos de mesa con sus títulos, imágenes y descripciones.
- **Página de detalles del juego**: al seleccionar un juego, se abre su página de detalles, que incluye más información, como el título, la descripción, el editor y la categoría.

## Explora el backlog con Copilot

> [!TIP]
> **Abre Copilot Chat**
>
> Antes de empezar los siguientes ejercicios, vuelve al codespace, abre el panel de Copilot Chat y selecciona **New Chat** para iniciar una conversación nueva. La selección de modo y modelo varía según el ejercicio; cada paso indicará cuándo sea importante.

La implementación inicial del sitio funciona, pero queremos ampliarlo con nuevas funciones. Empecemos revisando el backlog. Al crear el proyecto a partir de la plantilla, se generó automáticamente un backlog de incidencias. Pide a GitHub Copilot que te muestre esos elementos.

1. Selecciona **Agent** en el selector de agentes de la vista Chat. El agente puede planificar e implementar cambios de forma autónoma en varios archivos, ejecutar comandos de terminal e invocar herramientas.

   ![Captura del selector de agentes en la vista Chat](../../../_images/shared-chat-mode-selector.png)

2. Selecciona **Claude Sonnet 4.5** en la lista de modelos disponibles.

> [!CAUTION]
> Los autores de este taller no muestran preferencia por un modelo concreto. Durante su desarrollo usamos Claude Sonnet 4.5 y lo incluimos en las instrucciones para que las sugerencias de código sean relativamente uniformes y la experiencia resulte más predecible. Sin embargo, como los modelos de lenguaje grandes son probabilísticos, es posible que recibas sugerencias distintas a las que se describen. Es normal y esperable.

> [!NOTE]
> Debido al carácter probabilístico de los modelos de lenguaje grandes, Copilot puede usar un comando MCP distinto y aun así completar la tarea.

3. Envía a Copilot este prompt para consultar las incidencias del backlog:

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. Selecciona **Continue** para ejecutar el comando que enumera las incidencias.
5. Revisa la lista generada.

Observa que Copilot también ha priorizado los elementos según lo que considera más útil para las personas que usan el sitio.

## Revisa los archivos de instrucciones

Antes de pedir al agente que genere código, conviene revisar el archivo de instrucciones que usarás para proporcionar contexto a Copilot. Utilizarás el archivo de la [interfaz de usuario (UI)](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md), que incluye contexto sobre cómo añadir funcionalidades al sitio.

1. En tu codespace, ve a `.github/instructions/ui.instructions.md`.
2. Observa las indicaciones generales para añadir funciones, entre ellas:
   - una descripción general de la arquitectura.
   - principios de diseño de componentes, facilidad para probarlos y accesibilidad.
   - enlaces a archivos de instrucciones para distintos tipos de archivo, como Astro, Tailwind CSS y Drizzle.

> [!TIP]
> Los archivos de instrucciones pueden incluir referencias a otros archivos de instrucciones y a archivos del proyecto. Las rutas son relativas a la ubicación del archivo de instrucciones. Así puedes reutilizar indicaciones, dividir instrucciones grandes en partes más manejables y proporcionar a Copilot ejemplos y plantillas.

## Implementa el filtrado

Para completar el filtrado, tendrás que realizar al menos tres cambios distintos en la aplicación:

- añadir o ajustar la lógica restante de filtrado en la capa de datos (`src/lib/`).
- añadir o actualizar las pruebas del comportamiento de filtrado.
- actualizar la página de lista de juegos para incluir la interfaz de filtrado.

Además, las pruebas deben ejecutarse correctamente antes de fusionar los cambios. El modo agente de Copilot puede encargarse de estas tareas. Añadamos la funcionalidad.

1. Puedes continuar la conversación actual con Copilot o iniciar otra seleccionando **New Chat**.
2. Selecciona **Add Context**, **Instructions** y **ui** como archivo de instrucciones.

   ![Captura de cómo seleccionar el archivo de instrucciones de la interfaz](../../../_images/ex3-select-instructions-file.png)

3. Confirma que **Agent** sigue seleccionado en el selector de agentes de la vista Chat.

   ![Captura del selector de agentes en la vista Chat](../../../_images/shared-chat-mode-selector.png)

4. Confirma que **Claude Sonnet 4.5** sigue seleccionado como modelo.
5. Pide a Copilot que implemente la funcionalidad de acuerdo con la incidencia correspondiente del backlog:

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Observa cómo Copilot explora el proyecto y localiza los archivos relacionados con la funcionalidad: tanto los asistentes de la capa de datos como la interfaz y las pruebas. Después, modifica los archivos y ejecuta las pruebas.

   ![Captura de Copilot explorando los archivos del proyecto](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Verás que Copilot realiza varias tareas, como explorar el proyecto, modificar archivos y ejecutar pruebas. El proceso puede tardar varios minutos, según la complejidad de la tarea y del código base. Mientras tanto, es posible que aparezcan los botones **Keep** y **Undo** en el editor. Cuando Copilot termine, podrás aceptar o deshacer todos los cambios. No tienes que seleccionarlos mientras el agente sigue trabajando.

7. Cuando Copilot te lo solicite, selecciona **Continue** para ejecutar las pruebas.

   ![Captura de un diálogo de Copilot Chat que pregunta si quieres ejecutar las pruebas](../../../_images/ex3-agent-mode-run-tests.png)

8. Es posible que haya pausas e incluso que fallen algunas pruebas durante el proceso. No pasa nada. Copilot alterna entre generar código y ejecutar pruebas hasta completar la tarea sin detectar errores.

   ![Captura de una sesión completa de Copilot Chat en modo agente](../../../_images/ex3-agent-mode-proposed-changes.png)

9. Explora el código generado para detectar posibles problemas.

> [!CAUTION]
> Recuerda que siempre es importante revisar el código que generan Copilot u otras herramientas de IA.

10. Vuelve al navegador en el que se está ejecutando el sitio y explora la nueva funcionalidad.
11. Cuando hayas confirmado que todo funciona y revisado el código, selecciona **Keep** en la ventana de Copilot Chat.

## Resumen y siguientes pasos

¡Enhorabuena! En este ejercicio has aprendido a usar el modo agente de GitHub Copilot para añadir funciones al sitio web de Tailspin Toys. Hemos visto cómo:

- el modo agente de GitHub Copilot puede implementar funcionalidades en la interfaz y la capa de datos.
- el modo agente de Copilot puede explorar el proyecto, identificar los archivos pertinentes y coordinar cambios.
- revisar los cambios y las pruebas generados por el modo agente de Copilot antes de fusionarlos en el código base.

Ahora [probarás la funcionalidad con el servidor MCP de Playwright][next-lesson] y abrirás una solicitud de incorporación de cambios.

### Ejercicio adicional: implementa la paginación

A medida que crezca la lista de juegos, habrá que habilitar la paginación. Usa lo aprendido en este ejercicio para pedir a Copilot que actualice el sitio. Ten en cuenta lo siguiente:

- sigue las buenas prácticas existentes, incluidos los archivos de instrucciones.
- decide cómo quieres implementar la paginación: por ejemplo, si las personas podrán seleccionar el tamaño de página o si será fijo.
- al crear el prompt, proporciona a Copilot las indicaciones necesarias para obtener la implementación que quieres.
- puede que tengas que iterar con GitHub Copilot, pedir cambios y proporcionar contexto. Este es el flujo habitual al trabajar con Copilot.

## Recursos

- [Modos ask, edit y agent de Copilot: qué hacen y cuándo se usan][choose-mode]
- [Modo agente en VS Code][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
