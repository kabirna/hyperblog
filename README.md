# Guia de uso de Git y GitHub

Flujo de trabajo:

Working Directory --> Staging area --> Local repository

## Instalación

En distribuciones basadas en ArchLinux:

```bash
sudo pacman -Sy git
```

En distribuciones basadas en Debian:

```bash
sudo apt install git
```

En Windows y Mac:

> Bajar el instalador de la pagina oficial y seguir las instrucciones del asistente.

## Configurando Git en la terminal

Ver lista de comandos disponibles:

```bash
git config
```

Ver la configuracion actual de git:

```bash
git config --list
```

Especificar el nombre de usuario:

```bash
git config --global user.name 'Nombre de usuario'
```

Especificar el email (usualmente el mismo que usaremos en GitHub):

```bash
git config --global user.email 'user@mail.com'
```

## Comandos basicos de Git

Inicializar el repositorio en la carpeta actual, esto generará una carpeta oculta llamada `.git`:

```bash
git init
```

Ver el estado actual del staging area:

```bash
git status
```

Añadir archivo especifico al staging area:

```bash
git add nombre_archivo.txt
```

Añadir todos los archivos modificados al staging area:

```bash
git add -A
```

Quitar archivo especifico de staging area:

```bash
git rm --cached nombre_archivo.txt
```

Mandar los cambios al repositorio local:

```bash
git commit -m "Un mensaje"
```

Podemos hacer uso de un atajo que combina `git add` y `git commit` en un solo comando:

```bash
git commit -am "Un mensaje"
```

## Ver el estado del repositorio

Ver un listado de los commits de un archivo en especifico:

```bash
git log nombre_archivo.txt
```

Ver el listado de los commits mostrando las lineas que cambiaron:

```bash
git log --stat
```

Ver el listado de todos los commits de todas las ramas de manera decorada y en una sola linea:

```bash
git log --all --graph --decorate --oneline
```

Podemos crear un **alias** para no tener que escribir la linea anterior que es largisima:

```bash
alias arbolito="git log --all --graph --decorate --oneline"
```

**Nota** Si utilizamos ZSH como shell, debemos agregar el alias al archivo oculto `.zshrc` ubicado en nuestro home.

Ver el listado de los commits mostrando las lineas que cambiaron:

```bash
git show
```

## Operaciones con commits

Posicionarnos sobre un tag (SHA) de los commits:

```bash
git checkout [tag]
```

Ver las diferencias entre 2 commits:

```bash
git diff [tag1] [tag2]
```

Eliminar commits:

Volver al commit especificado conservando los archivos que esten en staging area:

```bash
git reset [tag] --soft
```

Volver al commit especificado, borrando todo lo anterior hasta el momento del tag especificado:

```bash
git reset [tag] --hard
```

## Ramas

Crear rama

```bash
git branch [nombre_rama]
```

Crear rama y posicionarnos sobre ella:

```bash
git checkout -b [nombre_rama]
```

Cambiarnos de rama:

```bash
git checkout [nombre_rama]
```

Listar todas las ramas:

```bash
git branch
```

Ver detalle de ramas:

```bash
git show-branch
git show-branch --all
```

Borrar rama:

```bash
git branch -d [nombre_rama]
```

Fusionar ramas:

1. Nos colocamos sobre la rama donde queremos el commit (usualmente master):

    ```bash
    git checkout master
    ```

2. Fusionamos las ramas:

    ```bash
    git merge [nombre_rama_a_fusionar]
    ```

## GitHub - Creando nuevas llaves SSH

Podemos utilizar distintos tipos de cifrado, el mas comun es RSA al ser mas compatible, sin embargo a partir de OpenSSH 6.5 podemos utilizar un cifrado mas seguro y con mejor rendimiento, este es **ED25519**

