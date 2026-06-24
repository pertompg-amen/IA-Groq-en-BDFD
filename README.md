# 🤖 BDFD AI Chatbot (Powered by [Groq](http://console.groq.com/))

Un comando avanzado y optimizado para **Bot Designer For Discord (BDFD)** que integra Inteligencia Artificial de código abierto utilizando la infraestructura de velocidad ultra rápida de **[Groq](http://console.groq.com/)**.

---

## 📋 Índice
* [Características](#-características)
* [Código del Comando](#-código-del-comando)
* [Instalación y Configuración](#%EF%B8%8F-instalación-y-configuración)
* [¿Dónde conseguir la API Key?](#-dónde-conseguir-la-api-key)
* [Estructura de la API (JSON)](#-estructura-de-la-api-json)
* [Fuentes y Documentación](#-fuentes-y-documentación)

---

## ✨ Características

* **Modelos Open Source:** Configurado por defecto con `llama-3.1-8b-instant`, un modelo de Meta potente, enfocado en programación y respuestas concisas.
* **Sistema Anti-Fallos:** Implementa control de estados HTTP (`$httpStatus`) para interceptar errores comunes como límites de cuota (`429`) o configuraciones inválidas (`401`) antes de que rompan el bot.
* **Validación Automática:** Comprueba la existencia de la variable local en el bot antes de realizar cualquier petición externa.
* **Diseño Limpio:** Respuestas automáticas encapsuladas dentro de elementos Embed interactivos y organizados con colores dinámicos según el estado.

---

## 💻 Código del Comando

Crea un nuevo comando en tu panel de BDFD, selecciona el disparador (trigger) de tu preferencia (por ejemplo: `!ia <texto>`) y pega el siguiente código:

```rb
$nomention
$var[model;llama-3.3-70b-versatile]

$if[$varExists[APIKEY]==true]
$httpAddHeader[Authorization;Bearer $getVar[APIKEY]]
$httpAddHeader[Content-Type;application/json]
$httpPost[https://api.groq.com/openai/v1/chat/completions;{
  "model": "$var[model]",
  "messages": [
    {
      "role": "system",
      "content": "Eres un bot de Discord conciso que sabe programar y dar buena vibra."
    },
    {
      "role": "user",
      "content": "$message"
    }
  \]
}]

$if[$httpStatus==200]
$botTyping
$description[$httpResult[choices;0;message;content]]
$footer[Modelo utilizado: $var[model]]
$color[00ff00]
$elseif[$httpStatus==429]
$description[⚠️ Has superado el límite de peticiones por minuto de Groq. Por favor, espera un momento antes de volver a intentarlo.]
$color[ffaa00]
$elseif[$or[$httpStatus==500;$httpStatus==503]==true]
$description[❌ Hubo un problema al procesar la petición con la IA.**Código de Estado HTTP:** `$httpStatus`]
$color[ff0000]
$endif

$else 
$description[⛔️ Crea una variable llamada ``APIKEY`` y pega tu APIKEY que conseguiste en [groq\](https://console.groq.com/)]
$color[ff0000]
$endif
```

## 🛠️ Instalación y Configuración
 1. **Crear la Variable:** En el panel de control de tu bot en BDFD, dirígete a la sección de **Variables**.
 2. **Asignar Nombre:** Crea una variable llamada exactamente `APIKEY`.
 3. **Guardar Valor:** Coloca tu clave secreta de Groq (debe empezar con gsk_) en el valor por defecto de la variable o asígnatela internamente.
## 🔑 ¿Dónde conseguir la API Key?
La API Key utilizada en este proyecto se obtiene de forma **100% gratuita** siguiendo estos pasos:
 1. Ve al sitio oficial de desarrolladores de [Groq](http://console.groq.com/).
 2. Inicia sesión o regístrate utilizando una cuenta de Google o GitHub.
 3. En el menú de navegación de la derecha, haz clic en la sección **API Keys**.
 4. Presiona el botón **"Create API Key"**, asígnale un nombre identificativo (ej. ia-for-BDFD) y copia el token generado.
> [!IMPORTANT]
> Mantén este token en completa privacidad. Cualquiera con acceso al token podría consumir tus cuotas de peticiones.
> 
## 🗂️ Estructura de la API (JSON)
El comando se comunica de forma nativa enviando y recibiendo estructuras JSON estandarizadas.
### Petición Enviada (Request Body)
Se realiza un método POST al endpoint de chat completions inyectando las siguientes variables:
 * model: ID del LLM open-source alojado en los servidores.
 * messages: El hilo conversacional dividido en roles (system para directrices de personalidad y user para la entrada de datos de Discord).
### Respuesta Esperada (Response Body)
[Groq](http://console.groq.com/) devuelve un mapa de objetos indexados del cual nuestro código extrae el string del mensaje final utilizando la ruta estructurada de BDFD:
```text
choices ➔ 0 (Primer elemento) ➔ message ➔ content

```
## 📚 Fuentes y Documentación
Para el desarrollo de este comando se utilizaron las especificaciones de las siguientes documentaciones oficiales:
 * **[Bot Designer For Discord Wiki](https://wiki.botdesignerdiscord.com/guides/general/httpRequests.html):** Guía oficial de HTTP Requests en BDFD donde se detallan las normas de uso para $httpPost, $httpAddHeader y $httpResult.
 * **[Groq](http://console.groq.com/) API Reference:** Documentación para Desarrolladores de Groq para las especificaciones de endpoints compatibles con OpenAI, esquemas de cabeceras de autorización Bearer y listado de modelos disponibles.

A continuación tienes la lista con los modelos, divididos por su categoría.
Cada uno de ellos tiene un ID específico

> [!IMPORTANT]
> Que es el texto que debes colocar exactamente en el campo "model" de tu JSON en BDFD usando la `$var` Model que deje al inicio

> 
### 1. Modelos de Texto Principales (Producción y Uso General)
Estos son los modelos estándar más estables, eficientes y recomendados para bots de charla.
 * **llama-3.1-8b-instant**
   * **Desarrollador:** Meta
   * **Descripción:** El modelo de velocidad ultrarrápida (instantánea) de Meta. Ofrece una ventana de contexto de 128k tokens. Ideal para bots conversacionales extremadamente fluidos y respuestas rápidas de comandos.
 * **llama-3.3-70b-versatile**
   * **Desarrollador:** Meta
   * **Descripción:** Un modelo de alta gama inteligente y versátil con 128k de contexto. Excelente para razonamiento lógico complejo, programación, traducción avanzada y el cumplimiento estricto de instrucciones estructuradas.
 * **openai/gpt-oss-120b** / **openai/gpt-oss-20b**
   * **Desarrollador:** OpenAI (Modelos Abiertos / GPT OSS)
   * **Descripción:** Modelos abiertos integrados en la plataforma Groq que destacan por su gran velocidad de respuesta y equilibrio en tareas de uso general.
### 2. Modelos Especializados en Audio (Transcripción)
Si tu bot necesita procesar archivos de voz, notas de audio o comandos hablados para convertirlos a texto:
 * **whisper-large-v3** / **whisper-large-v3-turbo**
   * **Desarrollador:** OpenAI
   * **Descripción:** Modelos líderes en reconocimiento automático del habla (STT). La versión *turbo* ofrece una velocidad de procesamiento masiva sacrificando una cantidad mínima de precisión.
> [!NOTE]
> Este modelo no lo e probado, pero según tengo entendido es como si usaras ChatGPT en llamada, cosa que en BDFD es imposible sin API :/
