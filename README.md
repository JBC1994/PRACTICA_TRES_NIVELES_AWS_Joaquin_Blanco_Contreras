## <p align="center">Índice</p> 
### <p align="right">Joaquín Blanco Contreras, Curso: ASIR2</p>

1.0. [Implementación y Configuración de Balanceador de Carga con Apache en AWS](#Implementación-y-Configuración-de-Balanceador-de-Carga-con-Apache-en-AWS)
-    1.1. [Escenario De La práctica](#Escenario-de-la-práctica)
-    1.2. [¿Qué es una VPC en AWS?](#Qué-es-una-VPC-en-AWS)
-    1.3. [Creación de VPC en AWS](#Creación-de-VPC-en-AWS)
-    1.4. [¿Qué es una EC2 en AWS?](#Qué-es-una-EC2-en-AWS)
-    1.5. [Creación de EC2 en AWS](#Creación-de-EC2-en-AWS)

2.0. [¿ Qué es una IP elástica en AWS ?](#Qué-es-una-IP-elástica-en-AWS) 
-    2.1. [Asociar una IP elástica a una instancia AWS](#Asociar-una-IP-elástica-a-una-instancia-AWS)
-    2.2. [Conexión a instancias EC2 AWS](#Conexión-a-instancias-EC2-AWS)

3.0. [Instalación de servicios en instancias AWS](#Instalación-de-servicios-en-instancias-AWS)
-    3.1. [Configuración de conectividad en instancias AWS](#Configuración-de-conectividad-en-instancias-AWS)
-    3.2. [Creación de dominio en NO-ip](#Creación-de-dominio-en-NO-ip)
-    3.3. [HTTPS con Let’s Encrypt y Certbot en instancia Balanceador AWS](#HTTPS-con-Let’s-Encrypt-y-Certbot-en-instancia-Balanceador-AWS)
-    3.4. [Configuración Balanceador AWS](#Configuración-Balanceador-AWS)
-    3.5. [Repositorio GitHub, con SCP en AWS para instancias backend y MariaDB](#Repositorio-GitHub-con-SCP-en-AWS-para-instancias-backend-y-MariaDB)

4.0. [Pila Lamp en marcha](#Pila-Lamp-en-marcha) 
-    4.1. [Consulta desde cliente Apache2 A Servidor MariaDB](#Consulta-desde-cliente-Apache2-A-Servidor-MariaDB)
-    4.2. [Comprobación de consulta con servidor MariaDB](#Comprobación-de-consulta-con-servidor-MariaDB)




## Implementación y Configuración de Balanceador de Carga con Apache en AWS

Bien, como vimos en prácticas anteriores las cuales se pueden ver en mis post abiertos de github, está no será menos, y será una continuación de la anterior (Práctica lamp dos niveles). 
En este caso proponemos una práctica distinta, y más profesional según lo solicitado en empresas. 
Está práctica tendrá en común varias herramientas con las que hemos trabajado ya, y sino lo hemos echo, las aprenderemos.
Tratará sobre un "Balanceador de carga con Apache" esté nos permitirá aceptar todas las peticiones que haga un cliente, y a su vez gestionarla con varios host apaches para balancear la carga de uno a otro, y así no sobrecargar el servidor principal. Ésto muy útil porque a parte de gestionar mejor el tráfico de solicitudes, si uno falla, el otro seguirá activo. Obviamente, como hemos comentado en el título está práctica se hará completamente en la plataforma de AWS.


## Escenario De La práctica

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/52d5c681-1583-46a8-ac37-54829641848f)

Bien, según el escenario mostrado, nuestra práctica tratará sobre: Un Balanceador de carga, dos servidores apache2, un servidor mariadb-server.
En cada uno de ellos tendremos sus correspondientes configuraciones, pero todo ello en nuestro ecosistema de AWS. 
Trabajaremos con 2 subredes, una de ella la haremos publica y la otra obviamente quedará como privada. Con la pública haremos NAT para poder tener salida a internet junto con una IP Elástica que AWS nos proporcionará.
Tendremos que controlar que nuestro Balanceador tendrá acceso a internet y además deberá poder conectarse sin problemas a los host de backend apache2. Pero ¡NO! con nuestro servidor MariaDB.
De igual manera, nuestros servidores backend apache2 se podrán conectar a nuestra máquina "Balanceador", estás máquinas no tendrán acceso a internet, y sí podrán conectarse con el servidor MariaDB.
Nuestro servidor MariaDB solo podrá conectarse a nuestros backend y de igualmanera no tendrán acceso a internet.
**(NOTA).-** ***Cuando decimos que no tendrán acceso a internet, será cuando terminemos de instalar los servicios correspondientes.***

## Qué es una VPC en AWS

En este apartado, antes de empezar me gustaría definir lo siguiente, ¿Que es una VPC?
Una VPC (Virtual Private Cloud) en AWS es un servicio que nos permite crear una red virtual aislada en la nube de Amazon.
En términos sencillos, es como nuestra propia red privada en la nube. 
Puedes definir rangos de direcciones IP, crear subredes, configurar tablas de ruteo y controlar las configuraciones de seguridad. 
Una VPC nos brinda el control y aislamiento sobre los recursos en la nube, permitiéndonos lanzar instancias de EC2 (servidores virtuales), bases de datos y otros servicios de AWS dentro de tu entorno virtual personalizado. Es fundamental para organizar y asegurar los recursos en la nube de acuerdo con tus necesidades específicas.

## Creación de VPC en AWS

Bien, dicho lo anterior y poniéndonos un poco en contexto, creo que ya podemos empezar a desarrollar dicha práctica.
Daré por echo que obviamente tenemos acceso a AWS y a un laboratorio para poder trabajar con la plataforma. 
Bien, una vez dentro de nuestro laboratorio, accederemos a **"VPC"**.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/3633438b-1973-475e-8e87-bf4ad65dcb9f)

Dentro de VPC le daremos a **"Crear".**

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/f1bbae62-0e55-4d50-8638-43da20859e3d)

Nos saldrá el siguiente menú, el cúal tendremos que configurar según nuestro escenario indicado.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/2ba33c2a-eb6b-4327-8c77-0cb8bdab28e3)
![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/25ff5a87-f446-47e0-92aa-1c3651d6f19b)
![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/4e2580b7-e33f-4c98-8c9c-60d8318c0506)
![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/5ea63da9-952e-4c08-900d-0185a8f4df93)

Bien, hasta aquí daremos por finalizada la configuración y creación de una **VPC**.

## Qué es una EC2 en AWS 

Una EC2 en AWS nos proporciona la capacidad de cómputo que necesitamos en la nube.
Con este servicio, podemos lanzar y gestionar servidores virtuales, llamados instancias EC2, para ejecutar diversas aplicaciones y cargas de trabajo. 
Esto nos ofrece la flexibilidad y escalabilidad necesarias en nuestro entorno de la nube de AWS.

## Creación de EC2 en AWS

Teniendo en cuenta el apartado anterior, y comprendiendo mejor lo que significa las siglas **EC2**, vamos a proceder a crear tantas instancias como máquinas nos solicitan en la práctica.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/3633438b-1973-475e-8e87-bf4ad65dcb9f)

Para tener una mejor organización de las instancias, lo correcto sería darles nombres identificativos.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/c814e664-3f9e-41ff-900a-6abdc0cc610a)

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/407ccd01-eca8-4ee9-bad6-77cc22561151)

Por no tener tanta redundancia de fotos enseñando lo mismo, quedaros con las configuraciones que veis en dichas fotos. No es más que eso, y tan solo, deberemos de crear tantas instancias como máquinas nos piden en el esquema, que son cuatro.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/d219559a-4f82-4315-ad6c-281663ca24e7)

## Qué es una IP elástica en AWS

La gran pregunta aquí es, ¿Qué es una IP elástica en AWS?
Una IP elástica en AWS es una dirección IP persistente que puedes asignar y reasignar a tus recursos en la nube, como instancias EC2. A diferencia de las IP estáticas tradicionales, las IP elásticas nos permiten mantener la misma dirección incluso si detienes y reiniciamos nuestra instancia, proporcionando estabilidad para aplicaciones y servicios de la nube.

## Asociar una IP elástica a una instancia AWS

Para asociar una IP elástica a nuestra instancia tenemos que pensar que en este caso lo lógico sería asociarsela a nuestro servidor **"Balanceador"**, que será través del cual podamos acceder de manera remota y poder configurar el resto de máquinas conectandonos por **SSH**, primero a nuestro Balanceador y de nuestro Balanceador a las otras máquinas.
Nos iremos a: **VPC** , "ASOCIAR DIRECCIÓN IP ELÁSTICA" y de ahí escogeremos la de nuestro servidor "Balanceador", en este caso, la ip que le corresponde es **192.168.1.5**.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/e0ba1010-eb17-459e-bb7a-b67df94e8b9d)

Ahora si nos vamos a nuestra máquina balanceador, veremos como tenemos nuestra "IP PÚBLICA" asociada.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/63ab5542-1a1b-4a31-8fa8-80b6da215b20)

## Conexión a instancias EC2 AWS

Teniendo en cuenta que ya tenemos todas nuestras instancias, trataremos de configurarlas, pero para ello lo ideal sería conectarnos a ellas por ssh. 
Pero, ¿Como hacemos para conectarnos por SSH a ellas? bien, nos iremos a nuestro laboratorio, de ahí nos iremos a **AWS-Details** Descargaremos la clave **"PEM"**.
Recordemos guardar dicha clave en nuestro lugar de trabajo en el cual solemos trabajar.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/2d6bc954-0501-471a-a924-6b4841052e29)

En mi caso se encuentra en una carpeta que he creado donde almaceno mis prácticas.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/1cee00a3-bf8c-4a58-814f-e30bbc2a71f0)

Bien, una vez guardada nuestra clave, abrimos una nueva "Terminal", os recomiendo abrir 4 para tener una mejor organización, así identificais cada una de las máquinas.  

    Comando utilizado: ssh -i labsuser.pem admin@ec2-3-228-114-171.compute-1.amazonaws.com ("admin"@"Aquí tendremos que poner nuestra DNS que nos proporcionó AWS")
    
![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/907d2ea8-b5ff-420a-9a32-ff3bc91ed12d)

como vemos la conexión con nuestro servidor ha sido un éxito.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/9ee630d2-55ae-4be4-8800-3d347be46880)

Una vez aquí, ¿porque no pensamos en como poder conectarnos a las demás máquinas? Pues debemos de tener una clave de igual manera para poder conectarnos a ellas. 
Bien, solucionemos esto de la siguiente manera, ejecutaremos el siguiente comando en nuestra **Terminal**.

    Este comando nos permite copiar nuestra clave del directorio actual a nuestra máquina Balanceador, para que desde ahí podamos hacer el mismo proceso con las demás máquinas.
    scp -i labsuser.pem labsuser.pem admin@ec2-3-228-114-171.compute-1.amazonaws.com: /home/admin
    (Recordemos que aquí: admin@"Poner nuestra DNS pública).

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/c99e7f26-ee6a-4327-89c6-1a7dc2d5fa93)

Ahora vemos como en nuestra maquina balanceador tenemos la clave copiada. 

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/f43f71ab-65f4-43f8-8bd8-e7be5c5ba0ad)

Recordemos que para que nos podamos conectar a las demás maquinas tenemos que ejercer permisos a nuestra clave, sino no habrá forma de poder conectarnos a las demás maquinas. 

    Este sería el comando: chmod 400 labsuser.pem
    Ahora ejecutariamos este otro:
    ssh -i labsuser.pem admin@192.168.1.135 (IP de la maquina a la cual nos queremos conectar, en mi caso JoaquinApache_Maquina1)

Ahora copiaremos dicha clave a las demás máquinas, de nada nos sirve conectarnos a una solo.

    scp -i labsuser.pem labsuser.pem admin@192.168.1.135:/home/admin labsuser.pem (Le hemos pasado la clave "labsuser.pem" a la maquina Apache)
    
Este paso lo repetiremos con las distintas instancias que tengamos.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/37779568-7285-4a9a-9f41-df8449767d36)

Hasta aquí, ¿bien, no? pero tal y como hemos dicho antes, esto no es factible, lo correcto seria tener 4 terminales de windows abiertas, ingresando como **Balanceador**, y de ahí ir pasando de máquina a máquina por ssh. 

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/eb170864-f076-495b-9f53-9dbe14675d16)

## Instalación de servicios en instancias AWS

En este apartado trataremos de descargar los servicios que necesitare en nuestras 4 instancias AWS creadas.
Bien, para ello doy por echo que tenemos nuestras 4 ventanas abiertas. 
Pero... , ¿Cómo pensamos descargar los servicios si las instancias, excluyendo la del 'Balanceador' al que asignamos la IP elástica, no tienen acceso a internet?
Bien, para que estas instancias tengan internet haremos lo siguiente, ¡OJO! Esto solo nos interesará hacerlo para descargar los servicios correspondientes a la práctica, despues obviamente si queremos seguir el escenario de red indicado tendremos que deshabilitarselo.

Nos vamos a nuestras instancias EC2, seleccionamos en este caso la de "JoaquinApache2_Maquina1" (En mi caso).
![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/3ca68500-4e62-4902-a12a-4ffcbee8fb0c)

En la parte inferior nos iremos al apartado de "Seguridad" y seleccionaremos el grupo que tenemos asociado a la instancia.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/d16c7929-2929-419d-b455-867f42dbf1b2)

Una vez ahí, le daremos a "Reglas de salida" y agregaremos una nueva regla de salida. Elegiremos "Tráfico de red TODO" y que el destino sea 0.0.0.0 .

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/ee261c3d-ac01-45e0-a569-ab69bab5109b)

