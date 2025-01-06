# ExamPass Frontend

Este proyecto es el frontend de **ExamPass**, una aplicación creada con **Vue 3**, **Tailwind CSS**, **Axios**, **Pinia** y **Google Auth**. Está diseñado para ofrecer una experiencia de usuario interactiva y moderna para gestionar exámenes y evaluaciones en línea.

## Tecnologías utilizadas

- **Vue 3**: Framework progresivo de JavaScript para construir interfaces de usuario.
- **Tailwind CSS**: Framework de CSS para un diseño rápido y responsivo.
- **Axios**: Cliente HTTP para realizar solicitudes a la API.
- **Pinia**: Gestor de estado para Vue 3, que reemplaza a Vuex.
- **Google Auth**: Autenticación a través de Google para facilitar el login de los usuarios.

## Instalación

Sigue estos pasos para configurar el proyecto en tu máquina local.

### 1. Clonar el repositorio

```bash
git clone https://github.com/tuusuario/exampass-frontend.git
```

## Instalar dependencias

Navega a la carpeta del proyecto e instala las dependencias necesarias.
npm install

Configuración de Google Auth

Para utilizar la autenticación de Google, necesitas configurar el cliente OAuth en la Consola de desarrolladores de Google. Una vez configurado, obtén las credenciales de la API y crea un archivo .env en el proyecto con la siguiente configuración:

### 2. Configurar Google Auth

1. Dirígete a la [Consola de desarrolladores de Google](https://console.developers.google.com/).
2. Crea un proyecto o selecciona uno existente.
3. Habilita la API de **Google Identity**.
4. Crea credenciales OAuth 2.0 para aplicaciones web.
5. En la configuración de credenciales, agrega las URI de redirección autorizadas para tu entorno local (por ejemplo, `http://localhost:8080`).
6. Descarga el archivo de credenciales y guarda el **Client ID** que te proporcionará

### 3. Crear el archivo `.env`

En la raíz de tu proyecto, crea un archivo llamado `.env` y agrega las siguientes variables de entorno:

```env
VUE_APP_GOOGLE_CLIENT_ID=tu_client_id_aqui


npm run serve
```
