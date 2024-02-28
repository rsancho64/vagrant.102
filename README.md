## previo

https://www.digitalocean.com/community/tutorials/how-to-install-ruby-on-rails-with-rbenv-on-ubuntu-22-04

TOC

- [previo](#previo)
- [¿Qué es Vagrant y para qué sirve?](#qué-es-vagrant-y-para-qué-sirve)
- [**Primeros pasos con Vagrant**](#primeros-pasos-con-vagrant)
  - [**Instalación y creación de una MV básica**](#instalación-y-creación-de-una-mv-básica)
  - [**Acceso a la MV**](#acceso-a-la-mv)
  - [Parando la MV](#parando-la-mv)
- [Vagrant file: archivo para configuración de máquinas virtuales con Vagrant](#vagrant-file-archivo-para-configuración-de-máquinas-virtuales-con-vagrant)
  - [Modificaciones de la MV](#modificaciones-de-la-mv)
  - [Vagrantfiles modificados para virtualización de máquinas](#vagrantfiles-modificados-para-virtualización-de-máquinas)
  - [Configurar MV disco adicional de 500 GiB](#configurar-mv-disco-adicional-de-500-gib)
- [Otras funcionalidades de Vagrant:](#otras-funcionalidades-de-vagrant)
- [Rendimiento de Vagrant](#rendimiento-de-vagrant)
- [Alternativas a Vagrant](#alternativas-a-vagrant)

## ¿Qué es Vagrant y para qué sirve?

Vagrant es un sw de código abierto que permite crear/mantener dev-envs portables; puede trabajar con VMware, VirtualBox, Hyper-V, KVM, AWS e incluso contenedores Docker, por tanto, es ideal para simplificar la configuración de estos sw de virtualización. Escrito en Ruby, permite utilizar otros LPs sin problema.

Los mencionados **archivos de configuración son denominados Vagrantfiles**, estos Vagrantfiles pueden ser compartidos entre
desarrolladores para replegar en sus equipos máquinas virtuales ya creadas. La utilización de Vagrant en dev es útil en equipos -varias personas-: permite garantizar que todos los integrantes trabajan con el mismo dev-env.

Además de solucionar problemas de compatibilidades de sw con algunos OSs, nos permite que nuestros proyectos vayan acompañados de su entorno configurado, ya que los ficheros de configuración son archivos de texto plano y pueden ser versionados en plataformas como Git o subversión. Esto permite incorporar personas en un proyecto comenzado: tan solo descargar del repositorio, el proyecto, y ejecutar la orden Vagrant, con ello ya tendría listo su entorno de dev para trabajar en el equipo.

Por defecto el comando Vagrant trabaja con VirtualBox, VirtualBox es un sw de virtualización para la creación de máquinas virtuales. Pero Vagrant no nos limita solo a VirtualBox, podemos usar también VMware Workstation en Windows y VMware Fusion en MacOS, pero en MacOS además necesitamos un plugin de pago. A veces, algunas Boxes (entornos creados con Vagrant), podemos ejecutarlos en Parallels Desktop, otro asistente de virtualización pero este es de pago.

En cuanto a la arquitectura de Vagramt, utiliza «Provisioners» y también «Providers» como bloques de construcción para los dev-envs. Estos «Provisioners» son herramientas para que los usuarios puedan personalizar su configuración en entornos virtuales. Los «Providers» son los servicios que usa Vagrant internamente para configurar y crear los entornos virtuales. Un detalle importante es que Vmware y AWS son soportados a través de plugins, pero no de forma nativa.

Otro aspecto muy destacable de Vagrant es la gran comunidad que hay detrás de esta herramienta, podremos acceder a una sección de «Docs» donde tendremos toda la documentación de la herramienta explicada en detalle, además, también tenemos una sección de «Community» donde podremos poner en los foros nuestras dudas. Por supuesto, este proyecto está más vivo que nunca, y tendremos desde la web oficial un enlace al proyecto oficial de GitHub donde podremos acceder al código fuente, ver los fallos o bugs que hay y que se han solucionado, y poner dudas sobre el funcionamiento de una determinada función y mucho más. Por último, Vagrant es ampliamente utilizado por empresas tan conocidas como Mozilla, Expedia, Nokia o Disqus para el dev interno de sus propias herramientas.

## **Primeros pasos con Vagrant**

### **Instalación y creación de una MV básica**

El primer paso, como es habitual, es **descargar e instalar*****Vagrant***. Para ello, vas a tener acceder a su página web
oficial que encontrarás directamente en el siguiente [**enlace**](https://www.vagrantup.com/downloads.html)y además instalar
el proveedor de máquinas virtuales que queramos utilizar, que por defecto será **[*VirtualBox*](https://www.virtualbox.org/wiki/Downloads)**, ya que es gratuito y viene integrado en *Vagrant*.

A Vagrant, además de ser instalado a través de la GUI, podemos instalarlo desde CLI 

linux: distribuciones derivadas de Debian: `sudo apt install vagrant`

Una vez instalado, podremos ejecutar el comando **`vagrant`** para obtener un listado de las opciones. Además de que te servirá para verificar que la app de este sw está instalada correctamente.

Por otro lado, para la creación de una maquína virtual podemos recurrir a su [**web de Boxes**](https://app.vagrantup.com/boxes/search) y elegir la más conveniente en el ejemplo lo haremos con Ubuntu xenial.

```sh
vagrant init ubuntu/xenial64
```

Tras ejecutar estos comandos, `vagrant init` descargará/instalará una MV de VirtualBox con el OS Ubuntu 16.04 LTS 64-bit. Luego genera el fichero de configuración "**Vagrantfile**" (en `.`) y tendrá un contenido similar al siguiente.

```rb
VAGRANTFILE_API_VERSION = «2»
Vagrant.configure(VAGRANTFILE_API_VERSION) do \|config\|
config.vm.box = «ubuntu/xenial64»
end
```

De este fichero hablaremos más en profundidad en las siguientes secciones. En este momento sólo hay que saber que en el vagrantfile
estamos indicando a *Vagrant* que queremos utilizar la imagen 'ubuntu/xenial64*′* como base para nuestra MV.

El segundo comando, **'vagrant up'**, descarga, instala, configura y arranca la MV. *Vagrant* descarga las imágenes base de las máquinas virtuales. En el repositorio de boxes hay multitud de imágenes de diferentes OSs y podremos utilizar la que más se ajuste a nuestras necesidades. Estas imágenes base contienen instalaciones básicas del correspondiente OS, que *Vagrant* clona para crear nuestras máquinas virtuales. Gracias a este modo de funcionamiento, la creación y arranque de las máquinas virtuales es mucho más rápida y sencilla.

Una vez que el comando **'vagrant up'** termine, tendremos la MV arrancada y lista para trabajar con ella. Por lo que será el momento de continuar con el siguiente proceso en cuestión que os vamos a explicar en las siguientes líneas.

### **Acceso a la MV**

Por defecto, Vagrant inicia la MV sin interfaz gráfica. Sin embargo, podemos acceder a ella mediante SSH con el comando «vagrant ssh». Lo que nos permitirá tener acceso a la línea de comandos de la MV y podremos hacer todo lo habitual en una máquina GNU/Linux. Para que te quede más claro, este comando te da la oportunidad de establecer conexión mediante SSH a la MV que has creado.

Además, por defecto, Vagrant configura el directorio actual (el directorio desde donde se ha hecho «vagrant init» y donde está el
fichero Vagrantfile) como un directorio compartido con la MV. Esto quiere decir que todos los ficheros que dejemos en ese directorio, serán accesibles por la MV y viceversa. En la MV, este directorio compartido se encuentra por defecto en la ruta '/vagrant'. Así pues, una vez conectados por SSH con la MV, podemos hacer:

Si ejecutamos el comando «ls», podremos ver, además, todos los ficheros del directorio compartido. Este directorio compartido es de gran utilidad, ya que si configuramos, por ejemplo, nuestra MV como un servidor web, podemos dejar en él los ficheros que queramos que el servidor procese y trabajar sobre ellos desde la máquina "host". 

### Parando la MV

Una vez que hayamos terminado de trabajar con la máquina podemos ejecutar los siguientes comandos:

- «vagrant suspend»: Pausa la MV, guardando el estado actual en el disco duro. Permite arrancar de nuevo la máquina muy
    rápidamente con «vagrant up» con el estado exacto en el que se quedó.
- «vagrant halt»: Realiza un apagado controlado de la MV (igual a apagar una máquina física). Como en el caso anterior,
    podemos volver a arrancar la MV con «vagrant up», aunque en este caso el arranque es más lento que al hacer un
    «suspend» (ya que tiene que volver a iniciar el OS).
- «vagrant destroy»: Destruye la MV y todo su contenido.

Como podemos observar en estas breves indicaciones Vagrant nos permite realizar entornos virtuales muy sencillos con dos o tres comandos. Ahora os vamos a enseñar cómo modificar nuestro fichero vagrantfile, para que nuestra máquina posea muchas más funcionalidades. Además, podremos combinar «vagrant» con otras herramientas para que consigamos así un entorno de dev elaborado.

## Vagrant file: archivo para configuración de máquinas virtuales con Vagrant

La configuración de un escenario concreto se realiza de forma bastante simple mediante modificaciones en este fichero, que está escrito en formato Ruby. Realmente la configuración que se aplica es la aplicación en serie de varios Vagranfiles, tal como se explica en **Load Order and Merging**, aunque lo más habitual es que se cargue el Vagrantfile que incluye el box y el que exista en el directorio de trabajo, siendo este último el que se modifica en la mayoría de los casos.

### Modificaciones de la MV

Se configuran en el espacio de nombres «config.vm», prefijo que antecede a los parámetros en este caso, por ejemplo, para modificar el hostname de la máquina utilizaríamos:

`config.vm.hostname = "redeszone"`

El resto de parámetros que se pueden modificar los encontramos en la documentación de **Vagrant: Machine Settings**, dejando en nuestro caso para secciones posteriores algunos de los aspectos que necesitan más dev, como la configuración de la red, o la configuración integrada de la MV mediante shell scripts o mediante aplicaciones como ansible o puppet.

Parámetros relativos a las características de hardware de la MV dependen del proveedor en Vagrant, y en el caso de VirtualBox se definen mediante una subsección, veamos de forma prácticas algunos ejemplos de configuración de los ficheros Vagrantfiles.

### Vagrantfiles modificados para virtualización de máquinas

Realiza las modificaciones apropiadas en un Vagrantfile para cambiar el nombre de la MV, la memoria RAM asignada y el número de núcleos virtuales.

```rb
> config.vm.provider «virtualbox» do \|vb\|
> vb.name = «nombre»
> vb.memory = «512»
> vb.cpus = 2
> end
```

Como la forma habitual de gestionar máquinas virtuales en Vagrant es mediante la línea de comandos, y accediendo a ellas a través de SSH, no tiene mucho sentido que se arranque una interfaz gráfica, pero en algunas ocasiones es conveniente. Modifica un fichero Vagrantfile para que se inicie la interfaz gráfica de usuario al levantar la máquina.

```rb
> config.vm.provider «virtualbox» do \|vb\|
> vb.gui = true
> end
```

Aprovisionamiento ligero (thin provisioning) es una técnica muy utilizada en diferentes sistemas de virtualización, y consiste en crear un disco de imagen de MV que incluya sólo las modificaciones respecto a una imagen base, consiguiendo un ahorro
significativo de espacio en disco a costa de una pequeña penalización en rendimiento. Configura un Vagrantfile para que se realice
aprovisionamiento ligero.

```rb
> config.vm.provider «virtualbox» do \|vb\|
> vb.name = «ligera»
> vb.linked_clone = true
> end
```

Por otra parte, si se quiere ejecutar sobre una máquina el bloque de aprovisionamiento, el comando a usar es 'vagrant provision'. Aunque la configuración completa de las redes lo dejamos para una sección posterior, una funcionalidad muy útil y sencilla es la redirección de puertos de la red por defecto que utiliza Vagrant (red interna con NAT). Configura un Vagrantfile para que las peticiones al puerto 8080/tcp de la máquina anfitriona se redirijan al puerto 80/tcp de la MV.

La documentación completa se encuentra en **[Forwarded Ports](https://www.vagrantup.com/docs/networking/forwarded_ports.html)**.

`config.vm.network "forwarded_port", guest: 80, host: 8080`

Podemos ver en todo momento los puertos que se has redireccionado con la instrucción:

`vagrant port`

Estos cambios se pueden realizar sobre una máquina ya funcionando y para
que se apliquen se utiliza la opción:

`vagrant reload`

En algunas ocasiones, Vagrant no ofrece directamente la posibilidad de hacer cierta configuración específica en la MV, por lo que pierde parte de su atractivo, sin embargo, esto puede solucionarse utilizando comandos, en el caso de utilizar VirtualBox, incluyendo en el fichero Vagrantfile comandos de VBoxManage, como en el siguiente.

### Configurar MV disco adicional de 500 GiB

Editamos el fichero Vagrantfile e incluímos las líneas:

```rb
> config.vm.provider «virtualbox» do \|vb\|
> file_to_disk = 'tmp/disk.vdi'
> unless File.exist?(file_to_disk)
> vb.customize \['createhd',
> '--filename', file_to_disk,
> '--size', 500 \* 1024\]
> end
>
> vb.customize \['storageattach', :id,
> '--storagectl', 'SATAController',
> '--port', 1,
> '--device', 0,
> '--type', 'hdd',
> '--medium', file_to_disk\]
> end
```

En el directorio de trabajo podremos ver que se ha creado un fichero en formato vdi.

```sh
> ls -lh
> -rw-------    1 alejandrojosecaraballogarcia staff 3,0M feb 14 13:50
> disk.vdi
```

Y desde la MV veremos un disco adicional de 500GiB:

> NAME   MAJ: MIN    RM   SIZE     RO     TYPE MOUNTPOINT
> sda    8:0                0       40 G     0      disk
> └─sda1 8:1                0        40 G     0     part
> /
> sdb    8:16              0       500 G    0     disk


NOTA: Este tipo de configuraciones en las que se pone de forma explícita las características de la MV, no son ni mucho menos generales, en el caso anterior se está poniendo de forma concreta el puerto SATA al que conectar el disco y el nombre del controlador SATA, características que pueden variar de una MV a otra. Suele ser conveniente obtener previamente información de las características de la MV, que en el caso de VirtualBox se puede hacer con el siguiente comando de VBoxManage:

`VBoxManage showvminfo NOMBREDELAMV`

Para finalizar veremos la opción «provision», esta opción nos permite la ejecución de comandos en la terminal durante la activación de la máquina, es decir en el momento que nosotros hagamos «vagrant up» y se vayan ejecutando las configuraciones del Vagrantfile, en el momento de llegar a «provision» ejecutará estas órdenes en la terminal. Esto es útil cuando queremos levantar nuestra MV con distintos programas ya instalados, por ejemplo, podríamos poner lo necesario en provisión para que nuestra MV levante un servidor Apache.

```rb
vb.vm.provision «shell», inline: <<-SHELL
    apt-get update
    apt-get upgrade
    apt-get install apache2 -y
SHELL
```

En la siguiente imagen podréis observar una configuración para una MV que actuaría como Router en una red.

No solo tenemos la opción del «provision» en algunos casos, algunas máquinas deben de ejecutar el mismo «provision» y no por ello debemos de estar repitiéndolo. Podemos crear una variable \$shell con todo el «provision» y en las máquinas ejecutar dicha variable.

## Otras funcionalidades de Vagrant:

- **Arrancar un entorno**: es una de las principales características de Vagrant. Esto nos permite crear una MV en menos de un minuto. En esta también podemos implementar SSH, en cambio no podremos visualizar nada, pues esta máquina se ejecuta sin una interfaz gráfica. Este SSH nos permitirá gestionar una sesión SSH  completa, de forma que podemos interactuar con la máquina y básicamente, hacer lo que queramos. Solo debemos tener cuidado con algunos comandos, pues estos comparten directorio con el directorio host, que contiene Vagrantlife, lo que puede ocasionar la pérdida de archivos.

- **Sincronización de archivos**: con este entorno, podremos sincronizar de forma automática los archivos en la MV.
    De esta forma, se pueden editar en local y ejecutarlos en el entorno de dev.

- **Compartir entornos**: en el momento que tenemos un servidor web que se está ejecutando y es accesible desde la máquina, podemos
    disponer de un entorno de dev que nos facilita compartir y colaborar con otros entornos. Esto se llama Vagrant Share. Esto quiere decir que podremos compartir el entorno con cualquier persona, a la cual daremos una URL que se enruta directamente al entorno, pudiendo conectarse desde cualquier dispositivo.

- **Reconstrucción de entornos**: cuando trabajamos con este tipo de entornos, puede darse el caso de que alguno se deje de usar por un determinado periodo de tiempo. Esto no será problema, pues podremos retomarlo cuando sea necesario. Con el comando «vagrant up», este hará una recreación del mismo, y lo ejecuta.

- **Derribar entornos**: una vez hemos acabado de trabajar con un determinado entorno de dev, podemos detenerlo, apagarlo o
    directamente destruirlo. Si optamos por suspender, esta guardará el estado anterior al momento de realizar la suspensión, de esta forma al querer trabajar de nuevo con ella, hacemos un «up», y ya se ejecuta de nuevo. Si la detenemos esta se parará por completo, de forma que al iniciarla esta se ejecutará como una nueva sesión, y finalmente la destrucción, donde eliminaremos todo rastro que pueda quedar de la máquina.

Tal y como habéis visto, necesitaremos tener conocimientos básicos de bash para configurar correctamente Vagrant y automatizar todo el trabajo de crear un entorno de dev portable, esta herramienta es realmente útil y utilizado ampliamente en empresas donde se hagan diferentes devs.

## Rendimiento de Vagrant

Con Vagrant es importante destacar que puede variar según diferentes factores, como las especificaciones del sistema anfitrión, la
configuración de la MV y la carga de trabajo del entorno de dev. Sin embargo, en general, Vagrant **ofrece un buen rendimiento** y puede ser una opción eficiente para la virtualización de dev-envs. De hecho, por cada una de sus características y funcionalidades se convierte en una de las opciones más completo.

Por otro lado, hay que tener en cuenta que su objetivo principal es proporcionar una configuración consistente y reproducible para los desarrolladores, permitiéndoles crear y desplegar dev-envs portátiles y escalables.

Uno de los aspectos que contribuye al rendimiento de Vagrant es su capacidad para utilizar diferentes proveedores de virtualización, como VirtualBox, VMware y Hyper-V. Estos ofrecen diferentes características y optimizaciones de rendimiento, lo que permite elegir más. Además, Vagrant implementa virtualización ligera utilizando tecnologías como Docker y LXC, lo que permite un inicio y apagado más rápidos de las máquinas virtuales, así como un uso eficiente de los recursos del sistema. Esto se traduce en un rendimiento mejorado y una experiencia de dev más fluida.

Otro aspecto a considerar es la configuración de la MV en sí. Vagrant permite personalizar diferentes aspectos de la
configuración, como la asignación de recursos (CPU, memoria RAM, almacenamiento) y la configuración de red. Ajustar adecuadamente estos parámetros puede tener un impacto significativo en el rendimiento general de la MV y, por lo tanto, en el rendimiento de Vagrant.

Además, Vagrant ofrece opciones de aprovisionamiento, como Ansible, Chef y Puppet, que permiten automatizar la instalación y configuración de sw en la MV. Esto contribuye a un proceso de configuración más rápido y a un mejor rendimiento, ya que evita la necesidad de realizar manualmente todas las tareas de configuración en la MV. Por lo cual estamos ante una herramienta con muy buenas capacidades.

## Alternativas a Vagrant

Siempre que hablamos de alguna herramienta en concreto, es bueno ver cuáles son las alternativas a esta. De esta manera, se pueden tener en cuenta diferentes opciones para saber cuál es la que más se ajusta a las necesidades de cada usuario en particular.

Por lo general, Vagrant es **una herramienta muy completa**, pero puede ser que esta no se ajuste exactamente a lo que nosotros necesitamos. Por lo cual, en Internet nos podemos encontrar muchas alternativas donde alguna, se puede adaptar a nosotros de una forma mucho más cómoda y eficiente. No son pocas las alternativas, por lo cual es algo que requiere de un estudio previo y así, hacer la mejor elección que sea posible. Algunas de las más conocidas son:

- **JIRA:** Se trata de una herramienta de dev de sw, considerada como el número uno dentro del sector. Es utilizada por gran cantidad de equipo, que la utilizan para planificación y construcción de sus proyectos. Sea a pequeña o gran  escala. Esta nos proporciona una versión de prueba, lo cual puede ser perfecto.

- **JIRA Service Management:** Estamos ante una opción muy similar a la anterior, pero más asequible. En cambio, está calificado como uno de los mejores sws, por lo cual tiene gran popularidad entre los usuarios.
- **SpiraTeam:** Se trata de un sistema de gestión del ciclo de vida de las aplicaciones. Se puede encargar de gestionar los requisitos,  lanzamientos, pruebas y todos los problemas que puedan aparecer durante el proyecto.

- **Asana:** Es una de las herramientas más sencillas de utilizar. Se considera una de las mejores a la hora de manejar proyectos a gran escala.

- **GitHub:** Esta permite a los desarrolladores colaboración, revisión y administración mucho más eficiente. Pero esta herramienta, se suele utilizar más como un complemento a las que citamos previamente.

Como puedes ver, son muchas las posibilidades que tenemos a la hora de elegir las mejores herramientas. Lo mejor que podemos hacer, es contrastar lo que necesitamos con las funcionalidades de cada una. De esta forma, tendremos que la mejor se ajusta a nuestras necesidades.

SEE ALSO [THIS](https://www.youtube.com/watch?v=GhYm4IvkLQA)