Bien, una vez agregada dicha opción esta máquina apache deberá de tener insternet sin problema alguno. (Haremos lo mismo con las demás), mi consejo es que apunteís bien lo que se hace en cada instancia.

Empezamos con la descarga de servicios, en cada una de las instancias.

    Recordemos:
    Balanceador: 
    1) sudo apt-get -y update   (Actualizamos repositorios).
    2) sudo apt-get install -y apache2 (Instalamos los servicios **Apache2** aceptando los mensajes de confirmación en la instalación con **-y**.).
    3) sudo apt-get install -y git (Instalamos los servicios **Github** aceptando los mensajes de confirmación en la instalación con **-y**.).
    4) sudo apt-get install -y php libapache2-mod-php php-mysql (Instalamos el servicio **php** aceptando los mensajes de confirmación de instalación con **-y**.).
    JoaquinApache2_Máquina1:
    1) sudo apt-get -y update   (Actualizamos repositorios).
    2) sudo apt-get install -y apache2 (Instalamos los servicios **Apache2** aceptando los mensajes de confirmación en la instalación con **-y**.).
    3) sudo apt-get install -y php libapache2-mod-php php-mysql (Instalamos el servicio **php** aceptando los mensajes de confirmación de instalación con **-y**.).
    4) sudo apt-get install -y mariadb-client-10.3  (Instalamos el servicio **MariaDB-Cliente** aceptando los mensajes de confirmación de instalación con **-y**.).
    JoaquinApache2_Máquina2:
    1) sudo apt-get -y update   (Actualizamos repositorios).
    2) sudo apt-get install -y apache2 (Instalamos los servicios **Apache2** aceptando los mensajes de confirmación en la instalación con **-y**.).
    3) sudo apt-get install -y php libapache2-mod-php php-mysql (Instalamos el servicio **php** aceptando los mensajes de confirmación de instalación con **-y**.).
    4) sudo apt-get install -y mariadb-client-10.3  (Instalamos el servicio **MariaDB-Cliente** aceptando los mensajes de confirmación de instalación con **-y**.).
    JoaquinMariaDB:
    1) sudo apt -y update (Actualizamos repositorios).
    2) sudo apt install -y mariadb-server-10.3 (Instalamos el servidor de **MariaDB-Server** aceptando los mensajes de confirmación en la instalación con **-y**.).

