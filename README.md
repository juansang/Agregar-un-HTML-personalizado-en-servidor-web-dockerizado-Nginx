# Agregar un HTML personalizado en un servidor web dockerizado Nginx

El objetivo de esta práctica es desplegar un HTML sobre un servidor web dockerizado Nginx, para ello hay que seguir los siguientes pasos : 

## Paso 1: Instalar NGNIX

Para poder desplegar nuestra página en el servidor Nginx, primero hay que instalarlo, esto se puede hacer mediante la ejecución de la imagen de Nginx 
``docker pull nginx`` que podemos encontrar en DockerHub, la cual descarga y la guarda en el caché de docker . 

<img width="446" alt="1" src="https://user-images.githubusercontent.com/91699247/168776669-a6b66d4c-1ad4-42f7-84d0-57b70c2b4c95.PNG">

![1](https://user-images.githubusercontent.com/91699247/168838341-529c31e4-5837-4da4-a2cd-d9156b1671e4.png)



Con este comando, lleva a cabo la ejecución del contenedor como un demonio y publica el puerto 8080 en la red del host.
```
docker run --rm -d -p 8080:80 --name web nginx
```

Si accedemos a ``http://localhost:8080`` comprobamos que la ejecución y el resultado han sido satisfactorios, ya que podemos ver la página por defecto de Nginx.

![Captura de pantalla de 2022-05-17 15-42-55](https://user-images.githubusercontent.com/91699247/168837910-ff263162-1fcc-45e5-a6e7-e3e70f1b998b.png)


En los siguientes pasos, cambiaremos esta página por una personalizada, antes de pasar al siguiente paso, hay que parar el contenedor con un ``docker stop web``.


## Paso 2: Agregar un HTML personalizado

De forma predeterminada, Nginx busca en el directorio ``/usr/share/nginx/html`` dentro del contenedor los archivos para servir, por lo tanto, tenemos que poner
nuestro archivo html en este directorio. Esto se puede hacer con un volumen montado, que permite vincular un directorio en nuestra máquina local y asignar ese 
directorio a nuestro contenedor en ejecución.

Crearemos una página html personalizada y luego la publicaremos usando la imagen Nginx.

Primero hay que crear un directorio llamado ``nginx`` dentro de ``Documentos`` y dentro de este otro, llamado ``site-content``. 
Dentro de ``site-content`` agregamos el archivo ``index.html`` , que tiene el siguiente contenido : 

![2](https://user-images.githubusercontent.com/91699247/168839021-7dbd1177-faaf-46f2-8692-0e19b01907bc.png)



Con el siguiente comando, podemos crear un volumen y montará nuestro directorio local ``~/Documentos/nginx/site-content`` localmente en el contenedor en ejecución 
en ``/usr/share/nginx/html`` :
```
docker run --rm -d -p 8080:80 --name web -v ~/Documentos/nginx/site-content:/usr/share/nginx/html nginx
```

Como podemos ver si accedemos a localhost:8080, aparece nuestra página personalizada :


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

El comando de compilación le dirá a Docker que ejecute los comandos ubicados en nuestro Dockerfile


Ahora podemos ejecutar nuestra imagen en un contenedor, pero esta vez no tenemos que crear un volumen para incluir nuestro html pues ya lo hacemos en el Dockerfile
al copiar el archivo.

```
docker run --rm -d -p 8080:80 --name web webserver
```

Por último vemos nuestra página personalizada, que se ha conseguido desplegar correctamente sobre un servidor web dockerizado Nginx .
