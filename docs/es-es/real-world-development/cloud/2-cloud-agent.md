---
title: "Ejercicio 2 - Agente en la nube de GitHub Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Lección anterior: Instrucciones personalizadas][previous-lesson] |
|:--|

Es probable que muy pocas organizaciones —si es que hay alguna— no tengan problemas con la deuda técnica. Puede tratarse de problemas de seguridad sin resolver, código heredado que necesita actualizarse o solicitudes de funcionalidades que llevan tiempo en el backlog porque no ha habido tiempo para implementarlas. El agente en la nube de GitHub Copilot puede realizar tareas como actualizar código y añadir funcionalidades de forma autónoma. Cuando termina, crea un borrador de PR para que lo revise una persona. Así puedes delegar tareas tediosas, acelerar el desarrollo y dedicarte a cuestiones de mayor alcance.

Con el agente en la nube de Copilot explorarás:

- cómo personalizar el entorno de generación de código.
- cómo asegurarte de que las operaciones se realizan de forma segura.
- la importancia de delimitar claramente las incidencias.
- cómo asignar incidencias a Copilot.

## Escenarios

Tailspin Toys tiene deuda técnica que quiere resolver. Los contratistas que crearon la primera versión del sitio dejaron la documentación en mal estado: comprobarás que falta por completo. Como primer paso, quieren añadir comentarios TSDoc a todas las funciones exportadas de la aplicación.

Además, el equipo de diseño quiere mejorar el descubrimiento de juegos. Quiere que la página de detalles de cada juego muestre juegos relacionados —otros títulos de la misma categoría— para que las personas puedan seguir explorando. Todavía no necesita una implementación refinada; solo quiere algo que pueda usar para las pruebas de aceptación de la experiencia. Este trabajo bloquea otras tareas, pero ahora mismo hay incidencias de mayor prioridad.

Estos son ejemplos de tareas que pueden acabar perdiendo prioridad fácilmente y que se pueden asignar al agente en la nube de Copilot. El agente puede trabajar en ellas de forma asíncrona mientras el desarrollador se centra en otras tareas, y después puede revisar el trabajo de Copilot y comprobar que el resultado es correcto.

## Presentación del agente en la nube de GitHub Copilot

El [agente en la nube de GitHub Copilot](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) puede realizar tareas en segundo plano de forma similar a un desarrollador. Al igual que cuando trabajas con otra persona, puedes asignarle una incidencia de GitHub, entre otras opciones. Cuando se le asigna una tarea, Copilot crea un borrador de solicitud de incorporación de cambios para registrar el progreso, configura un entorno y empieza a trabajar. Puedes consultar la sesión de Copilot mientras está en curso o una vez completada. Cuando la solución propuesta esté lista para que la revises, te mencionará en la solicitud de incorporación de cambios.

## La importancia de delimitar bien las instrucciones

GitHub Copilot no hace magia: no puedes saltarte el trabajo de pensar. Incluso las operaciones que parecen sencillas pueden ser complejas al analizarlas en profundidad, así que [delimita con cuidado las tareas que asignas al agente en la nube de Copilot][cloud-agent-best-practices]. Trátalo como un compañero de programación con IA: trabaja por etapas, aprende, experimenta y adapta el proceso. Los fundamentos del desarrollo de software no cambian al incorporar IA generativa.

## Instrucciones personalizadas en este repositorio

En ejercicios anteriores has conocido las instrucciones personalizadas y cómo guían a Copilot. Si ya has completado el ejercicio sobre instrucciones personalizadas, habrás visto cómo funciona `.github/copilot-instructions.md`; si no, este es un buen momento para echarle un vistazo. Antes de asignar trabajo al agente en la nube, revisa los archivos de instrucciones que ya incluye este repositorio para que después puedas reconocer su efecto.

Abre estos archivos en la interfaz web de GitHub o en un codespace que ya tengas en ejecución:

- `.github/copilot-instructions.md`: revisa la sección **Code standards**, en especial las expectativas sobre las convenciones de TypeScript y los comentarios TSDoc.
- `.github/instructions/unit-tests.instructions.md`: fíjate en el frontmatter `applyTo`, que aplica estas instrucciones a los archivos `**/*.test.ts`.

