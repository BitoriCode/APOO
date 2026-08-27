Git & GitHub: Control de versiones con tu proyecto POO

---

## Contexto

En este ejercicio vas a publicar en GitHub la solución del problema de POO del **Ejercicio — Gestor de Proyectos de Software**, practicando el flujo de trabajo profesional con control de versiones.

> Resuelve primero el Ejercicio  y asegúrate de que tu código pasa los casos de prueba antes de continuar aquí.

---

## Parte 1 — Git & GitHub: sube tu solución

Una vez que tengas el código funcionando, vas a publicarlo en GitHub siguiendo el flujo profesional de control de versiones.

> **Pre-requisitos:** tener Git instalado (`git --version`) y una cuenta en [github.com](https://github.com).

---

### Paso 1 — Configura tu identidad en Git

Git necesita saber quién hace cada commit. Tienes dos formas de configurar tu identidad, y es importante entender la diferencia, especialmente cuando trabajas en **equipos compartidos o computadores del laboratorio**.

#### Opción A — Configuración global (afecta a todos los repositorios del equipo)

```bash
git config --global user.name  "Tu Nombre"
git config --global user.email "tu@correo.com"
```

Esta configuración queda guardada en el archivo `~/.gitconfig` de Windows (`C:\Users\TuUsuario\.gitconfig`). Aplica a **cualquier repositorio** que abras en ese equipo.

> ⚠️ **Evita usarla en equipos de laboratorio o computadores que no son tuyos.** Tus datos quedarán registrados para cualquier persona que use ese computador después.

#### Opción B — Configuración local (solo para el repositorio actual) ✅ Recomendada en equipos compartidos

```bash
git config --local user.name  "Tu Nombre"
git config --local user.email "tu@correo.com"
```

Esta configuración se guarda dentro de la carpeta `.git/config` del proyecto. Solo aplica a ese repositorio específico y no afecta otros proyectos ni otros usuarios del equipo.

Verifica qué configuración tiene actualmente el repositorio:

```bash
git config --local --list
```

Verifica la configuración global del equipo (para saber si alguien más dejó sus datos):

```bash
git config --global --list
```

> **Regla práctica:** si trabajas en diferentes proyectos con distintas cuentas (personal, universidad, empresa), usa siempre `--local` por proyecto. Así cada repositorio tiene su propia identidad sin interferir con los demás.

---

### Paso 2 — Prepara la carpeta de tu proyecto

Crea una carpeta para tu ejercicio y coloca dentro el archivo con tu solución:

```
gestor_proyectos/
    gestor_proyectos.py
```

Desde la terminal, entra a esa carpeta:

```bash
cd gestor_proyectos
```

---

### Paso 3 — Inicializa el repositorio local

```bash
git init
```

Esto crea la carpeta oculta `.git` que convierte la carpeta en un repositorio Git. Git aún no está "vigilando" ningún archivo.

---

### Paso 4 — Revisa el estado del repositorio

```bash
git status
```

Verás que `gestor_proyectos.py` aparece como **untracked** (no rastreado). Git lo detecta pero no lo ha incluido aún.

---

### Paso 5 — Agrega el archivo al área de preparación (staging)

```bash
git add gestor_proyectos.py
```

Vuelve a ejecutar `git status`. Ahora el archivo aparece en verde bajo *"Changes to be committed"*. Esto significa que está listo para ser confirmado.

---

### Paso 6 — Crea tu primer commit

```bash
git commit -m "feat: implementación inicial del gestor de proyectos POO"
```

Un **commit** es una foto del estado actual de tus archivos. El mensaje debe describir qué cambió.

Buenas prácticas para mensajes de commit:
- Usa el prefijo `feat:` para nuevas funcionalidades.
- Usa `fix:` para correcciones de errores.
- Escríbelo en infinitivo o descripción corta, en minúsculas.

---

### Paso 7 — Crea el repositorio remoto en GitHub y conéctalo

Tienes dos caminos equivalentes. Elige el que mejor se adapte a tu situación.

---

####  Ruta A — Crear primero en GitHub, luego conectar el repositorio local

Esta ruta es útil cuando ya tienes trabajo local y quieres vincularlo a un repositorio recién creado en GitHub.

1. Ve a [github.com](https://github.com) e inicia sesión.
2. Haz clic en el botón **"New"** (repositorio nuevo).
3. Dale el nombre `gestor-proyectos-poo`.
4. Déjalo como **público** o **privado** según prefieras.
5. **No inicialices** con README ni `.gitignore` (ya tienes tu repositorio local con commits).
6. Haz clic en **"Create repository"**.

GitHub te mostrará los comandos. Conéctalo con tu repositorio local:

```bash
git remote add origin https://github.com/TU_USUARIO/gestor-proyectos-poo.git
```

Verifica que quedó registrado:

```bash
git remote -v
```

Sube tu código:

```bash
git push -u origin main
```

---

####  Ruta B — Crear primero en GitHub con README, luego clonar y trabajar desde ahí

Esta ruta es útil cuando empiezas un proyecto desde cero y prefieres que GitHub tenga la "versión maestra" desde el inicio.

1. En GitHub, crea el repositorio igual que en la Ruta A, pero esta vez **sí marca** la opción *"Add a README file"*.
2. Haz clic en **"Create repository"**.
3. En la página del repositorio, copia la URL (botón verde **"Code"** → opción HTTPS).
4. En tu terminal, clona el repositorio:

```bash
git clone https://github.com/TU_USUARIO/gestor-proyectos-poo.git
cd gestor-proyectos-poo
```

5. Copia tu archivo `gestor_proyectos.py` dentro de esa carpeta y continúa con el flujo normal:

```bash
git add gestor_proyectos.py
git commit -m "feat: implementación inicial del gestor de proyectos POO"
git push
```

> **¿Cuándo usar cada ruta?**
> - **Ruta A**: ya tienes código local y quieres publicarlo.
> - **Ruta B**: empiezas desde cero y quieres que GitHub sea el punto de partida.

---

### Paso 8 — Verifica en GitHub

Recarga la página de tu repositorio en GitHub. Deberías ver el archivo `gestor_proyectos.py` con tu commit inicial.

---

### Paso 9 — Agrega un archivo nuevo al repositorio

Un repositorio casi siempre tiene más de un archivo. Vamos a agregar un `README.md` que describa el proyecto.

Crea el archivo `README.md` dentro de la misma carpeta con el siguiente contenido:


# Gestor de Proyectos POO

Ejercicio de Programación Orientada a Objetos.

## Clases implementadas
- `Colaborador`: representa a una persona que trabaja en un proyecto.
- `Proyecto`: agrupa colaboradores y expone operaciones sobre ellos.
- `GestorProyectos`: administra la colección global de proyectos.

## Cómo ejecutar
```bash
python gestor_proyectos.py
```

Ahora revisa el estado del repositorio:

```bash
git status
```

Verás que `README.md` aparece como **untracked**. Agrégalo al área de preparación:

```bash
git add README.md
```

Confirma que está listo:

```bash
git status
```

Crea el commit:

```bash
git commit -m "docs: agrega README con descripción del proyecto"
```

Sube el cambio a GitHub:

```bash
git push
```

Recarga tu repositorio en GitHub. Ahora verás el `README.md` renderizado en la página principal.

---

### Paso 10 — Modifica un archivo existente y registra el cambio

Abre `gestor_proyectos.py` y agrega al final del archivo un bloque de prueba dentro de un `if __name__ == "__main__":`:

```python
if __name__ == "__main__":
    ana   = Colaborador(username="ana_dev", email="ana@mail.com")
    luis  = Colaborador(username="luis99",  email="luis@mail.com")
    sofia = Colaborador(username="sofiaml", email="sofia@mail.com")

    p1 = Proyecto(nombre="InventarioApp", lenguaje="Python")
    p1.agregar_colaborador(ana)
    p1.agregar_colaborador(luis)
    p1.agregar_colaborador(ana)

    p2 = Proyecto(nombre="WebStore", lenguaje="JavaScript")
    p2.agregar_colaborador(sofia)

    gestor = GestorProyectos()
    gestor.registrar_proyecto(p1)
    gestor.registrar_proyecto(p2)

    for proyecto in gestor.listar_proyectos():
        print(proyecto)
```

Guarda el archivo y revisa el estado del repositorio:

```bash
git status
```

Ahora verás que `gestor_proyectos.py` aparece como **modified** (modificado). Git detectó el cambio pero aún no lo ha incluido en ningún commit.

Observa exactamente qué líneas cambiaron:

```bash
git diff gestor_proyectos.py
```

Las líneas en verde con `+` son las que agregaste. Las líneas en rojo con `-` son las que eliminaste.

Agrega el archivo modificado al área de preparación:

```bash
git add gestor_proyectos.py
```

Crea el commit:

```bash
git commit -m "feat: agrega bloque de prueba en __main__"
```

Sube el cambio:

```bash
git push
```

---

### Paso 11 — Verifica el historial de commits

Con dos commits ya subidos, puedes ver la historia del repositorio:

```bash
git log --oneline
```

Deberías ver algo como:

```
a3f9c12 feat: agrega bloque de prueba en __main__
7b2e041 docs: agrega README con descripción del proyecto
1d8a005 feat: implementación inicial del gestor de proyectos POO
```

Cada línea es un commit: a la izquierda su identificador corto (hash) y a la derecha el mensaje. Git guarda toda esa historia de forma permanente.

---

### Paso 12 — Trabaja con ramas (branches)

Hasta ahora todos los commits han ido directo a la rama principal (`main`). En la práctica, cuando quieres agregar una nueva funcionalidad o experimentar sin afectar el código que ya funciona, creas una **rama** separada.

Una rama es simplemente un puntero independiente dentro del mismo repositorio. Los cambios que hagas en ella no afectan a `main` hasta que tú decidas unirlos.

#### 12.1 — Mira en qué rama estás

```bash
git branch
```

El asterisco `*` indica la rama activa. Ahora mismo deberías ver solo `* main`.

#### 12.2 — Crea y cambia a una rama nueva

```bash
git checkout -b feature/eliminar-colaborador
```

- `checkout -b` crea la rama **y** se mueve a ella en un solo paso.
- El prefijo `feature/` es una convención: indica que esta rama contiene una nueva funcionalidad.

Verifica que cambiaste:

```bash
git branch
```

Ahora el asterisco estará en `* feature/eliminar-colaborador`.

#### 12.3 — Agrega la nueva funcionalidad en esta rama

Abre `gestor_proyectos.py` y agrega el siguiente método dentro de la clase `Proyecto`, después de `tiene_colaborador`:

```python
def eliminar_colaborador(self, username: str) -> None:
    original = len(self._colaboradores)
    self._colaboradores = [c for c in self._colaboradores if c.username != username]
    if len(self._colaboradores) == original:
        print(f"Aviso: no se encontró el colaborador '{username}'.")
```

Guarda el archivo. Revisa qué detectó Git:

```bash
git status
git diff gestor_proyectos.py
```

Agrega el cambio y haz el commit en esta rama:

```bash
git add gestor_proyectos.py
git commit -m "feat: agrega método eliminar_colaborador en Proyecto"
```

#### 12.4 — Compara la rama con `main`

Para ver todos los commits que tiene esta rama que `main` aún no tiene:

```bash
git log main..feature/eliminar-colaborador --oneline
```

#### 12.5 — Vuelve a `main` y fusiona los cambios (merge)

```bash
git checkout main
```

Confirma que estás en `main`:

```bash
git branch
```

Ahora fusiona la rama:

```bash
git merge feature/eliminar-colaborador
```

Git integrará el commit de la rama en `main`. Si no hay conflictos, verás un mensaje como `Fast-forward` o `Merge made by...`.

Revisa el historial para confirmar que el commit quedó en `main`:

```bash
git log --oneline
```

#### 12.6 — Sube `main` con los cambios fusionados

```bash
git push
```

#### 12.7 — Elimina la rama (opcional pero recomendado)

Una vez fusionada, la rama ya cumplió su propósito. Borrarla mantiene el repositorio ordenado:

```bash
git branch -d feature/eliminar-colaborador
```

La opción `-d` solo borra la rama si ya fue fusionada. Si intentas borrar una rama con cambios sin fusionar, Git te avisará.

> **Flujo resumido de trabajo con ramas:**
> ```
> git checkout -b feature/nombre   ← crea y cambia a la rama
> # ... edita archivos ...
> git add .
> git commit -m "descripción"
> git checkout main
> git merge feature/nombre          ← integra los cambios
> git push
> git branch -d feature/nombre     ← limpia la rama
> ```

---

## Parte 2 — Trabajar con múltiples repositorios y proyectos distintos

En la práctica es común tener varios proyectos activos al mismo tiempo, cada uno con su propio repositorio. Git maneja esto de forma natural: **cada carpeta con su propia subcarpeta `.git` es un repositorio independiente**.

### Estructura típica con múltiples proyectos

```
Documentos/
    proyecto_inventario/        ← repositorio 1
        .git/
        inventario.py
    proyecto_biblioteca/        ← repositorio 2
        .git/
        biblioteca.py
    proyecto_personal/          ← repositorio 3
        .git/
        main.py
```

Cada repositorio tiene su propia historia, sus propios commits y su propio remoto en GitHub.

### Configuración por proyecto (identidades distintas)

Si algunos proyectos usan tu cuenta personal y otros tu cuenta universitaria, configura la identidad localmente en cada uno:

```bash
# Dentro de proyecto_inventario/
git config --local user.name  "Nombre Universitario"
git config --local user.email "usuario@universidad.edu.co"

# Dentro de proyecto_personal/
git config --local user.name  "Nombre Personal"
git config --local user.email "yo@gmail.com"
```

Así cada repositorio sabe qué identidad usar al hacer commits, sin mezclar cuentas.

### Conectar cada proyecto a su propio repositorio en GitHub

Repite el proceso de la Ruta A o B para cada proyecto. Cada uno tendrá su propia URL remota:

```bash
# En proyecto_inventario/
git remote add origin https://github.com/TU_USUARIO/proyecto-inventario.git

# En proyecto_biblioteca/
git remote add origin https://github.com/TU_USUARIO/proyecto-biblioteca.git
```

---

## Parte 3 — Usando Git desde PyCharm

PyCharm tiene integración nativa con Git y permite hacer todas las operaciones anteriores desde una interfaz gráfica, sin necesidad de abrir una terminal.

### 3.1 — Abrir el proyecto en PyCharm

1. Abre PyCharm y selecciona **File → Open**.
2. Navega hasta la carpeta `gestor_proyectos/` y ábrela.

### 3.2 — Inicializar Git en el proyecto (si aún no lo hiciste)

1. Ve al menú **VCS → Enable Version Control Integration...** (o **Git → Initialize Repository**).
2. Selecciona **Git** en el desplegable y haz clic en **OK**.

PyCharm activará el panel de Git en la parte inferior de la ventana.

### 3.3 — Configurar tu identidad en PyCharm

1. Ve a **File → Settings** (Windows/Linux) o **PyCharm → Preferences** (macOS).
2. Navega a **Version Control → Git**.
3. Verifica que la ruta al ejecutable de Git sea correcta (PyCharm la detecta automáticamente).

> Para configurar nombre y correo, PyCharm usa la configuración de Git que ya hiciste en la terminal. Si usaste `--local`, los cambios ya están aplicados al proyecto abierto.

### 3.4 — Hacer un commit desde PyCharm

1. Modifica o crea un archivo en el proyecto.
2. PyCharm resalta los archivos modificados en el panel **Project** con colores:
   - **Verde**: archivo nuevo no rastreado.
   - **Azul**: archivo modificado rastreado.
3. Abre el panel de commit con **Ctrl+K** (Windows/Linux) o **Cmd+K** (macOS), o ve a **Git → Commit...**.
4. En la ventana de commit:
   - Marca los archivos que quieres incluir (equivale a `git add`).
   - Escribe el mensaje del commit.
   - Haz clic en **Commit** (solo local) o **Commit and Push** (local + remoto).

### 3.5 — Conectar con GitHub desde PyCharm

Si aún no has conectado el repositorio remoto:

1. Ve a **Git → Manage Remotes...**.
2. Haz clic en el ícono **+** y agrega la URL de tu repositorio en GitHub:
   ```
   https://github.com/TU_USUARIO/gestor-proyectos-poo.git
   ```
3. Haz clic en **OK**.

### 3.6 — Hacer push desde PyCharm

1. Ve a **Git → Push...** o presiona **Ctrl+Shift+K**.
2. Revisa los commits que se enviarán.
3. Haz clic en **Push**.

Si es la primera vez, PyCharm pedirá autenticarte con GitHub (usuario y contraseña o token).

### 3.7 — Ver el historial de commits en PyCharm

- Ve a **Git → Show Git Log** o abre la pestaña **Git** en la barra inferior.
- Verás el árbol de commits, las ramas y los autores de cada cambio.

---

## Parte 4 — Limpieza al terminar en equipos compartidos

> ⚠️ **Importante:** cuando trabajas en computadores del laboratorio o en equipos que no son tuyos, es tu responsabilidad borrar tus credenciales al terminar. De lo contrario, otros usuarios podrán hacer commits con tu nombre y correo.

### 5.1 — Eliminar la configuración global de identidad

```bash
git config --global --unset user.name
git config --global --unset user.email
```

Verifica que no quedó nada:

```bash
git config --global --list
```

Si el comando no muestra `user.name` ni `user.email`, la limpieza fue exitosa.

### 5.2 — Eliminar credenciales almacenadas en Windows

Windows puede haber guardado tu usuario y contraseña (o token) de GitHub en el **Administrador de credenciales**:

1. Abre el **Panel de Control → Cuentas de usuario → Administrador de credenciales**.
2. Selecciona la pestaña **Credenciales de Windows**.
3. Busca entradas que digan `git:https://github.com` o similares.
4. Haz clic en cada una y selecciona **Quitar**.

Alternativamente, desde PowerShell:

```powershell
cmdkey /delete:git:https://github.com
```

### 5.3 — Verificar que no quedó configuración global

```bash
git config --global --list
```

Si el comando no imprime nada (o no muestra tu nombre/correo), el equipo quedó limpio.

### 5.4 — Lo que NO necesitas borrar

- La configuración **local** (dentro de `.git/config` de tu proyecto) desaparece cuando eliminas la carpeta del proyecto. Si te llevas el proyecto en un USB o lo subes a GitHub, esa configuración local no afecta al equipo.
- Git en sí no necesita ser desinstalado; solo los datos personales deben eliminarse.

---

## Conceptos clave resumidos

| Concepto              | Qué es                                                                          |
|-----------------------|---------------------------------------------------------------------------------|
| `git init`            | Inicializa un repositorio local vacío                                           |
| `git clone`           | Copia un repositorio remoto al equipo local                                     |
| `git status`          | Muestra el estado actual (qué cambió, qué está en staging)                      |
| `git add`             | Mueve archivos al área de preparación (staging area)                            |
| `git commit`          | Guarda una "foto" permanente del estado actual                                  |
| `git push`            | Sube los commits locales al repositorio remoto (GitHub)                         |
| `git remote`          | Administra las conexiones con repositorios remotos                              |
| `--global`            | Configuración que aplica a todos los repositorios del equipo                    |
| `--local`             | Configuración que aplica solo al repositorio actual                             |
| Repositorio           | Carpeta versionada que guarda todo el historial de cambios                      |
| Commit                | Punto de control: registro inmutable de un conjunto de cambios                  |
| Rama (branch)         | Línea de desarrollo independiente dentro del mismo repositorio                  |
| Credenciales          | Usuario/token que Git usa para autenticarse con GitHub                          |

---

## Entrega

Comparte el **enlace a tu repositorio** en GitHub con tu profesor. El repositorio debe contener al menos:

- `gestor_proyectos.py` con la solución completa.
- Al menos **2 commits** (por ejemplo: implementación inicial + corrección o mejora).
- Antes de salir del aula, completa la **lista de verificación de limpieza** de la Parte 4.
