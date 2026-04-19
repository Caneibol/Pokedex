🚀 Despliegue de la PokeDex en Azure Static Web Apps
📋 Requisitos previos

Antes de empezar, se necesita tener:

Cuenta activa en Azure for Students
Repositorio en GitHub:
https://github.com/Caneibol/Pokedex
Git instalado en el equipo
Node.js y Angular CLI configurados
1️-Configuración inicial del proyecto

Primero cloné el repositorio en mi equipo y entré a la carpeta del proyecto:

git clone https://github.com/Caneibol/Pokedex.git
cd Pokedex

Luego instalé todas las dependencias necesarias:

npm install
2️-Creación del recurso en Azure

Para desplegar la aplicación, utilicé Azure Static Web Apps desde el portal de Azure:

Entré a https://portal.azure.com
Busqué Static Web Apps
Seleccioné la opción Crear
Llené los campos con la configuración del proyecto:
Suscripción: Azure for Students
Grupo de recursos: creado para la app
Nombre: Pokedex
Región: East US 2
Plan: Gratis
Origen: GitHub
Repositorio: Caneibol/Pokedex
Rama: main
Build preset: Angular
App location: /
Output location: dist/pokedex-angular

Finalmente, revisé y creé el recurso.

3️- Configuración del pipeline (CI/CD)

Azure automáticamente generó un workflow en GitHub dentro de:

.github/workflows/pipelines.yml

Durante el despliegue encontré algunos errores y los corregí:

🔧 Problema 1: output mal configurado

El campo appArtifactLocation estaba tomando un array en lugar de un string.

✔ Solución: definir correctamente:

output_location: "dist/pokedex-angular"
🔧 Problema 2: error con codecov

El pipeline fallaba porque la versión codecov@3.8.3 no es compatible con Node.js v22.

✔ Solución: eliminar completamente ese paso del pipeline.

Configuración final del deploy:
- name: Build And Deploy
  uses: Azure/static-web-apps-deploy@v1
  with:
    azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
    repo_token: ${{ secrets.GITHUB_TOKEN }}
    action: "upload"
    app_location: "/"
    api_location: ""
    output_location: "dist/pokedex-angular"
4️⃣ Corrección de error en imágenes

Al desplegar, las imágenes no cargaban (error 404).

❌ Problema:

La ruta en producción estaba mal definida en:

src/environments/environment.prod.ts
imagesPath: '/pokedex-angular/assets/images'
✅ Solución:
imagesPath: '/assets/images'

Después hice commit y push:

git add .
git commit -m "fix: corregir ruta de imagenes en produccion"
git push origin main
5️- Seguridad de la aplicación

Para mejorar la seguridad, creé el archivo:

staticwebapp.config.json

Aquí configuré los headers HTTP:

{
  "globalHeaders": {
    "Content-Security-Policy": "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-hashes'; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com https://www.gstatic.com; font-src 'self' https://fonts.gstatic.com; img-src 'self' data: https:; connect-src 'self' https://pokeapi.co https://beta.pokeapi.co;",
    "Strict-Transport-Security": "max-age=31536000; includeSubDomains; preload",
    "X-Content-Type-Options": "nosniff",
    "X-Frame-Options": "DENY",
    "Referrer-Policy": "strict-origin-when-cross-origin",
    "Permissions-Policy": "geolocation=(), microphone=(), camera=()"
  }
}

Luego validé la seguridad en:

👉 https://securityheaders.com

Resultado: A ✅

6️- Subida final de cambios
git add .
git commit -m "fix: configuracion final de despliegue y seguridad"
git push origin main
⚠️ Problemas encontrados

Durante el proceso me encontré con varios errores:

Configuración incorrecta del output en Azure
Fallo por dependencia deprecada (codecov)
Imágenes que no cargaban en producción
Restricciones de seguridad muy estrictas (CSP)
Problemas con submódulos en Git

Todos fueron solucionados ajustando configuración, rutas y dependencias.

🔗 Aplicación desplegada

Puedes ver el resultado final aquí: https://nice-rock-07b03fb0f.7.azurestaticapps.net/