Cuando asignes al agente en la nube la incidencia `Code lacks documentation` en la sección siguiente, observa si la solicitud de incorporación de cambios resultante incluye comentarios TSDoc, convenciones de TypeScript y comentarios de cabecera. Estas indicaciones proceden de los archivos de instrucciones. Volveremos a señalarlas cuando revisemos las PR en un ejercicio posterior.

## Configura el entorno de desarrollo del agente en la nube de Copilot

Al crear código suele ser necesario configurar un entorno y ejecutar scripts de preparación para asegurarse de que todo está listo. Lo mismo ocurre al asignar tareas a Copilot, que trabaja de forma parecida a un ingeniero de software.

El agente en la nube usa [GitHub Actions][github-actions] como entorno de trabajo. Puedes personalizarlo creando un [flujo de trabajo de configuración especial][setup-workflow] en `.github/workflows/copilot-setup-steps.yml`, que se ejecuta antes de que el agente empiece a trabajar. Así puede acceder a las herramientas y dependencias de desarrollo necesarias. Este flujo de trabajo ya está configurado para que el laboratorio funcione correctamente. Proporciona a Copilot Node.js, las dependencias del proyecto, los navegadores de Playwright para las pruebas de extremo a extremo y la base de datos SQLite migrada y preparada para la aplicación Astro:

```yaml
name: "Copilot Setup Steps"

# Permite probar los pasos de configuración desde la pestaña "Actions" del repositorio
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Define los permisos mínimos necesarios para *tus pasos*. Copilot recibirá su propio token para las operaciones que realice.
    permissions:
      # Si quieres clonar el repositorio durante la configuración (por ejemplo, para instalar dependencias), necesitarás el permiso `contents: read`. Si no clonas el repositorio en los pasos de configuración, Copilot lo hará automáticamente al terminar.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Configuración de la aplicación / frontend: Node.js (ahora toda la aplicación es Astro + Drizzle/libSQL)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # Migra y prepara la base de datos SQLite local para que las compilaciones y las pruebas tengan datos.
      - name: Set up the database
        run: npm run db:setup
```

Se parece a cualquier otro archivo de flujo de trabajo de GitHub, pero tiene algunos aspectos importantes:

- Contiene un único trabajo llamado `copilot-setup-steps`. Se ejecuta en GitHub Actions antes de que Copilot empiece a trabajar en la solicitud de incorporación de cambios.
- Observa el activador `workflow_dispatch`, que permite ejecutar manualmente el flujo de trabajo desde la pestaña **Actions** del repositorio. Esto es útil para comprobar que funciona correctamente sin esperar a que Copilot lo ejecute.

## Añade documentación

Aunque todos reconocen la importancia de la documentación, muchos proyectos tienen información desactualizada o carecen de ella. Esta deuda técnica suele quedar desatendida, lo que ralentiza el trabajo y dificulta mantener el código o incorporar a nuevos desarrolladores. Copilot destaca en la generación de documentación, así que esta es una tarea ideal para asignarle al agente en la nube. Trabajará en segundo plano para generar la documentación necesaria. En un ejercicio posterior volverás para revisar el resultado.

1. Abre una pestaña nueva del navegador y ve a tu repositorio en github.com.
2. Selecciona la pestaña **Issues**.
3. Selecciona **New issue** para abrir el diálogo de creación de incidencias.
4. Selecciona **Blank issue** para crear una incidencia en blanco.
5. Escribe `Code lacks documentation` en el campo **Title**.
6. Escribe lo siguiente en **Description**:

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. Selecciona **Create** para crear la incidencia.
8. A la derecha, selecciona **Assign to Copilot** para abrir el diálogo de asignación.

  ![Asignación de Copilot a una incidencia](../../../_images/shared-assign-copilot.png)

9. Selecciona **Assign**.
10. Selecciona la pestaña **Pull Requests**.
11. Abre la solicitud de incorporación de cambios (PR) que se acaba de generar. Su título será parecido a `[WIP]: Code lacks documentation`. Si todavía no aparece ninguna PR, espera un momento y actualiza la página.
12. Al cabo de unos minutos, Copilot debería haber creado una lista de tareas.

