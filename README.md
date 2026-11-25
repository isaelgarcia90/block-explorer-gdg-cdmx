# 🗺️ Block Explorer — Build with Google Maps Platform & AI
**Codelab en vivo · GDG CDMX & Devin Community CDMX**

> 📖 **Codelab oficial de Google:** [Build with Google Maps Platform and AI](https://codelabs.developers.google.com/codelabs/cloud-run/build-with-google-maps-platform-and-ai)

---

## ⚡ Antes de empezar (Prerrequisitos)
1. Abre **[Google AI Studio](https://aistudio.google.com/)** usando una cuenta personal **`@gmail.com`** (las cuentas corporativas o escolares suelen bloquear funciones experimentales y despliegues).
2. Ve al panel **Apps** y haz clic en **New App** (o selecciona la pestaña **Build**).
3. **Sin tarjeta de crédito:** No necesitas configurar facturación en Google Cloud; AI Studio aprovisionará una **Google Maps Platform Demo Key** gratuita (hasta 100 peticiones diarias por API) y desplegará en **Cloud Run Starter Tier**.
4. *(Opcional)* Si deseas seguir la guía paso a paso oficial en inglés o reclamar tu insignia al finalizar, abre el **[Codelab original aquí](https://codelabs.developers.google.com/codelabs/cloud-run/build-with-google-maps-platform-and-ai)**.

---

## 🚀 Prompts del Codelab (Copiar y pegar en orden)

### Paso 1: Cascarón de la aplicación y Geocoding V4
> Al enviar este prompt, aparecerá una tarjeta en el chat llamada **"Set up Google Maps Platform"**. Acepta los términos y haz clic en **Enable Google Maps** para activar tu Demo Key automática.

```text
Build a highly polished, responsive, split-screen application. This application should be called "Block Explorer". It is meant to be a geospatial exploration app. It will use Google Maps Platform.

Key Features & Functionality:

- Split-Screen Layout: On desktop, a 1/3-width left-side editorial control panel and a 2/3-width right-side full-height map.

- Include a search bar where users can type a location such as a city or neighborhood. Submitting should issue a Geocoding request to the Google Geocoding V4 API REST endpoint ([https://geocode.googleapis.com/v4/geocode/address/](https://geocode.googleapis.com/v4/geocode/address/)). Then pan the map to the geocoded location.

- Interactive Google Map: Render the Map inside an interactive container allowing users to pan, drag, and zoom freely. Place customized markers representing the currently selected/geocoded location.

- Also include five buttons for presets the users can click on: Buenos Aires, Shibuya, Copacabana, Cologne, and Lima. Clicking on this should geocode the location and pan the map there.

- Make sure to also include a mechanism to clearly show error messages when they occur so users can easily troubleshoot.
```

---

### Paso 2: Datos curiosos locales con Gemini API (`Local Insights`)
```text
Now, let's bring in the AI using the Gemini API.

1. Once geocoding successfully identifies the City and State (e.g., "Miami, Florida"), trigger a call to the Gemini API (using the gemini-3.8-flash model).

2. Use the following prompt template for the AI:

"You are a local tour guide for [City, State]. Give me exactly 3 short, highly engaging, and unusual or surprising fun facts about this place. Keep each fact under 2 sentences. Format the response as a clean HTML unordered list (<ul>) so I can inject it directly."

3. Display these fun facts in a beautiful "Local Insights" banner on the bottom portion of the map screen. Add a subtle loading spinner while the AI is thinking.

4. Make sure to make this handling robust. If an API request fails, display a user-friendly error notification in the UI.
```

---

### Paso 3: Trivia interactiva generada por IA
```text
Now let's build the actual game! We will have Gemini generate a 3-question quiz based on the real places we just got information about.

After the local insights are generated, show the user a button to "Test their local knowledge". Once clicked, offer them 3 options to choose from:

- Local cuisine
- Art and culture
- Local history

Once selected, generate a 3-question quiz based on the category and the place. The quiz should be multiple choice. Once the user submits the quiz show them their score and the correct answer.
```

---

### Paso 4: Maps Grounding Lite (`Hyperlocal mode`)
> Prueba escribir **`Colonia Condesa, CDMX`** en el buscador y activa este interruptor para generar preguntas basadas en lugares reales alrededor de Campus Wortev.

```text
Add a toggle to make the quiz more difficult. Call this "Hyperlocal mode". When enabled, use the Google Maps Grounding Lite service with the Gemini API to fetch more local data, making the quiz more difficult.

Add information for the user so they know that Hyperlocal Mode is more difficult and how it works.
```

---

## ☁️ Paso 5: Despliegue en Cloud Run y limpieza
1. Haz clic en **Publish** (esquina superior derecha de AI Studio) → **Get Started** → **Publish App** (tarda de 2 a 4 minutos).
2. Al finalizar tus pruebas, entra a la sección **Apps** de AI Studio y haz clic en el ícono de basura de tu proyecto para despublicar el contenedor de Cloud Run.

---

## 🛠️ Solución rápida de problemas (Troubleshooting en vivo)
* **No apareció la tarjeta de "Set up Google Maps Platform":** Ve al ícono de engrane (**Settings**) en AI Studio > pestaña **Integrations** > activa **Google Maps Platform** manualmente.
* **El mapa se ve gris o marca error de clave:** Verifica que no estés usando una cuenta institucional/corporativa (`@empresa.com` o `@escuela.edu.mx`) que bloquee proyectos alojados de Google; cambia a una cuenta `@gmail.com` personal.
* **Error en pantalla al generar los datos o la trivia:** Pídele al chat de AI Studio: *"Fix the API call error shown in the preview and ensure it uses the built-in Gemini SDK and GOOGLE_MAPS_PLATFORM_KEY environment variable"*.
* **El despliegue en Cloud Run tarda más de 4 minutos:** Recarga la pestaña de AI Studio; tu app ya queda guardada en **Apps** y podrás abrir su URL pública desde ahí.

---

## 📚 Recursos y documentación oficial
* **Codelab original:** [Build with Google Maps Platform and AI](https://codelabs.developers.google.com/codelabs/cloud-run/build-with-google-maps-platform-and-ai)
* **Entorno de desarrollo:** [Google AI Studio](https://aistudio.google.com/)
* **Documentación técnica:** [Google Maps Platform](https://developers.google.com/maps/documentation) · [Google Cloud Run](https://cloud.google.com/run/docs)
