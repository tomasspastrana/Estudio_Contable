# Configuración de EmailJS — Aura Contable SAS

Este documento describe cómo se integró EmailJS en el sitio para enviar los formularios de **Contacto** y **Encuesta de Satisfacción** por correo electrónico.

---

## ¿Qué es EmailJS?

[EmailJS](https://www.emailjs.com/) es un servicio que permite enviar emails directamente desde el navegador (sin backend). El plan gratuito incluye **200 emails/mes**, más que suficiente para el volumen esperado.

---

## Cambios realizados en `index.html`

### 1. SDK de EmailJS (en `<head>`)

Se agregó el script del SDK y la inicialización con la Public Key:

```html
<script src="https://cdn.jsdelivr.net/npm/@emailjs/browser@4/dist/email.min.js"></script>
<script>
  (function () {
    emailjs.init("TU_PUBLIC_KEY");
  })();
</script>
```

### 2. Atributos `name` en los campos del formulario de contacto

Se agregaron atributos `name` a cada input/select/textarea para que EmailJS pueda leer los valores:

| Campo                    | `name`         |
|--------------------------|----------------|
| Nombre                   | `nombre`       |
| Apellido                 | `apellido`     |
| Correo Electrónico       | `email`        |
| Teléfono / WhatsApp      | `telefono`     |
| Razón Social / Empresa   | `empresa`      |
| Tipo de Empresa          | `tipo_empresa` |
| Servicio de Interés      | `servicio`     |
| Descripción de Consulta  | `mensaje`      |

### 3. Atributo `name` en la encuesta

Se agregó `name="sugerencia"` al textarea de sugerencias. Las preguntas de estrellas se leen desde `data-v` y las radio buttons ya tenían `name`.

### 4. Funciones JavaScript actualizadas

- **`enviarContacto()`** — Usa `emailjs.sendForm()` que envía automáticamente todos los campos con `name` del formulario.
- **`enviarEncuesta()`** — Usa `emailjs.send()` con un objeto de parámetros construido manualmente (porque las estrellas usan `data-v`, no inputs convencionales).

Ambas funciones ahora:
- Muestran "⏳ Enviando..." en el botón mientras se procesa
- Muestran el mensaje de éxito al completarse
- Muestran una alerta y reactivan el botón si hay un error

---

## Pasos para activar el envío de emails

### Paso 1: Crear cuenta en EmailJS

1. Ir a [https://www.emailjs.com/](https://www.emailjs.com/) y crear una cuenta gratuita.

### Paso 2: Conectar servicio de email

1. En el dashboard, ir a **Email Services** → **Add New Service**.
2. Elegir el proveedor (Gmail, Outlook, etc.) y conectar la cuenta donde se recibirán los emails.
3. Copiar el **Service ID** (ej: `service_abc123`).

### Paso 3: Crear template para el formulario de contacto

1. Ir a **Email Templates** → **Create New Template**.
2. Usar estas variables en el cuerpo del template:

```
Nueva consulta de: {{nombre}} {{apellido}}
Email: {{email}}
Teléfono: {{telefono}}
Empresa: {{empresa}}
Tipo de empresa: {{tipo_empresa}}
Servicio solicitado: {{servicio}}

Mensaje:
{{mensaje}}
```

3. Copiar el **Template ID** (ej: `template_contacto`).

### Paso 4: Crear template para la encuesta

1. Crear otro template con estas variables:

```
Resultados de Encuesta de Satisfacción

1. Calidad técnica: {{calidad_tecnica}}/5
2. Comunicación del equipo: {{comunicacion_equipo}}/5
3. Plazo de respuesta: {{plazo_respuesta}}
4. Ajuste a necesidades: {{ajuste_necesidades}}
5. Información clara: {{info_clara}}/5
6. Aspecto más valorado: {{aspecto_valorado}}
7. Recomendación: {{recomendacion}}/5
8. Sugerencias: {{sugerencia}}
```

2. Copiar el **Template ID** (ej: `template_encuesta`).

### Paso 5: Obtener la Public Key

1. Ir a **Account** → **API Keys**.
2. Copiar la **Public Key**.

### Paso 6: Reemplazar los placeholders en `index.html`

Buscar y reemplazar estos tres valores:

| Placeholder                  | Reemplazar con               | Ubicación                      |
|------------------------------|------------------------------|--------------------------------|
| `TU_PUBLIC_KEY`              | Tu Public Key de EmailJS     | `<head>` (línea de `emailjs.init`) |
| `TU_SERVICE_ID`              | Tu Service ID                | `<script>` (constante JS)      |
| `TU_TEMPLATE_CONTACTO`      | Template ID del contacto     | `<script>` (constante JS)      |
| `TU_TEMPLATE_ENCUESTA`      | Template ID de la encuesta   | `<script>` (constante JS)      |

---

## Parámetros enviados por cada formulario

### Formulario de Contacto (`emailjs.sendForm`)

Se envían automáticamente todos los campos con atributo `name`:
`nombre`, `apellido`, `email`, `telefono`, `empresa`, `tipo_empresa`, `servicio`, `mensaje`

### Encuesta de Satisfacción (`emailjs.send`)

Se envía un objeto con estos parámetros:

| Parámetro             | Contenido                                           |
|-----------------------|-----------------------------------------------------|
| `calidad_tecnica`     | Estrellas (1-5) — Pregunta 1                        |
| `comunicacion_equipo` | Estrellas (1-5) — Pregunta 2                        |
| `plazo_respuesta`     | Radio: mismo-dia / 24hs / 48hs / mas — Pregunta 3  |
| `ajuste_necesidades`  | Radio: si / mayormente / parcialmente / no — Preg 4 |
| `info_clara`          | Estrellas (1-5) — Pregunta 5                        |
| `aspecto_valorado`    | Radio: tecnico / comunicacion / digital / precio — Preg 6 |
| `recomendacion`       | Estrellas (1-5) — Pregunta 7                        |
| `sugerencia`          | Texto libre — Pregunta 8                            |