1. Creando las llaves desde la terminal:

    Generando una llave nueva con ED25519:

    ```bash
    ssh-keygen -t ed25519 -C "email@example.com" -f ~/.ssh/id_ed25519_github
    ```

    Generando una llave nueva con RSA:

    ```bash
    ssh-keygen -o -t rsa -b 4096 -C "email@example.com" -f ~/.ssh/id_rsa_github
    ```

    > La bandera -C es para agregar un comentario y poder identificarla mejor en caso de que tengamos muchas llaves.

    Esto generará 2 archivos (la llave publica y la llave privada):

    >* ~/.ssh/id_ed25519_github
    >* ~/.ssh/id_ed25519_github.pub

    Para agregarla a GitHub/GitLab debemos copiar el contenido de la llave publica, este es el archivo con terminacion `.pub`, para ver el contenido del archivo desde la terminal podemos hacer:

    ```bash
    cat ~/.ssh/id_ed25519_github.pub
    ```

2. Agregando las llaves a GitHub y GitLab

    Ahora nos vamos a la pagina de GitHub/GitLab y pegamos el contenido de las llaves en la sección de llaves SSH.

3. Registrar las llaves con `ssh-agent`

    Iniciar el agente con:

    ```bash
    eval $(ssh-agent) 
    ```

    En nuestro equipo local usaremos **ssh-agent** para agregar la llave privada:

    ```bash
    ssh-add ~/.ssh/id_ed25519_github
    ```

4. Crear y editar un archivo de configuración

    Debemos crear un archivo llamado `config` en la ruta donde se encuentran las llaves y abrirlo para editarlo:

    ```bash
    touch ~/.ssh/config
    nano ~/.ssh/config
    ```

    Dentro pondremos los datos de cada una de las cuentas que vamos a usar, especificando el archivo de la llave privada correspondiente:

    ```bash
    # Cuenta personal en GitHub
    Host github-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_github

    # Cuenta del trabajo en GitLab
    Host gitlab-trabajo
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/id_ed25519_gitlab
    ```

5. Especificar que llave usaremos al hacer operaciones con el repositorio remoto

    Al ejecutar comandos en el remoto, debemos especificar que llave queremos usar, esto lo logramos mediante el texto que pusimos en el archivo de configuración en el apartado `Host` (`github-personal` y `gitlab-trabajo`):

    ```bash
    git remote add origin git@github-personal:kabirna/nombre-repositorio.git
    ```

    Si queremos cambiar de repositorio lo haremos con:

    ```bash
    git remote set-url origin git@github-personal:kabirna/otro-repositorio.git
    ```

6. Probar si todo quedo configurado correctamente

    ```bash
    ssh -T git@github-personal
    ```

    Si todo salio bien recibiremos:

    ```bash
    Hi kabirna! You've successfully authenticated, but GitHub does not provide shell access.
    ```

## Obteniendo repositorio remoto

Listar el nombre de los remotos actuales:

```bash
git remote
```

Listar los detalles de los remotos actuales:

```bash
git remote -v
```

Para clonar un repositorio que no tenemos en local:

```bash
git clone [url-repositorio]
```

Para traer el contenido de un repositorio que si tenemos en local. Esto lo que hará es crear una rama llamada `origin/master` en el repositorio local (sin pasarlos al Working Directory).

```bash
git fetch origin
```

Ya tenemos los cambios del repositorio remoto en la rama `origin/master`, para pasarlos a nuestra rama `master` local debemos hacer un merge:

```bash
git merge origin/master
```

A partir de esto ya tenemos los nuevos cambios en la rama `master` local.

Podemos ahorrarnos los pasos anteriores ejecutando un solo comando:

```bash
git pull origin master
```

Cuando los commits del remoto no tienen nada que ver con los del repositorio local (Usualmente cuando solo se tiene el README.md en el remoto).

```bash
git pull origin master --allow-unrelated-histories
```

## Empujando cambios al remoto

Para mandar nuestros commits al repositorio remoto:

```bash
git push origin master
```

## Tags y versiones

Crear un nuevo tag y asignarlo a un commit:

```bash
git tag -a [nombre_etiqueta] [hash_commit]
```

Eliminar un tag:

```bash
git tag -d [nombre_etiqueta]
```

Listar los tags de nuestro repositorio local:

```bash
git tag
git show-refs --tags
```

