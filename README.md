# Uceva Pipeline App Path

Segunda aplicación del laboratorio de Pipelines de Arquitectura de Software (UCEVA). Está hecha con Angular, tiene tres vistas y se publica automáticamente en GitHub Pages con un workflow de GitHub Actions.

Página publicada: https://santiagocruz09.github.io/Uceva-Pipeline-App-Path/

Integrantes: Santiago Santacruz Cuellar y Felipe Marulanda Castro.

## Qué tiene la aplicación

Tres páginas navegables desde un menú: Inicio, Acerca de y Contacto. Cada una se carga con `loadComponent`, así que Angular descarga su código solo cuando se visita.

Las rutas son `/home`, `/about` y `/contact`. La ruta vacía y cualquier ruta desconocida redirigen a `/home`.

## Estrategia de enrutamiento

Esta aplicación usa Path Location Strategy, que es la que Angular trae por defecto. Se configura con `provideRouter(routes)` en `src/app/app.config.ts`, sin `withHashLocation()`, y por eso las direcciones no llevan el carácter `#`.

La primera aplicación del laboratorio (https://github.com/Felipe-Marulanda/Uceva-Pipeline-App) hace lo contrario: usa `withHashLocation()` y sus direcciones se ven como `/#/about`.

## Por qué hace falta el 404.html

GitHub Pages es un hosting estático. Solo entrega archivos que existen en el sitio, y una ruta como `/about` no es un archivo. Mientras se navega con el menú no pasa nada, porque Angular cambia la vista sin pedirle nada al servidor. Pero si se abre `/about` directamente o se recarga la página con F5, el navegador sí le pide esa ruta a Pages, y Pages responde con un 404.

Para evitarlo, el pipeline copia `index.html` como `404.html`. Cuando Pages no encuentra una ruta, entrega ese archivo, que es la aplicación completa, y Angular lee la dirección y muestra la vista correcta.

Con Hash Location este paso no es necesario, porque lo que va después del `#` nunca llega al servidor.

## Build con base-href

La página no se sirve desde la raíz del dominio sino desde `/Uceva-Pipeline-App-Path/`. Por eso el build en el pipeline se ejecuta con:

```
npm run build -- --base-href /Uceva-Pipeline-App-Path/
```

Sin esa opción, el navegador buscaría los archivos JavaScript y el favicon en la raíz de `santiagocruz09.github.io` y no los encontraría.

## Pipeline

El archivo `.github/workflows/deploy.yml` se ejecuta en cada push a `main` (y también se puede lanzar a mano desde la pestaña Actions). Tiene dos jobs:

1. `build`: descarga el código, instala Node 24, ejecuta `npm ci`, corre las pruebas con `npm test`, construye la aplicación, crea el `404.html` y sube el resultado como artefacto.
2. `deploy`: publica ese artefacto en GitHub Pages.

Si las pruebas o el build fallan, el despliegue no se ejecuta.

Para que funcione hay que activar Pages en el repositorio: Settings, Pages, Source, GitHub Actions.

## Cómo correrla en local

```
npm install
npm start
```

Queda disponible en http://localhost:4200/. Para generar el build y correr las pruebas:

```
npm run build
npm test
```

La salida del build queda en `dist/pipeline-app/browser`. El proyecto se llama `pipeline-app` dentro de `angular.json`, aunque el paquete en `package.json` sea `pipeline-app-path`.

## Mensajes de commit

Usamos Conventional Commits (`feat:`, `fix:`, `docs:`, `ci:`, `chore:`) para que el historial se lea fácil.
