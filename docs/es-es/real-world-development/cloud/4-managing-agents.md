---
title: "Ejercicio 4 - Supervisar y gestionar agentes"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

En los dos ejercicios anteriores has pedido al agente en la nube de Copilot que se encargue de tres tareas distintas para mejorar la experiencia de usuario y añadir funcionalidades. Aunque el agente en la nube trabaja de forma autónoma y asíncrona, sigue siendo importante poder supervisar estas tareas.

Hay varias herramientas para gestionar las tareas asignadas al agente en la nube, incluida la [página de agentes][agents-page] de GitHub.com. Desde este centro de control puedes ver todas las tareas de los agentes que tienen solicitudes de incorporación de cambios abiertas. Puedes explorar las operaciones realizadas e incluso orientar una sesión que esté en curso.

En esta lección:

- explorarás la página de agentes para supervisar las tareas del agente en la nube.
- orientarás una sesión en curso para solicitar otra funcionalidad.

## Escenario

Después de asignar al agente la creación de un modo de alto contraste, el equipo se dio cuenta de que también sería un buen momento para añadir un modo claro. Como ya se estaba actualizando el estilo del sitio y añadiendo la funcionalidad de cambio de modo, parecía lógico incluirlo. Quieres orientar el trabajo del agente para asegurarte de que añade el modo claro además del modo de alto contraste.

## Revisa las tareas del agente en la nube de Copilot

Vamos a consultar el estado de las tareas que se han asignado al agente en la nube de Copilot.

1. Ve a la [página de agentes de Copilot](https://github.com/copilot/agents).
2. Observa la lista de tareas en el panel principal y en el panel izquierdo. Deberías ver las tareas que has asignado a Copilot, entre ellas:
    - Actualizar la documentación del código.
    - Generar API para modificar juegos.
    - Añadir un modo de alto contraste al sitio web.
3. Selecciona una de las tareas en curso. Revisa las operaciones que ha realizado Copilot, que pueden incluir:
    - Obtener el código del repositorio.
    - Crear el entorno de trabajo de Copilot.
    - Configurar servidores MCP.
    - Realizar varios pasos para completar la tarea asignada.

> [!NOTE]
> Los pasos concretos variarán según el estado del trabajo de Copilot y el enfoque que haya adoptado.

4. Observa también el panel de solicitudes de incorporación de cambios (PR) que aparece a la derecha. Desde allí puedes ver la PR y los archivos modificados para supervisar el trabajo.

## Dirige el trabajo del agente en la nube

Ahora que has visto las tareas activas, pidamos a Copilot que añada el modo claro mientras trabaja en el modo de alto contraste.

1. Selecciona la sesión relacionada con la creación del modo de alto contraste. El título exacto dependerá del nombre que haya elegido Copilot y del estado actual del trabajo.

    ![Sesión de accesibilidad en el centro de control](../../../_images/ex6-accessibility-session.png)

2. Observa la sesión durante unos minutos, hasta que indique que ha terminado la configuración y ha empezado a trabajar. Verás mensajes similares a los que se muestran a continuación.
3. En el diálogo **Steer active session while Copilot is working**, escribe este prompt:

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![Captura de la tarea del agente en la nube en la página de agentes, con el diálogo para dirigir la sesión activa de Copilot resaltado](../../../_images/ex6-steer-cloud-agent-task.png)

4. Pulsa <kbd>Enter</kbd> para enviar el prompt.
5. Observa cómo Copilot lo reconoce y lo incorpora a su flujo de trabajo.

## Deja que Copilot trabaje

Como antes, Copilot seguirá trabajando en la tarea actualizada. Incorporará la nueva solicitud a su flujo de trabajo cuando termine el paso en el que estaba trabajando al enviar el mensaje.

Esto también tardará varios minutos. Es un buen momento para hacer una pausa y repasar lo que has aprendido hasta ahora.

## Resumen y siguientes pasos

En esta lección has explorado la página de agentes de Copilot, el centro principal para supervisar y orientar las tareas del agente en la nube de GitHub Copilot. Desde este centro de control puedes seguir las tareas activas y completadas, revisar el trabajo que se está haciendo e incluso redirigir las tareas en curso para ajustar su alcance o proporcionar más indicaciones.

Has explorado estos conceptos:

- has explorado el centro de control y la página de agentes para supervisar las tareas del agente en la nube.
- has redirigido una sesión en curso para solicitar otra funcionalidad.

Mientras Copilot termina de trabajar en las funciones de accesibilidad, podemos centrarnos en [iterar sobre las solicitudes de incorporación de cambios que ha creado][next-lesson].

## Recursos

- [Página de agentes de Copilot][agents-page]
- [Acerca de los agentes personalizados][custom-agents]

---

| [← Lección anterior: Agentes personalizados][previous-lesson] | [Siguiente lección: Iterar sobre el trabajo de Copilot →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
