
# Solución para organizar múltiples subproyectos con `git subtree`  

Mi objetivo era organizar un proyecto local que tuviera múltiples partes (como un frontend, backend, y una aplicación móvil), pero que estuvieran bien estructuradas y sincronizadas con sus respectivos repositorios base. Además, no quería que los cambios locales afectaran al repositorio base original, sino mantener una estructura organizada, donde cada parte del proyecto esté aislada pero sincronizada. Aquí comparto la solución a los problemas que enfrenté y cómo logré implementar esta estructura usando `git subtree`.

## Descripción del problema

Quería crear un proyecto con un repositorio base, pero no quería que los cambios de mi nuevo proyecto afectaran al repositorio base original. Además, este proyecto necesita estar sincronizado con el repositorio base, pero me encontré con varias dificultades al intentar usar `--prefix=.` en la raíz del proyecto.

Aquí está la solución que encontré para manejar este escenario, incluyendo cómo sincronizar el proyecto con el repositorio base sin afectar la estructura del proyecto local.

## ¿Por qué no se puede usar `--prefix=.`?

Cuando intentas usar `--prefix=.` para agregar el contenido de un repositorio directamente en la raíz de un proyecto Git, Git lanza un error porque la raíz del proyecto ya existe o contiene archivos.

### Ejemplo de errores:

```
fatal: prefix '.' already exists.
```
o
```
fatal: can't squash-merge: '.' was never added.
You need to run this command from the top level of the working tree.
```

Estos errores indican que no se puede usar `git subtree` directamente en la ruta raíz porque ya contiene archivos o configuraciones y la idea es que este en la raiz.

## Solución

La solución es crear un directorio principal (proyecto) que contendra a las partes del proyecto (backend, frontend, app mobile, etc.) y usar `git subtree` para gestionar el código de cada subproyecto de forma aislada. Así, el directorio principal podra gestionar el proyecto y cada proyecto tendra su raiz

## Estructura de Directorios

A continuación se muestra un ejemplo de cómo deberías organizar tus proyectos localmente para mantener una estructura clara de la solución:

```
mis-proyectos
└───proyecto
│   ├───proyecto-backend
│   ├───proyecto-frontend
│   └───proyecto-app-mobile
│   .gitignore
│   readme.md (opcional)
└───proyecto-two
│   ├───proyecto-two-backend
│   ├───proyecto-two-frontend
│   └───proyecto-two-app-mobile
│   .gitignore
│   readme.md (opcional)
```

Puedes tener tantos proyectos como desees dentro de esta estructura.

## Comandos y Flujo de Trabajo para Configurar Múltiples Proyectos en Local y Sincronizar con Repositorios en GitHub

Voy a detallar los pasos usando un ejemplo donde `main-backend` es el repositorio base que quiero traer a un nuevo proyecto y mantener sincronizado usando `git subtree`.

### 1. Crear un Directorio Principal

Primero, creo un directorio principal y dentro de él inicializo Git.

```bash
mkdir proyecto
cd proyecto
git init
```

### 2. Añadir el Repositorio Base y Usar `git subtree`

Añado el repositorio remoto del backend que quiero sincronizar y utilizo `git subtree` para traer el código a mi subproyecto `proyecto-backend`:

```bash
pwd 
# Estoy en: proyecto
git remote add main-backend https://github.com/tu-usuario/repo-backend.git
git fetch main-backend
git subtree add --prefix=proyecto-backend main-backend main --squash
```

#### ⚠️Nota: Si aparece el siguiente error:

```
fatal: ambiguous argument 'HEAD': unknown revision or path not in the working tree.
Use '--' to separate paths from revisions, like this:
'git <command> [<revision>...] -- [<file>...]'
fatal: working tree has modifications.  Cannot add.
```

Este error indica que Git está detectando cambios no confirmados en tu repositorio (working tree has modifications) y además no puede encontrar la referencia HEAD, lo cual suele suceder si no tienes un commit inicial en tu repositorio recién creado. La solución es crear un commit vacío:

```bash
git commit --allow-empty -m "Commit inicial del backend"
```

Vuelve a ejecutar el comando `git subtree...`

### 3. Sincronización del Proyecto

Para sincronizar los cambios del repositorio base en el subproyecto, regreso al directorio principal que contiene los subproyectos y hago un `pull`:

```bash
pwd
# Estoy en: proyecto
git subtree pull --prefix=proyecto-backend main-backend main --squash
```

### 4. Repetir el Proceso para Múltiples Proyectos

Este flujo se puede repetir para cada proyecto adicional (Backend, Frontend, Aplicación Móvil, etc.).

## Configuración del `.gitignore`

Te recomiendo configurar un archivo `.gitignore` que ignore todos los archivos, excepto `README.md` y `.gitignore`, para mantener organizado tu proyecto principal y evitar conflictos:

```gitignore
# Ignorar todo excepto README.md y .gitignore
*
!.gitignore
!README.md
```

## 5. Subir los Cambios a un Nuevo Repositorio

Finalmente, subo los cambios al nuevo repositorio que he creado en GitHub. Asegúrate de estar en el subproyecto que vas a subir:

```bash
pwd
# Estoy en: proyecto/proyecto-backend
git init
git add .
git commit -m "Tu mensaje de commit"
git branch -M main
git remote add origin https://github.com/usuario/tu-nuevo-repo.git
git push -u origin main
```

¡Listo! Ya tienes tu proyecto sincronizado con un repositorio base, pero manteniendo los cambios locales sin afectar al repositorio original. 😊🙌
