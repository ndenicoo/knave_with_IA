# Decisiones Tecnológicas
## Telegram
- **Razón de Elección:**
  - Se eligió Telegram para evitar la necesidad de desarrollar un front-end personalizado.
  - La integración nativa con n8n facilita la orquestación de los workflows, lo que simplifica la comunicación entre el usuario y el sistema.
- **Ventajas:**
  - Permite una rápida implementación sin necesidad de construir interfaces complejas.
  - La amplia adopción y la robusta API de Telegram ofrecen fiabilidad en la comunicación.
- **Limitaciones:**
  - Obliga a los usuarios a interactuar en páginas externas, lo que puede resultar visualmente pesado y  una interacción menos fluida.

## GPT-4o
- **Razón de Elección:**
  - GPT-4o es empleado para generar narraciones épicas y descripciones inmersivas que enriquecen la experiencia de juego.
- **Ventajas:**
  - Ofrece una alta calidad en la generación de texto, lo que permite crear una narrativa dinámica y atractiva.
  - Su capacidad para contextualizar y adaptar la historia en función de las interacciones del usuario.
- **Limitaciones:**
  - A la hora de llamar a las herramientas a veces no da un formato json válido, lo que puede dar errores.

## Supabase
- **Razón de Elección:**
  - Se optó por Supabase como solución para el backend de la base de datos, encargándose del almacenamiento de la información de los jugadores y sus personajes.
- **Ventajas:**
  - Proporciona una interfaz sencilla y escalable para gestionar la base de datos.
  - Permite una integración directa con los workflows de n8n, facilitando la actualización y consulta de datos.


## Heroku y Microservicios en Python
- **Razón de Elección:**
  - Los microservicios, escritos en Python y desplegados en Heroku, gestionan la selección de monstruos, el cálculo de estadísticas y la simulación de combates.
- **Ventajas:**
  - Heroku ofrece un entorno de despliegue sencillo y rápido para aplicaciones en Python, que es el lenguaje que mejor domino.

## n8n
- **Razón de Elección:**
  - Se utiliza para la automatización de workflows, coordinando tareas como el registro, inicio de sesión, creación de personajes, simulación de batallas y actualización de niveles.
- **Ventajas:**
  - A pesar de que era un requisito, es una herramienta muy buena que permite la integración de múltiples servicios sin necesidad de desarrollar una infraestructura interna compleja.
  - Su interfaz visual facilita el diseño, seguimiento y mantenimiento de los procesos automatizados.