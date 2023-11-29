## <p align="center">Índice</p> 
### <p align="right">Joaquín Blanco Contreras, Curso: ASIR2</p>

1.0. [Implementación y Configuración de Balanceador de Carga con Apache en AWS](#Implementación-y-Configuración-de-Balanceador-de-Carga-con-Apache-en-AWS)
-    1.1. [Escenario De La práctica](#Escenario-de-la-práctica)
-    1.2. [¿ Qué es una VPC en AWS ?](#Qué-es-una-VPC-en-AWS)
-    1.3. [Creación de VPC en AWS](#Creación-de-VPC-en-AWS)
-    1.4. [¿ Qué es una EC2 en AWS ?](#Qué-es-una-EC2-en-AWS)
-    1.5. [Creación de EC2 en AWS](#Creación-de-EC2-en-AWS)

2.0. [Despliegue De La herramienta Vagrant](#Despliegue-de-la-herramienta-Vagrant) 
-    2.1. [Escenario De La práctica](#Escenario-de-la-práctica)
-    2.2. [Carpeta Compartida En Vagrant](#Carpeta-Compartida-En-Vagrant)

3.0. [Descarga De Repositorios](#Descarga-de-repositorios)
-    3.1. [Configuración De Cliente Apache2](#Configuración-de-Cliente-Apache2)
-    3.2. [Configuración De Servidor MariaDB](#Configuración-De-Servidor-MariaDB)

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

Teniendo en cuenta nuestro apartado anterior, y comprendiendo mejor lo que significa las siglas **EC2**, vamos a proceder a crear tantas instancias como máquinas nos solicitan.

![image](https://github.com/JBC1994/PRACTICA_TRES_NIVELES_AWS_Joaquin_Blanco_Contreras/assets/120668110/3633438b-1973-475e-8e87-bf4ad65dcb9f)





