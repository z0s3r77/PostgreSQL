# PostgreSQL

A continuación, se mostrará como se crean dos contenedores en Docker. Uno con PostgreSQL y otro con PGadmin, que es la interfaz gráfica para tratar con PostgreSQL. 

#### Volumen:

Una vez instalado Docker, crearemos un volumen para hacer persistir los datos de las bases
en nuestro host. De esta forma insertamos el siguiente comando:
```
     docker volume create postgresqldata
```

#### Contenedor PostgreSQL:

Para montar el primer contenedor con la imagen de Postgres, primero lo enlazaremos al volumen “postgresqldata”. Segundo, le pondremos los datos del usuario y contraseña para acceder al gestor y por último, conectaremos nuestro puerto 5432 al puerto 5432 que es el que utiliza Postgres por defecto.

```
      docker run -d -v postgresqldata:/data/db -e POSTGRES_PASSWORD=postgres --name contenedor_postgres -p 5432:5432 postgres
```
 
Enviamos el contenedor al background con CTRL + L o simplemente abrimos otra terminal y ejecutamos: 

       docker exec -it contenedor_postgres /bin/bash
  
Una vez dentro ejecutamos: 

       psql -h localhost -U postgres
       postgres=#
  
  
#### Contenedor PGadmin:

Ahora vamos a proceder a montar un contenedor con PgAdmin, reutilizaremos el comando anterior:

```
docker run -p 2345:80 \
    -e 'PGADMIN_DEFAULT_EMAIL=user@domain.com' \
    -e 'PGADMIN_DEFAULT_PASSWORD=SuperSecret' \
    --name contenedor_pgadmin \
    -d dpage/pgadmin4
```

(Si el puerto del Host está ocupado, no se inicia el contenedor, en mi caso he puesto el 2345)

Una vez tengamos los dos contenedores funcionando, antes de vincularlos en una misma red docker, si nos dirigimos a nuestro navegador he introducimos “http://localhost:2345/login”. Veremos que se nos abrirá la página inicial de PgAdmin4. Debemos introducir el puerto 2345 porque es el que bindeamos con el puerto 80 del contenedor.
El usuario y contraseña es el de los comandos anteriores:

![imagen](https://user-images.githubusercontent.com/80277545/146464077-886cbd24-efb8-4a23-9afa-bd418dedb124.png)



#### MONTANDO LA RED DE DOCKER:

Para que los dos contenedores puedan establecer una conexión deben estar en la misma red, de lo contrario, al intentar introducir las credenciales en nuestro pgadmin, nos dirá que no encuentra el host. Llegados a este punto debemos introducir el siguiente comando:

```

       docker network create --driver bridge red_postgres
       docker network ls
```

Ahora tendremos que conectar los contenedores a la red con:
```
       docker network connect red_postgres contenedor_pgadmin
       docker network connect red_postgres contenedor_postgres
```

#### MONTAR EL SERVER DE POSTGRES EN PGADMIN:

Debido a que establecimos la conexión entre ambos contenedores, ahora vamos a
configurar un nuevo servidor en PgAdmin:

![imagen](https://user-images.githubusercontent.com/80277545/146463640-ad2495dc-d409-428c-b347-429ca86adc19.png)

![Captura de pantalla 2021-12-17 002658](https://user-images.githubusercontent.com/80277545/146463850-431368ae-9d5a-45bf-8694-787f9d37aa14.png)

_Maintenance database: postgres
_Username: postgres
_Password: postgres 
_Name: Postgres 

Guardamos. 

Pulsamos en “Save” y con esto estaría concluido la instalación de Postgres y PgAdmin4 mediante Docker:
![imagen](https://user-images.githubusercontent.com/80277545/146463999-878e0f28-a854-4c92-8d4d-8736274073cb.png)


Dudas: ipopdue24@gmail.com
