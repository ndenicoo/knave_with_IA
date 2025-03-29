# Arquitectura del Sistema IA Game Master para Knave

La arquitectura del sistema IA Game Master para Knave combina la potencia de microservicios en Python, la automatización de workflows con n8n, la narración dinámica con GPT-4o y la robustez de una base de datos en Supabase. Este enfoque modular y escalable permite una experiencia de juego inmersiva y automatizada, donde cada componente se integra para ofrecer una narrativa interactiva y una simulación de combate fluida.

## Componentes Principales

### 1. Interfaz de Usuario y Comunicación
- **Telegram:** Se utiliza como interfaz principal para la interacción con el usuario. A través de Telegram se reciben comandos, se solicitan datos para la creación y edición de personajes, y se envían notificaciones y resultados de batallas.
- **Workflow de n8n:** Automatiza la ejecución de tareas, integrando los distintos procesos del juego (registro, inicio de sesión, creación de personajes, batallas y actualización de niveles).

### 2. Lógica de Narración y Simulación de Combate
- **GPT-4:** Responsable de generar las narraciones épicas, descripciones de combates y la historia general, proporcionando un enfoque inmersivo al juego.
- **Microservicios en Python:** Desarrollados para:
  - Seleccionar aleatoriamente monstruos.
  - Calcular estadísticas de combate.
  - Simular la batalla entre el personaje y el monstruo.
  
  Estos microservicios están escritos en Python y desplegados en Heroku, lo que permite escalabilidad y gestión independiente de cada servicio.

### 3. Base de Datos
- **Supabase:** Utilizada como backend de la base de datos para almacenar la información de los jugadores y sus personajes. La tabla principal posee la siguiente estructura:

  | Campo             | Tipo                                 | Descripción                                         |
  |-------------------|--------------------------------------|-----------------------------------------------------|
  | id                | uuid                                 | Identificador único de cada usuario.                |
  | created_at        | timestamp with time zone             | Fecha y hora de creación del usuario.               |
  | username          | character varying                    | Nombre de usuario.                                  |
  | password          | character varying                    | Contraseña del usuario.                             |
  | fuerza            | bigint                               | Valor de fuerza del personaje.                      |
  | armadura          | character varying                    | Información sobre el tipo de armadura.              |
  | PG                | bigint                               | Puntos de Golpe del personaje.                      |
  | XP                | bigint                               | Experiencia acumulada por el personaje.             |
  | nivel             | bigint                               | Nivel actual del personaje.                         |
  | arma              | text                                 | Tipo de arma utilizada por el personaje.            |
  | fisico            | text                                 | Descripción física del personaje.                   |
  | armadura_defensa  | bigint                               | Valor calculado para la defensa de armadura.        |

## Flujo de Datos e Integración

1. **Inicio de la Partida:**
   - El jugador inicia la interacción enviando un mensaje en Telegram.
   - Se activa el workflow de bienvenida en n8n, que gestiona el registro o inicio de sesión validando credenciales en Supabase.

2. **Gestión del Personaje:**
   - Durante la creación o edición, se solicitan datos (nombre, arma, armadura, descripción física) y se generan atributos estáticos (fuerza, armadura_defensa y PG) mediante tiradas de dados.
   - La información se almacena y actualiza en la tabla de Supabase.

3. **Simulación de Combate:**
   - El workflow "Batalla.json" inicia el proceso de combate.
   - Se consulta un microservicio en Heroku (desarrollado en Python) para obtener un monstruo aleatorio y calcular sus estadísticas.
   - GPT-4o genera la narrativa del combate y configura los parámetros de la batalla.
   - Los datos de combate se envían nuevamente a otro microservicio en Heroku para resolver el resultado del enfrentamiento.
   - Dependiendo del resultado (victoria o derrota), se actualiza la experiencia y el nivel del personaje mediante el workflow "Actualizacion_nivel.json".

4. **Notificaciones y Seguimiento:**
   - Al finalizar cada combate, se envían notificaciones a través de Telegram.
   - Se actualiza la base de datos en Supabase con la nueva información del personaje, y se consulta si el jugador desea continuar o finalizar la sesión.

## Despliegue y Escalabilidad

- **Heroku:** Los microservicios escritos en Python se despliegan en Heroku, lo que permite gestionar y escalar cada componente de la simulación de combate y la selección de monstruos de manera independiente.
- **n8n:** Automatiza y orquesta los flujos de trabajo. Permite integrar la lógica de negocio, la gestión de usuarios y la simulación de batallas.
- **Supabase:** Sirve como backend centralizado para el almacenamiento de datos, asegurando la persistencia y consistencia de la información de los jugadores y sus personajes.



