https://36df-200-3-193-78.ngrok-free.app

<img width="256" src="https://www.icesi.edu.co/launiversidad/images/La_universidad/logo_icesi.png">


# Proyecto Integrador 1
Este es el repositorio del curso. Ejemplos de código, acceso a videos, etc. serán accesibles desde aquí.

# Entrega 1


## 1. Introducción
Una buena introducción debe poder contextualizar al lector sobre el trabajo. En qué entorno nace el proyecto, posibles antecedentes y por qué el proyecto es una necesidad y en qué ayudaría a satisfacer esta necesidad, cómo se toman actualmente los datos y cómo un proyecto desplegado en la red contribuye al problema. Puede usar imágenes o ilustraciones que permitan al lector conocer el proyecto. Debe hacer de cuenta que el lector no tiene la menor idea del proyecto. 



## 2. Problema de ingeniería
Debe poder describir el problema. ¿Cuál es el problema que se está solucionando?. Para esto primera haga una análisis causa-raíz y use un gráfico de arbol de problema donde las causas identificadas son las raíces del arbol, el problema es el tronco y las consecuencias son las hojas.

## 3. Justificación
Luego de analizar el problema, justifique por qué el uso de la tecnología y las comunicaciones soluciona el problema que han planteado.

## 4. Referencias
En la introducción incluya al menos <b>4 referencias</b>, puede ubicarlas en la introducción o en la justificación. Recuerde que una referencia puede dar fuerza a un argumento o evoca el <b>estado del arte</b> de un área de trabajo específico.


# Entrega 2

## 5. Requerimientos
Elicite los requerimientos siguiendo estrictamente el EARS Pattern (https://alistairmavin.com/ears/)


# Planeador

<a href="https://docs.google.com/spreadsheets/d/1feE3kOQm7wpqfM3v6dFeca1pdTfPyf7G/edit?usp=sharing&ouid=117897710133227559254&rtpof=true&sd=true"><small>Planeador de Proyecto Integrador 1</small></a>

# Grupo de Whatsapp

<table style="border-collapse: collapse; border: none;" border="0">
  <tr>
    <td>
      <a href="https://miro.com/app/board/o9J_l2waJG0=">
        <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/6/6b/WhatsApp.svg/479px-WhatsApp.svg.png" width="128">
      </a>
    </td>
    <td style="vertical-align: middle;">
      <a href="https://chat.whatsapp.com/BB96owfvYzSLqTZJ0KtkhV">Grupo de Whatsapp</a>
    </td>
  </tr>
</table>




# Comando básicos de git

## Comandos clave:
</br>

```
git init
```
Para que una carpeta normal del sistema se vuelva un repositorio (tiene una carpeta oculta llamada .git)
</br></br>
```
git status
```
Para mirar los cambios actualues 
</br></br>
```
git add .
```
Para agregar todos los cambios al stage area
</br></br>
```
git commit -m "Mensaje"
```
Para crear un commit con los cambios del stage area
</br></br>
```
git log --oneline
```
Para ver el historial de commits
</br></br>

```
git log --oneline --graph
```
Para ver el historial de commits, pero más gráficamente
</br></br>


### Remotos
```
git remote -v 
```
Permite listar los remotos de nuestro repositorio local
</br></br>


```
git remote add <nombreRemoto> <URLremoto> 
```
Permite agregar un remoto con un nombre específico
</br></br>


```
git remote rm <nombreRemoto> 
```
Permite quitar un remoto
</br></br>


### Operaciones con remotos

```
git push <nombreRemoto> <nombreBranch> 
```
Permite actualizar un remoto con el trabajo hecho en el repositorio local
</br></br>

```
git pull <nombreRemoto> <nombreBranch> 
```
Permite actualizar una rama local usando una rama remota como fuente de actualización. Tener en cuenta que debemos estar parados en una rama local homónima a la que estamos invocando con el comando, porque si no, estamos mezclando las ramas
</br></br>

```
git fetch -p 
```
Permite actualizar las ramas respecto al repositorio remoto
</br></br>

```
git push --delete origin <nombreDeRama> 
```
Permite eliminar la rama nombreDeRama del remoto
</br></br>


### Branches

```
git branch <nombreBranch>
```
Permite crear una nueva rama
</br></br>

```
git checkout -b <nombreBranch>
```
Permite crear una nueva rama y pararse en ella en un sólo comando
</br></br>

```
git branch -a
```
Listar todas las ramas
</br></br>

```
git merge <nombreRama>
```
Permite mezclar la rama nombreRama en la rama en la que estemos parados. Hay 3 posibilidades: fast-fordward, recursive strategy o conflictos. En los dos últimos casos tendremos un commit específico que representa la mezcla.
</br></br>

```
git branch -d
```
Permite eliminar una rama local
</br></br>

```
git branch -D
```
Permite eliminar una rama local a la fuerza
</br></br>