> [!NOTE]
> La lista de tareas de Copilot puede tardar varios minutos en aparecer en la PR. Copilot está preparando su entorno —ejecuta el flujo de trabajo que acabamos de ver—, analiza el proyecto y determina la mejor forma de abordar el problema.

13. Revisa la lista y las tareas que va a completar.
14. Desplázate por la cronología de la solicitud de incorporación de cambios. Deberías ver que Copilot ha empezado a trabajar en la incidencia.
15. Selecciona el botón **View session**.

  ![Vista de la sesión de Copilot](../../../_images/ex4-view-session.png)

> [!CAUTION]
> Puede que tengas que actualizar la página para ver el indicador actualizado.

16. Observa que puedes desplazarte por la sesión en directo y ver cómo Copilot resuelve el problema. Explora el código y comprende su estado, cómo se detiene a pensar y decidir el plan adecuado, y cómo crea código.

Este proceso puede tardar varios minutos. Uno de los objetivos principales del agente en la nube de Copilot es que pueda realizar tareas de forma asíncrona mientras tú te ocupas de otras. Aprovecharemos esa función asignándole otra tarea y, después, nos centraremos en escribir código para añadir funcionalidades a la aplicación.

## Añade una sección de juegos relacionados a la página de detalles

Como ya hemos indicado, una de las grandes ventajas del agente en la nube de GitHub Copilot es que permite dividir el trabajo: tú puedes centrarte en un conjunto de tareas mientras él se ocupa de otro. Añadir una sección de juegos relacionados para el equipo de diseño quizá no lleve mucho tiempo, pero ese tiempo se podría dedicar a otras tareas. ¡Vamos a asignárselo al agente en la nube de Copilot!

1. Vuelve a tu repositorio en github.com.
2. Selecciona la pestaña **Issues**.
3. Selecciona **New issue** para abrir el diálogo de creación de incidencias.
4. Selecciona **Blank issue** para usar la plantilla en blanco.
5. Escribe `Show related games on the game details page` en el campo **Title**.
6. Escribe lo siguiente en **Description**:

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. Selecciona **Create** para crear la incidencia.
8. A la derecha, selecciona **Assign to Copilot** para abrir el diálogo de asignación.

  ![Asignación de Copilot a una incidencia](../../../_images/shared-assign-copilot.png)

9. Selecciona **Assign**.

Al poco tiempo, verás unos 👀 en el primer comentario de la incidencia, lo que indica que Copilot está trabajando en ella.

![Copilot usa el emoji de ojos para indicar que está trabajando en la incidencia](../../../_images/ex4-issue-eyes-emoji.png)

Copilot ya está trabajando en tu segunda solicitud. El agente en la nube de Copilot trabaja de forma parecida a un ingeniero de software: no hace falta que lo supervises continuamente, puedes revisar el resultado cuando termine. Ahora vamos a crear y usar agentes personalizados.

## Resumen y siguientes pasos

En esta lección has explorado el [agente en la nube de GitHub Copilot][copilot-agents]. Puedes asignar incidencias a Copilot para que trabaje en ellas de forma asíncrona. Puedes usarlo para resolver deuda técnica, crear funcionalidades o ayudar a migrar código de un framework a otro.

Has explorado estos conceptos:

- cómo personalizar el entorno de generación de código.
- cómo asegurarte de que las operaciones se realizan de forma segura.
- la importancia de delimitar claramente las incidencias.
- cómo asignar incidencias a Copilot.

Mientras el agente en la nube trabaja, nos centraremos en crear y usar agentes personalizados. [El agente en la nube de Copilot también puede usar servidores MCP][cloud-agent-mcp] y puede acceder a las instrucciones personalizadas que hemos visto en módulos anteriores.

## Recursos

- [Acerca del agente en la nube de Copilot][copilot-agents]
- [Asignar incidencias de GitHub a Copilot][assign-issue]
- [Buenas prácticas para el flujo de configuración del agente en la nube de Copilot][cloud-agent-best-practices]

| [Siguiente lección: Agentes personalizados →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