Ahora descargado ya todos los servicios el procedimiento será el mismo que hicimos pasos mas arriba... Quitaremos las reglas de salida a internet de todas las máquinas menos la de **Balanceador**.


## Configuración de conectividad en instancias AWS

Para comprobar la conectividad que tenemos entre máquinas por ejemplo haciendo ping entre ellas, tendremos que tener en cuenta que las instancias de AWS siguen una regla de entrada y salida las cuales tenemos que trabajar. 
En este caso, según el esquema de red y condiciones dichas anteriormente necesitamos tener conectividad en local entre máquinas Balanceador y instancias backend Apache2, pero no con el servidor MariaDB.
***NOTA:*** Tendremos que tener en cuenta, que tanto al servidor Balanceador como a los server backend se les enviará una petición por los puertos 80 o 443, además del tráfico interno de la red.
Configuración servidor Balanceador:

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/eff09c13-98ae-45b3-a047-de94130f21f2)
![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/c1c1db64-80ef-4fec-be83-2a02a40d75f7)

Configuración backend Apaches:

Aquí estmaos agregando que todo tráfico de entrada 192.168.1.0/24, tanto de servidor Balanceador y Server MariaDB puedan tener ping a esta máquina. El puerto 3306 es por donde escucha el servidor MariaDB, así qué directamente lo metemos en su grupo.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/084dba76-3d97-4a74-b0eb-7968f81ab24e)