Publicar un tag en el repositorio remoto:

```bash
git push origin --tags
```

Borrar un tag del repositorio remoto:

```bash
git tag -d [nombre_etiqueta]
git push origin :refs/tags/[nombre_etiqueta]
```

## Creando un Fork, contribuyendo a un repositorio

Para contribuir a un proyecto debemos hacer un **Fork**, esto lo que hara es crear una copia en nuestro Github.

Una vez hecha esta copia podemos descargar a nuestro repositorio local con:

```bash
git clone [url-repoisitorio]
```

Podemos solicitar un merge con el repositorio original, lo cual se conoce en GitHub como **Pull Request**.

Para mantener actualizado nuesto repositorio local con el proyecto original, lo que debemos hacer es crear un nuevo remoto que apunte al repositorio del proyecto original, de tal manera que **origin** será el remoto a nuestro repositorio en GitHub, y **upstream** será el remoto al proyecto original:

```bash
git remote add upstream [url-proyecto-original]
```

Para mantener actualizado nuestro repositorio local con el proyecto original, debemos hacer pull desde el **upstream**:

```bash
git pull upstream master
```

Una vez teniendo actualizado nuestro repositorio local, podemos continuar haciendo cambios en local y empujando a nuestro repositorio en Github (**origin**) para eventualmente solicitar un **Pull Request**.

## Tu sitio web público con GitHub Pages

Para configurar el sitio en la raíz y que en vez de quedar con el formato:

<https://kabirna.github.io/nombre-repositorio>

Debemos cambiar el nombre del repositorio a:

> kabirna.github.io

De esta manera ya podemos acceder a ese repositorio con:

<https://kabirna.github.io>

## Git Rebase: Reorganizando el trabajo realizado

Con el comando `git rebase` podemos reacomodar la historia en caso de que nos equivoquemos en un punto, recordar que hacer uso de rebase es **UNA MALA PRACTICA** y que solo debe ser usada en el repositorio local en casos muy necesarios.

Para ejemplificar dentro de nuestra rama `master` haremos 1 cambio y su correpondiente commit:

```bash
git commit -am "Master1"

git log --oneline

    * 7b6676d (HEAD -> master) Master1
```

Ahora vamos a crear una rama llamada `experimento` que contiene una copia de `master`:

```bash
git checkout -b experimento
```

```bash
* 7b6676d (HEAD -> experimento, master) Master1
```

Dentro de esta rama crearemos 2 cambios y haremos sus commits (**Exp1** y **Exp2**)

```bash
* 491b034 (HEAD -> experimento) Exp2
* 8f44ca6 Exp1
* 7b6676d (master) Master1
```

Ahora volvamos a nuestra rama `master` y hagamos un cambio y su commit:

```bash
* ee7e3d9 (HEAD -> master) Master2
* 7b6676d Master1
```

Supongamos que lo que queremos es que en nuestra rama `experimento` aparezcan al principio los commits de `master` y luego los que hicimos en `experimento`, es decir cambiar el orden de la historia, de esto:

```bash
* 491b034 (HEAD -> experimento) Exp2
* 8f44ca6 Exp1
* 7b6676d (master) Master1
```

A esto:

```bash
* f666c55 (HEAD -> experimento) Exp2
* 8ecd67b Exp1
* ee7e3d9 (master) Master2
* 7b6676d Master1
```

Lo que tenemos que hacer es un `git rebase`, desde la rama `experimento`:

```bash
$~proyecto (experimento)> git rebase master

First, rewinding head to replay your work on top of it...
Applying: Exp1
Using index info to reconstruct a base tree...
M       historia.txt
Falling back to patching base and 3-way merge...
Auto-merging historia.txt
Applying: Exp2
```

Con esto le estoy diciendo que: En la rama `experimento` me traiga todos los commits nuevos de `master` desde que se creo la rama `experimento` y los coloque en orden, es decir despues del ultimo commit mas reciente que teniamos de `master` al crear la rama `experimento`.

Simulando en la historia que nuestra rama `experimento` fue creada despues de todos los commits de `master`.

