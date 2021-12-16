# PostgreSQL

A continuación, se mostrará como se crean dos contenedores en Docker. Uno con PostgreSQL y otro con PGadmin, que es la interfaz grafica para tratar con PostgreSQL. 

#### Volumen:

Una vez instalado docker, crearemos un volumen para hacer persistir los datos de las bases
en nuestro host. De esta forma insertamos el siguiente comando:

    ·  docker volume create postgresqldata

#### Contenedor PostgreSQL:

Para montar el primer contenedor con la imagen de Postgres, primero lo enlazaremos al volumen “postgresqldata”. Segundo, le pondremos los datos del usuario y contraseña para acceder al gestor y por último, conectaremos nuestro puerto 5432 al puerto 5432 que es el que utiliza Postgres por defecto.

    ·  docker run -d -v postgresqldata:/data/db -e POSTGRES_PASSWORD=postgres --name contenedor_postgres -p 5432:5432 postgres
 
Enviamos el contenedor al background con CTRL + L o simplemente abrimos otra terminal y ejecutamos: 

    ·   docker exec -it contenedor_postgres /bin/bash
  
Una vez dentro ejecutamos: 

    ·   psql -h localhost -U postgres
        postgres=#
  
  
#### Contenedor PGadmin:

Ahora vamos a proceder a montar un contenedor con PgAdmin, reutilizaremos el comando anterior:

    ·    docker run --name contenedor_pgadmin -e
    “PGADMIN_DEFAULT_EMAIL=zoser@postgres.com “ - e
    “PGADMIN_DEFAULT_PASSWORD= admin” -p 5050:80 -d dpage/pgadmin4

Una vez tengamos los dos contenedores funcionando, antes de vincularlos en una misma red docker, si nos dirigimos a nuestro navegador he introducimos “http://localhost:5050/login”. Veremos que se nos abrirá la página inicial de PgAdmin4. Debemos introducir el puerto 5050 porque es el que bindeamos con el puerto 80 del contenedor.


#### MONTANDO LA RED DE DOCKER:

Para que los dos contenedores puedan establecer una conexión deben estar en la mismared, de lo contrario, al intentar introducir las credenciales en nuestro pgadmin, nos dirá que no encuentra el host. Llegados a este punto debemos introducir el siguiente comando:

    ·   docker network create --driver bridge red_postgres
    ·   docker network ls

Ahora tendremos que conectar los contenedores a la red con:

    ·   docker network connect red_postgres contenedor_pgadmin
    ·   docker network connect red_postgres contenedor_postgres


![imagen](https://user-images.githubusercontent.com/80277545/146463640-ad2495dc-d409-428c-b347-429ca86adc19.png)
![Captura de pantalla 2021-12-17 002555](https://user-images.githubusercontent.com/80277545/146463694-c6e60e8b-2d20-41d9-89d7-26c4762a0de2.png)