Configuración Servidor MariaDB:

Habilitamos el tráfico de salida a los grupos backend para que solo tenga ping y respuesta con ellos, en la entrada igual, y agregamos el puerto 3306 con el que trabaja MariaDB.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/07046b13-3e8e-45ad-960e-d9f9d4864a1e)

**PRUEBA DE CONECTIVIDAD**

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/ab2a3184-cc3d-4691-ac00-da9754158c28)

## Creación de dominio en NO-ip

Llegados hasta aquí, y tenga sentido nuestra práctica, lo aconsejable sería tener nuestro propio dominio, asociando esté a la dir ip pública que nos proporcionó nuestra plataforma AWS.
Recordemos la IP pública...

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/50d4e24a-9f1d-4d57-8c22-8e3ccf3a75c1)

Bien, esa dirección ip la asociaremos a nuestro nombre de dominio.
En mi caso, me he creado el dominio en la pag de: https://my.noip.com/ , os creáis una cuenta y la configurais a vuestro gusto.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/7c88d13b-33a3-4f55-bb37-31363766aec9)

Una vez creado el nombre de dominio y asignando este a nuestra dirección ip pública avanzaremos un poquito más en nuestra práctica.

## HTTPS con Let’s Encrypt y Certbot en instancia Balanceador AWS

