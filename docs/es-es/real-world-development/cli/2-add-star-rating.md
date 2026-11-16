---
title: "Lección 2 - Añadir valoraciones con estrellas: una mejora rápida"
description: "Usa Copilot CLI para hacer un pequeño cambio en las tarjetas de juegos, revisar el diff en un navegador reenviado y fusionarlo como tu primera solicitud de incorporación de cambios."
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

Ahora que has instalado Copilot CLI y has probado una conversación, es hora de hacer tu primer cambio en el proyecto. Será pequeño: los juegos ya tienen una valoración con estrellas en sus datos, pero todavía no aparece en las tarjetas de la página de inicio. Pedirás al agente que la muestre, revisarás el cambio y lo fusionarás como tu primera solicitud de incorporación de cambios.

En esta lección:

- iniciarás una conversación de Copilot centrada en una rama de funcionalidad.
- pedirás al agente que haga un pequeño cambio en el proyecto.
- revisarás el cambio con `/diff`.
- ejecutarás la aplicación para comprobar el cambio en un navegador reenviado.
- abrirás y fusionarás tu primera solicitud de incorporación de cambios.

## Escenario

Cada juego de Tailspin Toys puede tener una valoración con estrellas, que ya aparece en la página de detalles del juego. Sin embargo, las tarjetas de juegos de la página de inicio solo muestran el título, la categoría, el editor y la descripción. Para empezar, pedirás al agente que muestre en cada tarjeta la valoración existente: un cambio pequeño y autónomo, perfecto para tu primera sesión.

## Anatomía de una conversación

Una **conversación** es el espacio donde trabajas con Copilot CLI en una tarea. A diferencia de la aplicación de Copilot, una conversación normal de CLI usa el repositorio y la rama de Git que están actualmente seleccionados en el terminal, en lugar de crear un worktree dedicado. Las conversaciones guardadas te permiten volver a la misma conversación más adelante, mientras que los archivos y la rama siguen siendo el estado normal de Git en el disco.

En una conversación verás tres cosas: tus prompts y las respuestas del agente, la actividad de las herramientas que usa el agente para explorar y editar archivos, y los cambios que puedes inspeccionar con `/diff`.

## Inicia una conversación y solicita el cambio

Iniciemos una conversación nueva para empezar a implementar la funcionalidad.

1. Vuelve a tu Codespace.
2. Si el terminal no está abierto desde antes, pulsa <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux).
3. Si Copilot aún no está abierto, ejecuta el siguiente comando:

   ```bash
   copilot --yolo
   ```

4. Asegúrate de iniciar una sesión nueva con el comando de barra `/new` y pulsa <kbd>Enter</kbd>.
5. Usa este prompt para solicitar el cambio:

   ```plaintext
   Show each game's starRating out of 5 in the game cards on the list page. If the rating is null, show "No rating yet". Keep the card layout as it is, add tests, and run the relevant checks.
   ```

Copilot explora el proyecto, localiza los archivos que se usan para mostrar los detalles de los juegos y crea el código necesario. ¡Ya has añadido una funcionalidad con Copilot CLI!

## Revisa el diff

Revisa todos los cambios generados por IA antes de fusionarlos, aunque sean pequeños. Vamos a explorarlos directamente en Copilot CLI.

1. Introduce `/diff` e inspecciona todos los archivos modificados.
2. Confirma que la tarjeta muestra la valoración numérica cuando está disponible y `No rating yet` cuando `starRating` es `null`.
3. Confirma que las pruebas cubren ambos casos.
4. Revisa los resultados de las comprobaciones que ha ejecutado Copilot y pídele que corrija los fallos.
5. Cuando hayas terminado la revisión, pulsa <kbd>Esc</kbd> para salir de la vista de diff.

> [!NOTE]
> Como Copilot, al igual que todas las herramientas de IA generativa, es probabilístico y no determinista, el código exacto puede variar. Revisa el comportamiento en lugar de esperar una implementación idéntica.

## Comprueba los cambios

No basta con leer el código y dar por sentado que funciona. Pidamos a Copilot que inicie el sitio para que podamos examinar la interfaz actualizada en el navegador reenviado por Codespaces.

1. Pide a Copilot que inicie la aplicación:

   ```plaintext
   Start the app so I can inspect the star-rating change in my browser. Tell me the URL and leave the server running.
   ```

2. Cuando Codespaces indique que el puerto `4321` está disponible, selecciona **Open in Browser**.
3. Confirma que las tarjetas de los juegos muestran sus valoraciones sobre cinco.
4. Vuelve a Copilot y pídele que detenga el servidor que ha iniciado:

   ```plaintext
   Stop the development server you started.
   ```

## Abre y fusiona tu primera solicitud de incorporación de cambios

¡Ya has creado la funcionalidad! Es hora de abrir una solicitud de incorporación de cambios (PR) para fusionar el nuevo código en el proyecto.

1. Pide al agente predeterminado que confirme el cambio:

   ```plaintext
   Commit the reviewed star-rating changes with an appropriate commit message.
   ```

2. Introduce `/pr create`. Copilot CLI envía el commit existente al crear la PR y muestra su URL.
3. Abre la PR manteniendo pulsada la tecla <kbd>Command</kbd> (Mac) o <kbd>Ctrl</kbd> (Windows/Linux) y seleccionando la URL que muestra Copilot CLI.
4. Revisa los archivos modificados y las comprobaciones.
5. Cuando esté todo listo, selecciona **Merge pull request** y confirma la fusión.
6. Vuelve a tu Codespace y sal de Copilot CLI con `/exit`.
7. Actualiza tu `main` local:

   ```bash
   git checkout main
   git pull
   ```

## Resumen y siguientes pasos

¡Enhorabuena! Has publicado tu primer cambio con GitHub Copilot CLI. En concreto:

- has iniciado una conversación centrada en una rama de funcionalidad.
- has dirigido al agente para que hiciera un pequeño cambio en las tarjetas de los juegos.
- has revisado el cambio con `/diff`.
- has ejecutado la aplicación y comprobado la valoración con estrellas en un navegador reenviado.
- has abierto y fusionado tu primera solicitud de incorporación de cambios.

A continuación, [empezarás con la incidencia de filtrado y usarás los modos Plan y Autopilot][next-lesson] para crear una funcionalidad más amplia.

## Recursos

- [Acerca de GitHub Copilot CLI][about-copilot-cli]
- [Referencia de comandos de Copilot CLI][cli-reference]

[previous-lesson]: ../1-install-copilot-cli/
[next-lesson]: ../3-agent-modes/
[about-copilot-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
