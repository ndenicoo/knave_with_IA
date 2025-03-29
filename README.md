# IA Game Master para Knave

Este proyecto implementa un **Game Master (GM) basado en IA** para el juego de rol **Knave**. La IA se encarga de narrar la historia, gestionar los combates y actualizar los personajes, permitiendo a los jugadores disfrutar de una experiencia inmersiva sin necesidad de intervención manual.

El sistema está automatizado mediante **n8n**, utiliza **GPT-4o** para la narración, **Supabase** como base de datos y un microservicio en **Heroku** para la gestión de monstruos y batallas.

---

## 📌 Inicio de la Partida

Para comenzar, el jugador escribe cualquier mensaje en el chat.

Al activarse el workflow de bienvenida, se permite:
- **Registrar un nuevo usuario.**
- **Iniciar sesión en una cuenta existente.**

---

## 🛡️ Creación y Gestión de Personajes

Para facilitar la implementación, solo se ha conservado el atributo de **Fuerza** en la mecánica de juego.

### 📍 Atributos del Personaje

Los personajes tienen dos tipos de atributos:

#### 🔹 Dinámicos (Modificables por el jugador):
- **Nombre**
- **Arma**
- **Armadura**
- **Descripción física**

Estos atributos sirven solo para enriquecer la narración del GM.

#### 🔹 Estáticos (Definen las capacidades del personaje y no se pueden modificar):
- **Bonus de Fuerza:** Se obtiene lanzando 3d6 y tomando el menor valor.
- **Defensa de Armadura:** Se calcula como (menor de 3d6) + 10.
- **Puntos de Golpe (PG):** Se generan con 1d8.

### 📌 Reglas de gestión del personaje:
- ✅ Se puede editar solo los atributos dinámicos.
- ✅ Al eliminar un personaje, se crea automáticamente uno nuevo.
- ✅ Cada jugador solo puede tener un personaje activo.

---

## ⚔️ Sistema de Batalla

Las batallas son completamente narradas por la IA, sin intervención del jugador, para enfatizar su rol como espectador de la historia.

### 📍 Mecánica del Combate

1. **Selección del Enemigo**
   - Un microservicio en Heroku elige aleatoriamente un monstruo.
   - El GM ajusta sus atributos para equilibrar la batalla.

2. **Orden de Ataque**
   - Se lanza 1d6. Si el resultado es ≤3, comienzan los monstruos; de lo contrario, ataca el jugador.

3. **Ataques y Defensa**
   - Se lanza 1d20 + Bonus de Ataque. Si el resultado supera la defensa del enemigo, se hace daño.
   - El daño es de 1d6 para el jugador o el daño base del monstruo.

4. **Fin del Combate**
   - Las rondas continúan hasta que uno de los dos queda sin PG.
   - No hay moralidad, críticos ni opciones como huir o lanzar hechizos.

### 📌 Resolución del Combate

- Un microservicio en Heroku simula la batalla.
- La IA narra el inicio y desenlace del combate.

---

## 🏆 Sistema de Niveles y Experiencia

El sistema de niveles se basa en la acumulación de **XP** tras cada combate.

### 🔹 Experiencia Necesaria para Subir de Nivel:
- **Nivel 1:** 50 XP
- **Nivel 2:** 60 XP
- **Nivel 3:** 70 XP  
  *(Se incrementa en +10 XP por nivel).*

### 🔹 Beneficios al Subir de Nivel:
- ✅ Ganas 50 XP por victoria.
- ✅ Se suma +1 a **Fuerza**, **Defensa de Armadura** o **PG**. *(Máximo de 20 puntos por atributo).*
- ✅ Tras el combate, el jugador puede elegir jugar de nuevo o salir.

---

## ⚙️ Workflows de n8n

### 📌 1. Actualizacion_nivel.json

**Propósito:** Actualizar el nivel y los atributos del personaje tras una victoria.

**✅ Flujo:**
- **Trigger:** Se ejecuta desde el workflow Batalla.json.
- **Cálculo de Nivel:**
  - Suma 50 XP al personaje.
  - Sube de nivel si la XP supera el umbral correspondiente.
  - Incrementa Fuerza, Armadura y PG (máx. 20).
- **Base de Datos:**
  - Actualiza los valores en Supabase.
- **Notificación:**
  - Envía un mensaje por Telegram con los atributos actualizados.
  - Pregunta si el jugador quiere jugar otra batalla.

---

### 📌 2. Batalla.json

**Propósito:** Simular un combate entre el personaje y un monstruo.

**✅ Flujo:**
- **Obtener Monstruo:**
  - Consulta un API en Heroku para obtener un monstruo aleatorio.
  - Calcula sus estadísticas (PG, defensa, ataque).
- **Narración:**
  - GPT-4 genera una descripción épica del combate.
  - Se configuran los parámetros de batalla.
- **Simulación:**
  - Se envían los datos a un API en Heroku para resolver el combate.
  - Se muestra el resultado (victoria/derrota) y un log de la batalla en Telegram.
- **Post-Combate:**
  - Si el jugador gana, se ejecuta **Actualizacion_nivel.json**.

---

### 📌 3. Creacion_Edicion_Borrado_personajes_v2.json

**Propósito:** Gestionar la creación, edición y eliminación de personajes.

**✅ Flujo:**
- **Creación:**
  - Solicita datos mediante Telegram.
  - Usa GPT-4 para generar una historia y atributos aleatorios.
  - Guarda la información en Supabase.
- **Edición/Eliminación:**
  - Permite editar atributos dinámicos o eliminar el personaje.
  - Actualiza o borra registros en Supabase.
- **Redirección:**
  - Pregunta si el jugador desea iniciar una batalla o seguir editando.

---

### 📌 4. Bienvenida.json

**Propósito:** Manejar el registro, inicio de sesión y la introducción al juego.

**✅ Flujo:**
- **Inicio:**
  - Pregunta si el usuario quiere registrarse o iniciar sesión.
- **Registro:**
  - Solicita usuario y contraseña.
  - Guarda los datos en Supabase.
  - Envía un mensaje de bienvenida generado por GPT-4.
  - Redirige a la creación de personaje.
- **Inicio de Sesión:**
  - Valida credenciales en Supabase.
  - Si son correctas, el usuario puede iniciar una batalla o editar su personaje.

---

## 🛠️ Tecnologías Utilizadas

- ✅ **n8n** → Automatización de workflows.
- ✅ **GPT-4** → Generación de narraciones épicas.
- ✅ **Supabase** → Base de datos para almacenar información de los jugadores.
- ✅ **Heroku** → Microservicio para selección de monstruos y simulación de batallas.
- ✅ **Telegram** → Interfaz de comunicación con los jugadores.

---

## 🚀 Conclusión

Este sistema de **IA Game Master para Knave** proporciona una experiencia totalmente automatizada y narrada, permitiendo a los jugadores disfrutar del juego sin necesidad de intervención manual en los combates.