Este certificado nos asegurará trabajar con una identidad certificadora la cual nos permitirá asociar nuestro nombre de dominio a un certificado seguro. 
Bien, empecemos, en nuestra instancia Balanceador, instalaremos el siguiente servicio.

    COMANDOS:
        sudo apt install snapd
        sudo snap install core
        sudo snap refresh core
        sudo apt-get remove certbot
        sudo snap install –classic certbot
        sudo ln -s /snap/bin/certbot /usr/bin/certbot
        sudo certbot –apache
        
    TAMBIÉN HABILITAREMOS LOS SIGUIENTES MODULOS PARA POSIBLES PROBLEMAS:
    sudo a2enmod proxy && sudo a2enmod proxy_http && sudo a2enmod proxy_ajp && sudo a2enmod rewrite && sudo a2enmod deflate && sudo a2enmod headers && sudo a2enmod proxy_balancer && sudo a2enmod         
    proxy_connect && sudo a2enmod proxy_html && sudo a2enmod lbmethod_byrequests 

    REINICIAMOS NUESTRO SERVIDOR:
    systemctl restart apache2  

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/af9ce2fa-d782-4ba9-b164-66b215e34406)

Después de las instalaciones pertenecientes según indicaciones, si nos vamos a nuestro navegador y ponemos el nombre de nuestro dominio, esté se tiene que resolver y apuntar a nuestro apache2 balanceador. En mi caso, hice la consulta de la siguiente manera, https://joaquiniaw.ddns.net .

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/206f61fa-da36-42ac-9f0e-a02c2375833e)

