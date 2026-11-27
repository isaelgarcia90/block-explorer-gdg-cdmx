# 🗺️ Block Explorer
# Construye con Google Maps Platform e Inteligencia Artificial
**Codelab en vivo · GDG CDMX & Devin Community CDMX**

> 📖 **Codelab oficial de Google:** [Build with Google Maps Platform and AI](https://codelabs.developers.google.com/codelabs/cloud-run/build-with-google-maps-platform-and-ai)

Este repositorio contiene la guía y los prompts actualizados para construir una aplicación web geoespacial interactiva ("Block Explorer") utilizando Google AI Studio, Gemini API y Google Maps Platform.

---

## ⚡ Antes de empezar (Prerrequisitos y Cuentas)

### 1. Validación de Cuentas (Cuentas Personales vs. Institucionales)
*   **Obligatorio:** Usa una cuenta personal **`@gmail.com`** [Crear una cuenta de Gmail](https://accounts.google.com/signup)
*   **Restricción:** Las cuentas de Google Workspace (empresariales, `@empresa.com`) o educativas (`@escuela.edu.mx`) suelen tener políticas de seguridad que bloquean la creación de proyectos en Google Cloud o el uso de AI Studio.

## 2. Créditos Promocionales vs. Nivel Gratuito (Free Tier)

Puedes completar este taller de dos formas:

*   **Opción A (Recomendada para publicar tu app):** Reclama tus créditos patrocinados en los enlaces del evento (válidos el 27/11/2026 de 17:00 a 05:00 hrs):
    *   **Enlace principal:** https://g.dev/credits/gdg-cdmx-6
    *   **Enlace de respaldo:** https://g.dev/credits/gdg-cdmx-6-bkp
    *   **¿Para qué se usan los créditos?** Principalmente para cubrir los costos de infraestructura al desplegar tu aplicación terminada en internet utilizando **Cloud Run**.
    *   ⚠️ **AI Studio, GCP y el "Muro" de la Tarjeta:** Es vital entender que AI Studio y Google Cloud (GCP) manejan la facturación por separado. Al reclamar los créditos en los enlaces, estos se abonan a tu perfil de inmediato. Sin embargo, **esto no elimina mágicamente los límites de cuota (Rate Limits) de Gemini en AI Studio**. Para levantar esos límites o para usar Cloud Run, necesitas vincular tu proyecto a una cuenta de facturación activa en modo "Pay-as-you-go". Es precisamente en ese paso de habilitación donde Google pone el muro y te exige ingresar una tarjeta bancaria (solo como verificación de identidad). El consumo se descontará de tus créditos, no de tu banco.
    *   🎁 **Bono de Bienvenida de $300 USD:** Como dato extra, si es la primera vez que configuras una cuenta de facturación en Google Cloud con tu correo de Gmail, la plataforma te otorgará automáticamente un bono de $300 USD en créditos iniciales (válidos por 90 días). Si decides ingresar tu tarjeta, este saldo se sumará a los créditos del evento, dándote un margen enorme para experimentar sin riesgo de cobros reales.

*   **Opción B (Entorno Local - Sin Tarjeta):** Si no deseas ingresar una tarjeta bancaria hoy, puedes hacer todo el taller usando el entorno gratuito ("Free Option" / Demo Key).
    *   Podrás escribir todo el código, consultar la IA, cargar los mapas y probar el juego funcional dentro de tu editor (manteniéndote en los límites del Free Tier), pero **no podrás publicar la aplicación en internet** al finalizar el taller.

### 3. Entorno de Desarrollo
1.  Entra a **[Google AI Studio](https://aistudio.google.com/)** con tu cuenta personal.
2.  Ve al panel **Apps** y haz clic en **New App** (o selecciona la pestaña **Build**).

---

## 🧠 APIs y Modelos Utilizados

Para entender la arquitectura de nuestra aplicación, dividiremos los servicios en dos categorías:

### 1. Modelos de Gemini (Inteligencia Artificial)
En este taller comprenderemos la diferencia entre usar modelos ligeros para evitar costos y modelos pesados para producción:

*   **`gemini-3.1-flash-lite` (Entorno de Taller - Free Tier):** Es el modelo que indicaremos en nuestros prompts para que la aplicación genere los datos curiosos y el quiz. Lo usamos porque es extremadamente rápido y tiene límites muy generosos en la capa gratuita (15 millones de tokens diarios). Esto garantiza que el servidor no colapse por límites de peticiones (*Rate Limits*) mientras todos los asistentes prueban su app al mismo tiempo.
*   **`gemini-3.8-flash` (Entorno de Producción - Demo Oficial):** Es el modelo de gama alta sugerido en el codelab original de Google. Tiene capacidades de razonamiento superior, respeta el formato JSON a la perfección y genera trivias mucho más creativas. *Nota:* El ponente utilizará este modelo durante la demostración en vivo, ya que requiere una cuenta de Google Cloud con facturación activa (como el bono de bienvenida de $300 USD) para soportar las cuotas de red.
*   **`Gemini 4 Hydrogen` (El programador predeterminado):** Es el agente experto interno que lee tus instrucciones en AI Studio y programa la aplicación. Déjalo como predeterminado, pero si agotas sus usos diarios, cambia a **`Gemini Flash Latest`** en los ajustes del chat.

### 2. Google Maps Platform (Servicios Geoespaciales)
*   **Maps JavaScript API:** El núcleo del frontend. Se encarga de renderizar el mapa interactivo en la pantalla, gestionar el zoom, el desplazamiento (panning) y colocar los marcadores visuales (pines) en la interfaz.
*   **Geocoding API (V4):** Traduce direcciones legibles por humanos (ej. "Colonia Condesa, CDMX") a coordenadas geográficas exactas (Latitud y Longitud) para que el mapa sepa dónde centrarse y la IA sepa de dónde buscar datos.
*   **Places API (New):** Utilizada en el Nivel Avanzado (Arquitectura RAG). Busca negocios reales, museos, restaurantes y parques en un radio métrico exacto (ej. 1000 metros) para alimentar a Gemini con datos hiperlocales antes de que genere el quiz.

---

## 🚀 Prompts del Codelab (Depurados y Blindados)

*Copia y pega estos prompts en orden en el chat de AI Studio. Han sido optimizados para prevenir errores de cuota, "alucinaciones" y tiempos de espera.*

### Paso 1: Cascarón de la aplicación y Geocoding V4
> 💡 **Nota Importante:** Al enviar este prompt, aparecerá una tarjeta en el chat llamada **"Set up Google Maps Platform"**. Acepta los términos, haz clic en **Enable Google Maps** y **selecciona obligatoriamente la "Free Option" (Demo Key)**.
>
> *   **¿Por qué elegir "Free Option"?** AI Studio te proporcionará una clave de demostración instantánea que funcionará perfectamente para renderizar el mapa interactivo durante todo el taller, sin salir del editor y con cero fricciones.
> *   **¿Por qué NO elegir "Full Access"?** Porque aunque hayas reclamado tus créditos promocionales del evento exitosamente, la opción de Full Access te pedirá ingresar los datos de una tarjeta de crédito o débito para verificar tu cuenta de facturación de Google Cloud. Para evitar usar tarjetas bancarias y agilizar el taller, usaremos exclusivamente la Free Option.
>
> 💡 **Adaptación del Codelab Original:** Este prompt está diseñado para construir el frontend de la aplicación llamada "Block Explorer". Sin embargo, **hemos modificado el prompt oficial de Google** agregando soporte bilingüe (ES/EN) desde la petición a la IA.

```text
Build a highly polished, responsive, split-screen application. This application should be called "Block Explorer". It is meant to be a geospatial exploration app. It will use Google Maps Platform.

Key Features & Functionality:

- Split-Screen Layout: On desktop, a 1/3-width left-side editorial control panel and a 2/3-width right-side full-height map.

- Include a search bar where users can type a location such as a city or neighborhood. Submitting should issue a Geocoding request to the Google Geocoding V4 API REST endpoint (https://geocode.googleapis.com/v4/geocode/address/). Then pan the map to the geocoded location.

- Interactive Google Map: Render the Map inside an interactive container allowing users to pan, drag, and zoom freely. Place customized markers representing the currently selected/geocoded location.

- Also include five buttons for presets the users can click on: Buenos Aires, Shibuya, Copacabana, Cologne, and Lima. Clicking on this should geocode the location and pan the map there.

- Make sure to also include a mechanism to clearly show error messages when they occur so users can easily troubleshoot.

- Bilingual Support (ES / EN): Add a clean language toggle button ("ES | EN") in the top header of the control panel (defaulting to Spanish) stored in global state so all UI labels, placeholders, error messages, and future AI-generated content switch dynamically between Spanish and English.
```

### Paso 2: Datos curiosos locales con Gemini API (`Local Insights`)

> 💡 **Adaptación del Codelab Original:** Este prompt conecta la aplicación con Gemini para generar los "Fun Facts". Hemos **modificado el prompt oficial de Google (Puntos 1, 2, 4 y 5)** para adaptar el modelo y agregar un blindaje técnico estricto:
> 
> *   **Cambio de Modelo (Punto 1):** Cambiamos la instrucción de usar `gemini-3.8-flash` a `gemini-3.1-flash-lite`. Aunque ya lo explicamos al inicio, forzar este cambio directo en el prompt asegura que AI Studio no intente usar el modelo pesado por defecto, evitando así los bloqueos por *rate limits* en las cuentas gratuitas.
> *   **Parseo de HTML y Regex (Puntos 2 y 4):** El modelo original a veces devuelve el código HTML envuelto en bloques de markdown (```html). AI Studio solía intentar resolver esto creando expresiones regulares complejas que terminaban rompiendo la aplicación y dejando la pantalla en blanco. Ahora instruimos explícitamente al asistente para que limpie el texto sin usar *regex* estrictos.
> *   **Protección de Cuota (Punto 4):** Prohibimos los bucles de reintento automático (`retry loops`) y el uso de `thinkingConfig`. Si la app falla, fallará rápido y mostrará un error, en lugar de intentar hacer la petición 5 veces seguidas y agotar el límite de tu API de golpe.
> *   **Soporte Bilingüe (Punto 5):** Agregamos lógica para que el prompt enviado a Gemini respete el estado global de idioma (ES/EN) configurado en el Paso 1.

```text
Now, let's bring in the AI using the Gemini API.

1. Once geocoding successfully identifies the City and State (e.g., "Miami, Florida"), trigger a call to the Gemini API (using the gemini-3.1-flash-lite model).

2. Use the following prompt template for the AI:
"You are a local tour guide for [City, State]. Give me exactly 3 short, highly engaging, and unusual or surprising fun facts about this place. Keep each fact under 2 sentences. Format the response strictly as a raw HTML unordered list (<ul>) without any markdown code fences or backticks so I can inject it directly."

3. Display these fun facts in a beautiful "Local Insights" banner on the bottom portion of the map screen. Add a subtle loading spinner while the AI is thinking.

4. Make sure to make this handling robust, but follow these strict server rules to conserve quota and prevent silent errors: 
- DO NOT implement automatic retry loops (make exactly 1 API call per request).
- Set a 30-second timeout for the Gemini API request.
- Set maxOutputTokens to 2048.
- DO NOT include any thinkingConfig or reasoning parameters in the API call.
- Do NOT use strict regex to validate the HTML output; simply read response.text, strip any accidental markdown fences if present, and send it to the frontend to inject.
- If an API request fails, display a user-friendly error notification in the UI.

5. Bilingual Support (ES / EN): Make sure the prompt sent to Gemini instructs it to write the 3 fun facts in Spanish when "ES" is active and in English when "EN" is active, and translate the "Local Insights" banner title, loading state, and error notifications accordingly.
```

### Paso 3: Trivia interactiva generada por IA

> 💡 **Adaptación del Codelab Original:** Este prompt le indica al asistente que construya la lógica del juego. **El único cambio que le hicimos al prompt oficial de Google** fue agregar la instrucción final:
> 
> *   **Soporte Bilingüe:** Le indicamos a la IA que conecte las categorías, los textos de la interfaz y las preguntas generadas al estado global de idioma (ES/EN) que configuramos en el Paso 1, para que todo el juego se traduzca dinámicamente.

```text
Now let's build the actual game! We will have Gemini generate a 3-question quiz based on the real places we just got information about.

After the local insights are generated, show the user a button to "Test their local knowledge". Once clicked, offer them 3 options to choose from:
- Local cuisine
- Art and culture
- Local history

Once selected, generate a 3-question quiz based on the category and the place. The quiz should be multiple choice. Once the user submits the quiz show them their score and the correct answer.

Bilingual Support (ES / EN): Localize all quiz UI labels, categories, and Gemini-generated questions/answers so they dynamically match the active "ES | EN" language toggle.
```

### Paso 4: El Reto del Grounding (`Hyperlocal mode` Básico)

> 💡 **Adaptación del Codelab Original:** Este prompt activa la herramienta interna de Gemini para anclarse a datos reales de Google (Grounding). **Hemos modificado el prompt oficial de Google** para inyectar nuestro soporte de idioma y exponer un reto técnico crucial en la IA:
> 
> *   **Soporte Bilingüe:** Agregamos la instrucción para que la etiqueta del interruptor ("Modo Hiperlocal"), su texto explicativo y las nuevas preguntas generadas respeten el estado global de idioma (ES/EN).
> *   **🚨 La "Alucinación Geográfica" (El problema):** Notarás que aunque activemos el *Grounding*, el modelo puede arrojarte resultados a varios kilómetros de distancia del lugar que se busca explorar. Esto ocurre porque los LLMs no son mapas, buscan texto (semántica) en lugar de medir distancias físicas (geometría). Si buscas una calle residencial o poco turística, la IA no encontrará artículos de trivia al respecto; para no fallar, expandirá silenciosamente su radio de búsqueda hasta encontrar lugares famosos, priorizando darte un dato real sobre respetar lugares cercanos a tu lugar de interés.

```text
Add a toggle to make the quiz more difficult. Call this "Hyperlocal mode". When enabled, use the Google Maps Grounding Lite service with the Gemini API to fetch more local data, making the quiz more difficult.

Add information for the user so they know that Hyperlocal Mode is more difficult and how it works.

Bilingual Support (ES / EN): Localize the "Hyperlocal mode" toggle label, its explanatory info text, and the resulting hyperlocal quiz questions and answers so they dynamically match the active "ES | EN" language toggle (in Spanish when "ES" is active and in English when "EN" is active).
```

### Paso 5: Arquitectura RAG (Nivel Avanzado) 🚀 PLUS

> 💡 **Adaptación del Codelab Original:** Este prompt es una **mejora arquitectónica completa** sobre el tutorial oficial. En lugar de depender del "Grounding" interno de Gemini (que demostró fallar en distancias cortas en el paso anterior), implementamos un patrón RAG (Retrieval-Augmented Generation) para garantizar precisión absoluta:
> 
> *   **Búsqueda Espacial Real (Puntos 1, 2 y 3):** Obligamos al servidor a consultar primero la API de Places (New) de Google Maps con un radio estricto de 1000 metros exactos desde la ubicación del usuario.
> *   **Eliminación de Alucinaciones (Punto 5):** Le pasamos a Gemini los nombres exactos de los negocios locales encontrados para que genere la trivia basándose *solo* en ellos, resolviendo el problema de la desviación kilométrica.
> *   **Enlaces Interactivos de Maps (Puntos 3, 4 y 6):** Modificamos el `FieldMask` para extraer no solo el nombre del lugar, sino también su `googleMapsUri`. Con esto, obligamos al frontend a dibujar enlaces reales y clicables en la interfaz, mejorando drásticamente la UX del taller.
> *   **Protección del Free Tier:** Seguimos forzando el uso de `gemini-3.1-flash-lite` para asegurar que esta consulta avanzada de múltiples pasos no colapse por límites de cuota.

```text
Now, let's fix the geographic deviation and make Hyperlocal Mode strictly precise by implementing a Retrieval-Augmented Generation (RAG) pattern. 

Update the quiz endpoint in `server.ts` to fetch real places from Google Maps BEFORE asking Gemini to write the quiz. Maintain all existing quota protections (gemini-3.1-flash-lite, no thinkingConfig).

1. When the frontend sends `hyperlocal: true`, the server must first fetch real nearby places using the Places API (New) `searchNearby` REST endpoint (https://places.googleapis.com/v1/places:searchNearby).
2. Map the quiz category to Places API `includedTypes`: 
  - 'Local cuisine' -> ['restaurant', 'cafe']
  - 'Art and culture' -> ['museum', 'art_gallery']
  - 'Local history' -> ['historical_landmark', 'tourist_attraction']
3. The Places API payload must center strictly on the provided `latitude` and `longitude` with a `radius` of 1000.0 meters. Pass the server's existing Maps API key in the `X-Goog-Api-Key` header and exactly `places.displayName,places.googleMapsUri` in the `X-Goog-FieldMask` header.
4. Extract the top 3 places from the JSON response, specifically grabbing both the `displayName` and the `googleMapsUri` for each.
5. THEN, call Gemini with `responseMimeType: "application/json"` and this exact prompt: "Generate a 3-question multiple-choice quiz based STRICTLY on these specific local places: [Insert the extracted Place Names]. Do not invent places. Generate the quiz in [ES or EN based on active language]."
6. IMPORTANT: Ensure the final JSON response sent to the frontend includes an array containing the names and `googleMapsUri` of the 3 places used, so the React frontend can render them as clickable external links in the "Google Maps Sources" footer section.
7. If `hyperlocal` is false, keep using the standard geographic quiz prompt we built earlier.
```

---

## 🛠️ Solución de Problemas Frecuentes (Troubleshooting)

1.  **Error `EMPTY_RESPONSE` o textos en blanco al cambiar de ciudad:**
    *   *Causa:* Si le das muchos clics rápidos ("spam") a las categorías, chocarás con el límite de **N peticiones por minuto (RPM)** de la capa gratuita.
    *   *Solución:* Espera 30 segundos a que se reinicie tu cuota por minuto y pulsa "Reintentar".
2.  **Mensaje: "Upgrade to continue your flow" en AI Studio:**
    *   *Causa:* Agotaste el límite diario de interacciones con el asistente de código experimental (ej. Gemini 4 Hydrogen).
    *   *Solución:* En la esquina inferior derecha del chat, haz clic en el menú desplegable de modelos y cambia a **`Gemini Flash Latest`** para seguir editando gratis.
3.  **Error 404 (Modelo no encontrado):**
    *   *Causa:* Usaste un modelo deprecado como `gemini-1.5-flash` o `gemini-2.5-flash` o un modelo inventado (`3.8`). 
    *   *Solución:* Asegúrate de que el archivo `server.ts` siempre declare el modelo como `'gemini-3.1-flash-lite'`.
4.  **Error 400 (Invalid Argument / Timeout):**
    *   *Causa:* Google Cloud rechaza peticiones con límites de tiempo menores a 10 segundos. 
    *   *Solución:* Nuestros prompts ya incluyen la regla estricta de *"Set a 30-second timeout"* para evitar esto.
5.  **El mapa se ve gris o marca error de clave:**
    *   Ve al ícono de engrane (**Settings**) en AI Studio > **Integrations** > y activa el switch de **Google Maps Platform** manualmente.

---

## ☁️ Despliegue en Cloud Run y limpieza

1. Haz clic en **Publish** (esquina superior derecha de AI Studio) → **Get Started** → **Publish App** (tarda de 2 a 4 minutos).
2. Si tarda más de 4 minutos, simplemente recarga la pestaña; tu app ya quedó guardada en el panel izquierdo (**Apps**) y podrás abrir su URL pública desde ahí.
3. Al finalizar, puedes ir a la sección **Apps** y hacer clic en el ícono de basura para despublicar el contenedor.

---
# 🌍 Block Explorer Live Demo

> 🚀 **¡Prueba la aplicación terminada!**
>
> 🔗 **[blockexplorer.ai.studio](https://blockexplorer.ai.studio/)**
>
> 💡 **Nota:** El despliegue de este demo se implementó utilizando una cuenta facturada (Tier 1) ligada a una cuenta personal gmail con **bono de bienvenida de $300 USD en GCP**.

---

## 📚 Documentación Oficial y Referencias
- **Entorno de desarrollo:** [Google AI Studio](https://aistudio.google.com/)
- **Consola de Google Cloud:** [Google Cloud Console](https://console.cloud.google.com/)
- **Documentación técnica:** [Google Maps Platform (Geocoding / Places)](https://developers.google.com/maps/documentation) · [Gemini API Docs](https://ai.google.dev/docs)
