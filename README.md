# Agregar un HTML personalizado en un servidor web dockerizado Nginx

El objetivo de esta práctica es desplegar un HTML sobre un servidor web dockerizado Nginx, para ello hay que seguir los siguientes pasos : 

## Paso 1: Instalar NGNIX

Para poder desplegar nuestra página en el servidor Nginx, primero hay que instalarlo, esto se puede hacer mediante la ejecución de la imagen de Nginx 
``docker pull nginx`` que podemos encontrar en DockerHub, la cual descarga y la guarda en el caché de docker . 

<img width="446" alt="1" src="https://user-images.githubusercontent.com/91699247/168776669-a6b66d4c-1ad4-42f7-84d0-57b70c2b4c95.PNG">

<img width="540" alt="1" src="https://user-images.githubusercontent.com/91699247/169006111-b25dfaf4-1bf8-4152-93fa-c5c5ee536b47.png">



Con este comando, lleva a cabo la ejecución del contenedor como un demonio y publica el puerto 8080 en la red del host.
```
docker run --rm -d -p 8080:80 --name web nginx
```

Si accedemos a ``http://localhost:8080`` comprobamos que la ejecución y el resultado han sido satisfactorios, ya que podemos ver la página por defecto de Nginx.

<img width="492" alt="2" src="https://user-images.githubusercontent.com/91699247/169006260-fafba9f3-6fc4-46dc-8ed9-dd50de9738cf.png">


En los siguientes pasos, cambiaremos esta página por una personalizada, antes de pasar al siguiente paso, hay que parar el contenedor con un ``docker stop web``.


## Paso 2: Agregar un HTML personalizado

De forma predeterminada, Nginx busca en el directorio ``/usr/share/nginx/html`` dentro del contenedor los archivos para servir, por lo tanto, tenemos que poner
nuestro archivo html en este directorio. Esto se puede hacer con un volumen montado, que permite vincular un directorio en nuestra máquina local y asignar ese 
directorio a nuestro contenedor en ejecución.

Crearemos una página html personalizada y luego la publicaremos usando la imagen Nginx.

Primero hay que crear un directorio llamado ``nginx`` dentro de ``Documentos`` y dentro de este otro, llamado ``site-content``. 

<img width="510" alt="3" src="https://user-images.githubusercontent.com/91699247/169007107-21d6f165-eb99-41ae-ac50-83ee56ffa6e5.png">


Dentro de ``site-content`` agregamos el archivo ``index.html`` , que tiene el siguiente contenido : 

<img width="537" alt="4" src="https://user-images.githubusercontent.com/91699247/169006510-32a945a4-c667-4868-90a1-f7a6a0f1dbdc.png">


Con el siguiente comando, podemos crear un volumen y montará nuestro directorio local ``~/Documentos/nginx/site-content`` localmente en el contenedor en ejecución 
en ``/usr/share/nginx/html`` :
```
docker run --rm -d -p 8080:80 --name web -v ~/Documentos/nginx/site-content:/usr/share/nginx/html nginx
```

Como podemos ver si accedemos a localhost:8080, aparece nuestra página personalizada :

<img width="483" alt="5" src="https://user-images.githubusercontent.com/91699247/169006532-49569b1d-ff5a-4d27-a314-9dbcaeb5b191.png">


## Paso 3: Crear una imagen personalizada

Los volúmenes permiten ejecutar localmente y compartir archivos en un contenedor en ejecución. Pero, ¿qué pasa si queremos mover esta imagen y que nuestros archivo html se mueva con ella?

La forma en que vamos a hacer esto es copiando nuestro archivo html en la imagen creando una imagen personalizada. 

Para crear una imagen personalizada, hay crear un Dockerfile y agregarle una serie de comandos. En el mismo directorio, se crea un archivo llamado Dockerfile y 
se insertan los siguientes comandos.

```
FROM nginx:latest
COPY ./site-content/index.html /usr/share/nginx/html/index.html
```

Con el primer comando, comenzamos a construir nuestra imagen personalizada usando una imagen base, hacemos esto usando el comando FROM. Esto extrae la última imagen de nginx a nuestra máquina local y luego construye nuestra imagen personalizada encima de ella.

En el segundo, copiamos nuestro archivo index.html en el directorio ``/usr/share/nginx/html`` dentro del contenedor, sobrescribiendo el archivo index.html predeterminado proporcionado por nginx.

Para construir nuestra imagen, hay que ejecutar el siguiente comando :

```
docker build -t webserver .
```

<img width="538" alt="2022-05-18 (7)" src="https://user-images.githubusercontent.com/91699247/169007290-417bd2aa-de6b-48ae-9067-95a66fa22e7b.png">


El comando de compilación le dirá a Docker que ejecute los comandos ubicados en nuestro Dockerfile


Ahora podemos ejecutar nuestra imagen en un contenedor, pero esta vez no tenemos que crear un volumen para incluir nuestro html pues ya lo hacemos en el Dockerfile
al copiar el archivo.

```
docker run --rm -d -p 8080:80 --name web webserver
```

Por último vemos nuestra página personalizada, que se ha desplegado correctamente sobre un servidor web dockerizado Nginx .

<img width="483" alt="5" src="https://user-images.githubusercontent.com/91699247/169007399-52e13ee5-6e1d-411a-bd3f-78828cf754d3.png">

