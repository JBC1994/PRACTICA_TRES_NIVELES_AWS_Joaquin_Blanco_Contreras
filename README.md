## <p align="center">Índice</p> 
### <p align="right">Joaquín Blanco Contreras, Curso: ASIR2</p>

1.0. [Implementación y Configuración de Balanceador de Carga con Apache en AWS](#Implementación-y-Configuración-de-Balanceador-de-Carga-con-Apache-en-AWS)
-    1.1. [Escenario De La práctica](#Escenario-de-la-práctica)
-    1.2. [¿Qué es una VPC en AWS?](#-¿Qué-es-una-VPC-en-AWS?-)
-    1.3. [Creación de VPC en AWS](#Creación-de-VPC-en-AWS)

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

## ¿Qué es una VPC en AWS?

En este apartado, antes de empezar me gustaría definir lo siguiente, ¿Que es una VPC?
Una VPC (Virtual Private Cloud) en AWS es un servicio que nos permite crear una red virtual aislada en la nube de Amazon.
En términos sencillos, es como nuestra propia red privada en la nube. 
Puedes definir rangos de direcciones IP, crear subredes, configurar tablas de ruteo y controlar las configuraciones de seguridad. 
Una VPC nos brinda el control y aislamiento sobre los recursos en la nube, permitiéndonos lanzar instancias de EC2 (servidores virtuales), bases de datos y otros servicios de AWS dentro de tu entorno virtual personalizado. Es fundamental para organizar y asegurar los recursos en la nube de acuerdo con tus necesidades específicas.

## Creación de VPC en AWS

Bien, dicho lo anterior y poniéndonos un poco en contexto, creo que ya podemos empezar