## Configuración Balanceador AWS

Para que nuestro Balanceador funcione como tal, tendremos que hacer lo siguiente. 
Nos iremos a la siguiente ruta: **/etc/apache2/sites-available/**
Una vez ahí, os recomiendo cambiar de nombre el fichero **000-default-le-ssl.conf** por "**Balanceador**" así nos será mucho mas identificativos y prevenimos de posibles problemas. 
**NOTA:** (¡OJO!, ESTOY TRABAJANDO CON UNA COPIA DEL FICHERO, 000-default.le-ssl.conf). Este fichero no lo tocamos para ¡NADA!.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/cf50a48f-6b83-4b0c-8f9a-7c82af625ede)

Bien una vez le hayamos cambiado el nombre hacemos lo siguiente, y escribimos las siguientes instrucciones.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/7e53b13f-397e-495e-897b-265e7e5cbd22)

Si os fijáis en las imágenes he puesto la IP de nuestros servidores backend apaches. Esto nos será muy útil para que en la llamada a nuestro dominio se les redirija a uno u otro. Así como la propia palabra indica, "Balanceamos" el tráfico de peticiones. 

Bien, una vez hecho esto escribimos los siguientes comandos:
Nos vamos a **cd /etc/apache2/sites-enabled/**
Veremos que tenemos 2 habilitados. 
Ahora volvemos de nuevo a, **/etc/apache2/sites-available/**

    a2dissite 000-default.conf
    a2dissite 000-default.le-ssl.conf
    a2ensite Balanceador.conf
    systemctl reload apache2
    
Bien, una vez hecho esto, nuestro servidor Balanceador ya estaría preparado para enviar peticiones a los servidores backend.

## Repositorio GitHub con SCP en AWS para instancias backend y MariaDB

En ese apartado, trabajaremos con el repositorio de nuestro buen amigo, "jose juan sanchez", el cuál tiene un repositorio subido muy sencillo pero que es estupendo para poner a prueba está práctica y nuestros conocimientos.
Os dejamos el link para que podaís clonarlo en vuestras instancias. 

    https://github.com/josejuansanchez/iaw-practica-lamp.git
    
Bien, con este link haremos lo siguiente, en nuestra instancia **"Balanceador"** ejecutaremos el comando: (Recordemos que para hacer esto, tenemos que tener instalado el servicio de Git en nuestra instancia, cosa que os pongo al principio del post).

    sudo git clone https://github.com/josejuansanchez/iaw-practica-lamp.git
    
Esto nos dará como resultado el respositorio descargado en nuestro directorio activo, con el cual estamos trabajando en **Balanceador**.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/e5f312cc-6da2-4098-a6eb-a90104d1812b)

Hasta aquí, ¿Vamos bien, no?

Recordemos lo siguiente, en las máquinas backend y MariaDB tenemos restringido el acceso, ¿Como pasamos ahora la carpeta descargada a cada instancia?
Bien, pues para ello 











