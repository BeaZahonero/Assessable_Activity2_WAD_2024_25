# Assessable_Activity2_WAD_2024_25

##  EC2 instance

### 1.1   Explicación
Vamos a crear una instancia EC2 alojada en AWS donde se despliega una aplicación en React con Vite como bundler. 
Además, para poder mantener una IP fija aunque se detenga la instancia, se usa una IP elástica también de AWS enlazada con la EC2.

Para empezar, en el apartado Network & Security, se crea un grupo de seguridad que nos permitirá reglas que controlen todos los accesos.
Se configuran los detalles básicos y las reglas de entrada para los distintos puertos de la red.
Una vez establecido el Security Group, pasamos a crear la instancia EC2

En la siguiente pantalla configuramos los el nombre, qué tipo de AMI (Amazon Machine Image) nos servirá de base, una Ubuntu 24.04 LTS de 64-bit(x86).
El tipo de instancia una t2.micro. 
Para garantizar un login seguro, se emplea Vockey tipo RSA. Esto es un par de claves una pública guardada en la instancia EC2 y otra privada (.pem) descargada desde el Lab en el momento de acceder.
En el apartado de Network settings, seleccionamos como Firewall el Security group que habíamos creado previamente ‘default-web-sg’
Y el tamaño de almacenamiento 8 GiB que viene por defecto. Como número de instancias, solo 1.

A continuación, en el apartado Network & Security / Elastic IPS, se crea una IP elástica.Se configura en la zona de red y se crea “Allocate”.
Para asociar la IP con una instancia EC2, la seleccionamos y completamos su configuración con el Id y la IP privada de dicha instancia.


Con esto, ya tenemos todo lo necesario para acceder desde nuestra máquina local a la instancia EC2 de AWS. Para ello abrimos una consola de comandos y mediante SSH, usando ya la IP elástica, podremos interactuar con la instancia.

Como se trata de una máquina limpia, instalamos todo el software con los siguientes comandos.
- sudo apt-get update (para actualizar los paquetes)
- sudo apt-get install apache2 (para descargar Apache)
- sudo apt-get install npm (para instalar el gestor de paquetes NPM)
- git clone https.//github.com/gisgarme/adivina-el-numero (para clonar el repositorio del proyecto a desplegar)
- Dentro del directorio descargado, se edita el package.json y como ‘homepage → Poner DNS público’
- npm install (para instalar todas las dependencias del proyecto)
- sudo apt-get install -g pm2 (para instalar PM2, el process manager de Node.js)
- pm2 start npm --name "vite-server" -- run dev -- --host (para iniciar el servidor de desarrollo de Vite con PM2)
- npm run build (para compilar y optimizar el código y guardarlo en la carpeta /dist para ser desplegado)

Se mueve el contenido de la carpeta /dist al directorio /secure.
Cambiamos el propietario de la carpeta /secure al usuario por defecto de Apache.
A continuación configuramos un virtual host que permitirá alojar nuestra aplicación De los sitios disponibles de Apache, hacemos una copia que llamaremos ‘secure.conf’.
En ‘secure.conf’ se escribe como ServerName la dirección de la IP elástica de la instancia EC2.

Se deshabilita el sitio por defecto (000-default.conf), se activa el recién configurado y reiniciamos Apache.

Generamos el certificado autofirmado que guarda el par de certificado y llave en sus carpetas. En la configuración insertamos los datos deseados como País, Provincia, etc...

Se edita de nuevo el archivo ‘secure.conf’ para añadir las rutas del certificado.
Activamos el módulo SSL y reiniciamos Apache.
Ya está todo listo para que la aplicación funcione.



