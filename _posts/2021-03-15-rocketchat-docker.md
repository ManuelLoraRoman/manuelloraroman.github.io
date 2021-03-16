---
layout: post
title: Rocket.Chat implementado con Docker
date: 2021-03-15 13:31 +0800
last_modified_at: 2021-03-15 13:32:25 +0800
tags: [RocketChat, Docker, Tutorial, LDAP, Ubuntu]
toc:  true
---
Rocket.Chat es una herramienta que permite de manera segura, mantener los datos y las comunicaciones centralizadas.
{: .message }

## 1. Introducción

En primer lugar, deberás tener una cuenta de Github. Como habitualmente se suele
acceder al mismo mediante SSH, haremos lo siguiente:

1. Copia el contenido del fichero *~/.ssh/id_rsa.pub* y añadelo en el apartado
de:
 
> _Settings --> SSH and GPG keys --> SSH keys --> New SSH key_  

y añadimos dicho contenido.    

2. Instala git en tu PC:

{% highlight js linenos %}

apt-get install git

{% endhighlight %}


3. Para configurar git con tu nombre de usuario y tu email (importante para
los "commits") debemos hacer lo siguiente:

{% highlight js linenos %}

git config --global user.name "Nombre de usuario"
git config --global user.email email@ejemplo.com

{% endhighlight %}

Esto solo hay que hacerlo una vez únicamente, ya que especificamos la opción
--global.


4. Clona el repositorio remoto en tu PC. Asegúrese de encontrarse en el 
directorio que usted desea almacenar dicho repositorio para trabajar de manera
local.

{% highlight js linenos %}

git clone git@github.com:ManuelLoraRoman/Prueba.git``` 

{% endhighlight %}

## 2. Comandos básicos de Git

* _<span style="color:black">git add</span>_ --> permite añadir al repositorio un nuevo fichero.

* _<span style="color:black">git rm</span>_ --> se usa para borrar ficheros del repositorio. Se usa de la misma
               manera que el "_rm_" de la shell.

* _<span style="color:black">git commit</span>_ --> permite realizar y mandar un commit al repositorio remoto.
                 Se suele usar los parámetros -am para añadir un fichero
		 (add) y escribir el contenido del commit al mismo tiempo.

* _<span style="color:black">git mv</span>_ --> cambia el nombre de cierto fichero o mueve un fichero de un
	       directorio a otro.

* _<span style="color:black">git push</span>_ --> envía los cambios al repositorio remoto.

* _<span style="color:black">git pull</span>_ --> sincroniza el repositorio local con el remoto (en caso de que
               se trabaje con varios repositorios locales).

* _<span style="color:black">git status</span>_ --> comprueba el estado del repositorio local.


## 3. Git Avanzado


* _<span style="color:black">git log</span>_ --> lista las confirmaciones hechas sobre el repositorio en el
		que trabajamos en orden cronológico. Muestra varios datos como
		la suma de comprobación SHA-1, nombre, email, fecha, etc.
		
Al usar el parámetro -p, muestra las diferencias en cada
confirmación. Al usar -x(nº), muestras las x últimas entradas.
		
Si usamos --pretty, modificaremos el formato de salida. El 
formato _oneline_ imprime cada confirmación en una única línea.
Otras opciones de formato son _short_, _full_ o _fuller_.
Puedes crear tu propio formato con _format_. Para más
información sobre esto, visita esta 
[página](https://uniwebsidad.com/libros/pro-git/capitulo-2/viendo-el-historico-de-confirmaciones).


* _<span style="color:black">git commit --amend</span>_ --> si haces la confirmación demasiado pronto, y te has
			   olvidado modificar, crear, etc, puedes volver a hacer
			   la confirmación con este comando. 
 
* _<span style="color:black">git remote -v</span>_ --> muestra todos los repositorios remotos que tienes
		      configurados.

* _<span style="color:black">git remote add nombre URL</span>_ --> añade a los repositorios configurados un
				  repositorio en cuestión. Útil si no quieres
				  usar toda la URL, y quieres usar un nombre. 

* _<span style="color:black">git remote show repositorio</span>_ --> permite ver información del repositorio en
				    cuestión.

* _<span style="color:black">git remote rename repositorio nombrenuevo</span>_ --> cambia el nombre guardado del
						  repositorio. 

* _<span style="color:black">git fetch repositorio</span>_ --> recibe datos de un repositorio en cuestión.


------