**Importante** Este cambio en la historia solo aplica a la rama desde la que hacemos el `rebase`, en este caso `experimento`.

Si ahora nos vamos a la rama master veremos que solo tenemos:

```bash
* ee7e3d9 (HEAD -> master) Master2
* 7b6676d Master1
```

Si queremos agregar los commits de `experimento` sin que se vea que provienen de otra rama, es decir como si nunca hubiera existido la rama `experimento`, sino que parezca que son commits realizados directamente en la rama `master` haremos un rebase desde `master` a la rama `experimento`:

```bash
$~proyecto (master)> git rebase experimento

First, rewinding head to replay your work on top of it...
Fast-forwarded master to experimento.
```

Y queda la historia de los commits de `master` así:

```bash
* f666c55 (HEAD -> master, experimento) Exp2
* 8ecd67b Exp1
* ee7e3d9 Master2
* 7b6676d Master1
```

## Git Stash: Guardar cambios en memoria y recuperarlos después

`git stash` es típico cuando estas haciendo cambios que no merecen una rama o un commit si no que simplemente estamos probando algo y luego queremos volver rápidamente a tu versión estable (HEAD).

Para crear el `stash` lo haremos con:

```bash
git stash

Saved working directory and index state WIP on master: f666c55 Exp2
```

Y esto nos devolverá automaticamente al ultimo commit que tenemos (HEAD).

Para ver una lista de los stash:

```bash
git stash list

stash@{0}: WIP on master: f666c55 Exp2
```

Para volver a los cambios que guardamos en el stash lo haremos con:

```bash
git stash pop
```

Si queremos mandar a una rama el contenido del stash, primero debemos hacer el stash y luego mandarlo a la rama:

```bash
git stash
git stash branch [nombre-rama]
```

Para borrar un stash:

```bash
git stash drop
```

Para guardar un stash con un mensaje (por si tenemos varios y queremos identificarlos):

```bash
git stash save "mensaje para identificar el stash"
```

Para llamar a un stash específico:

```bash
git stash apply stash@{numero del stash}
```

## Git Clean: Limpiar tu proyecto de archivos no deseados

Con el comando `git clean` podemos borrar archivos en nuestro Working Directory que esten como `untracked`.

Para ver una lista de los archivos que vamos a borrar:

```bash
git clean --dry-run
```

Para eliminar esos archivos:

```bash
git clean -f
```

**Nota:** Este comando no afecta a los archivos contenidos en `.gitignore`

## Git cherry-pick: Traer commits viejos al head de un branch

Cherry pick nos sirve para extraer commits de otras ramas e incluirlos en nuestro ultimo commit (HEAD) de la rama en la que nos encontramos.

Supongamos que tenemos una rama llamada `mejoras-al-readme` en donde tenemos 3 commits:

```bash
4af0ad5 (HEAD -> mejoras-al-readme) Es para todas las edades
627ca4e Incluye ejemplos en todos los S.O
e59c74b Creado por el team
```

Ubicados en la rama `master` extraemos el commit `e59c74b` de la rama `mejoras-al-readme` para incluirlo en el HEAD de `master`:

```bash
git cherry-pick e59c74b

[master 0ce769f] Creado por el team
 Date: Sat May 25 21:44:25 2019 -0500
 1 file changed, 1 insertion(+)
```

Con esto ahora nuestra rama master incluye el commit de la rama `mejoras-al-readme` en el HEAD sin la necesidad de haber hecho un merge ni dejar rastro de la rama.

```bash
0ce769f (HEAD -> master) Creado por el team
```

## Reconstruír commits en Git con amend

A veces hacemos un commit, pero resulta que no queríamos mandarlo porque faltaba algo más. Utilizamos `git commit --amend`, amend en inglés es remendar y lo que hará es que los cambios que hicimos nos lo agregará al commit anterior.

Veamos un ejemplo:

Supongamos que en el archivo `index.html` tenemos este titulo:

```html
<html>
    <head>
        <title>Titulo original</title>
    </head>
</html>
```

Ahora el titulo debe ser: `Este es un titulo mas creativo y divertido`, pero en el archivo se nos olvido ponerlo completo y solo agregamos `Este es un titulo mas creativo` y hacemos un commit de eso:

```bash
git commit -am "Le agregue un titulo mas creativo y divertido"
```

Nos damos cuenta que el titulo no esta completo, pero no queremos hacer un reset, volver a cambiar el titulo y rehacer el commit, por lo que utilizaremos `ammend`, nos vamos al `index.html` y ahora si ponemos el titulo completo, y super importante, debemos agregar el archivo mediante `git add` antes del `ammend`:

```bash
git add index.html
git commit --amend
```

Esto lo que hará sera guardarnos el titulo completo en el ultimo commit que teniamos (HEAD), y nos permite si queremos cambiar el mensaje del commit.

## Git Reset y Reflog: Úsese en caso de emergencia

Suponiendo que por error hemos realizado un `reset --hard` y hemos perdido cambios de algunos commits que posteriormente nos damos cuenta que necesitamos, si hacemos `git log` veremos que ya no aparecen dichos commits debido al `reset`, sin embargo podemos ver un historial mas amplio de todo lo que hemos hecho, o mas bien de todos los commits que estuvieron en HEAD en algun momento y de esta manera poder recuperar el commit deseado a traves de su hash:

```bash
git reflog
```

Ejemplo de salida:

```bash
0ce769f (HEAD -> master) HEAD@{0}: cherry-pick: Creado por el team
88daf18 HEAD@{1}: checkout: moving from mejoras-al-readme to master
4af0ad5 (mejoras-al-readme) HEAD@{2}: checkout: moving from master to mejoras-al-readme
88daf18 HEAD@{3}: checkout: moving from mejoras-al-readme to master
4af0ad5 (mejoras-al-readme) HEAD@{4}: commit: Es para todas las edades
627ca4e HEAD@{5}: commit: Incluye ejemplos en todos los S.O
e59c74b HEAD@{6}: commit: Creado por el team
88daf18 HEAD@{7}: checkout: moving from master to mejoras-al-readme
88daf18 HEAD@{8}: commit: Dejando el README listo para probar cherry-pick

```

Supongamos que queremos volver al commit `627ca4e` pero antes habiamos reseteado hasta el commit `88daf18` por lo que perdimos el commit `627ca4e` de nuestro log.

Entonces ahora que sabemos el hash del commit al que queremos volver, nuevamente aplicamos un `git reset`

```bash
git reset 627ca4e --hard
```

## Buscar en archivos y commits de Git con Grep y log

A medida que nuestro proyecto se hace grande vamos a querer buscar ciertas cosas.

Por ejemplo: ¿cuántas veces en nuestro proyecto utilizamos la palabra color?

Para buscar utilizamos el comando `git grep color` y nos buscará en todo el proyecto los archivos en donde está la palabra color.

Con `git grep -n color` nos saldrá un output el cual nos dirá en qué línea está lo que estamos buscando.
Con `git grep -c color` nos saldrá un output el cual nos dirá cuántas veces se repite esa palabra y en qué archivo.
Si queremos buscar cuántas veces utilizamos un atributo de HTML lo hacemos con `git grep -c "<p>"`.

Para buscar cosas en los commits usamos:

```bash
git log -S "texto_a_buscar"
```

## Comandos y recursos colaborativos en Git y Github

Ver logs por persona colaboradora:

```bash
git shortlog
```

Agregar **alias** dentro de Git para comandos largos:

```bash
git config --global alias.[nombre_alias] "shortlog -sn --all --no-merges"
```

Ver quien ha hecho cada camnbio en el repositorio:

```bash
git blame [nombre_archivo] -C
```

> La bandera -C es para ver identado el codigo

Ver el uso de un comando en git:

Ejemplo:

```bash
git blame --help
```

Ver quien hizo un cambio entre ciertas lineas especificas:

```bash
git blame [nombre_archivo] -L35,53 -C
```

Ver las ramas que tenemos en el remoto:

```bash
git branch -r
```

Ver las ramas locales y remotas:

```bash
git branch -a
```
