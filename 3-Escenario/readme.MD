
### Equipo de trabajo:
* Jorge Andres Moreno - A00328088
* Andres Varela - A00049016
* Leonardo Cifuentes - A00027552

---

### Escenario 03

En este escenario se hará uso de lo aprendido anteriormente para implementar una aplicación web que haga uso de una base de datos, el sistema debe permitir insertar y listar datos de una tabla en la base de datos. La aplicación debe ser desplegada en un servidor web y la base de datos en un servidor aparte.

### Pre Requisitos
- [VirtualBox](https://www.virtualbox.org/)
- [Vagrant](http://vagrantup.com)
- [Saltstack](https://www.saltstack.com/)

### Entregable

un documento en formato MarkDown (se suguiere el nombre Escenario03.MD) que contenga:

1. Nombres completos y códigos de los estudiantes.
2. Documentación y evidencias del proceso de creación, provisionamiento y despliegue de la aplicación.
3. Dificultades encontradas en el proceso.
4. Conclusiones acerca de los hallazgos y aprendizajes obtenidos en el desarrollo del laboratorio.

---

### Instalación

#### **1. Vagrant.**
Ingresmos al siguiente link:

https://www.vagrantup.com/docs/installation/

En la esquina superior derecha, seleccionamos la opción Download y después seleccionamos el instalador o paquete apropiado para nuestro sistema operativo (en este caso es Lubuntu 19.04).

Para validar la correcta instalación utilizamos el comando dentro de la consola de la maquina que instalamos Vagrant:

~~~
vagrant -v
~~~

#### **2. VirtualBox.**

Ingresamos al siguiente link:

https://vitux.com/how-to-install-virtualbox-on-ubuntu/

Agregamos un repositorio fuente y actualizamos el sistema.
~~~
sudo add-apt-repository multiverse && sudo apt-get update
~~~

Ahora instalamos VirtualBox con el comando.
~~~
sudo apt install virtualbox
~~~

Para abrirlo directamente desde la terminal se escribe:
~~~
virtualbox
~~~

#### **3. Ansible**

Ingresamos al siguiente link:

https://github.com/Jorge-Andres-Moreno/Ansible_examples

y ejecutamos los siguientes comandos para la instalación en lubuntu:

~~~
sudo apt install software-properties-common -y
sudo apt-add-repository ppa:ansible/ansible -y
sudo apt install ansible -y
~~~

Por ultimo validamos si quedo instalado:

~~~
ansible -v
~~~

---

### MySQL

Utilizamos el motor de base de datos de MySQL. Para la instalación creamos el playbook para aprovisionar con ansible y luego integrarlo con el proyecto en Vagrant.

Primero configuramos el nombre del host que lo nombramos **database** .Además, habilitamos el usuario root y metodo sudo para la instalación y no tener problemas de permisos.

~~~
  # web server installation
- hosts: database
  become: yes
  become_user: root
  become_method: sudo
  gather_facts: no
  ~~~

En las tareas de ejecución tenemos primero la instalación de dependencias y librerias necesarias para utilizar MySQL. Además, se verifican si algunas si el servicio de MySQL esta corriendo, para verificar que las librerias y el servicio ha sido instalado correctamente.

~~~
 - name: "updating server"
    apt:
      update_cache: yes
 
  - name: "Installing apt dependencies"
    apt:
      name: "{{ item }}"
      update_cache: yes
    with_items:
      - libmysqlclient-dev
      - mysql-client
      - python-dev
      - python-pip
      - python-mysqldb
      - python3-pip

  - name: Make sure pymysql is present
    become: true # needed if the other tasks are not played as root
    pip:
      name: pymysql
      state: present

  - name: "Installing  mysql server"
    apt:
      name: mysql-server

  - name: Ensure mysql is running and starts on boot
    service:
      name: mysql
      state: started
      enabled: yes
    become: yes
~~~

Ahora configuramos MySQL para que pueda ser visible ante otros usuarios. Ya que, de momento solo esta corriendo en localhost y necesitaremos más adelante conectar el **Web server**.

**NOTA** : la linea **bind-address** por defecto esta en **127.0.0.1** en el archivo de configuración de MySQL. Esto  con lleva a que sólo permita que desde la maquina **host** acceder al servicio de base de datos. Remplazando esta linea en el archivo de configuración por **0.0.0.0** pudimos se habilita el servicio para todos los usuarios y no solo para el **localhost**.

~~~
- name: Configure mysql to listen to external interface
    lineinfile:
      dest: /etc/mysql/mysql.conf.d/mysqld.cnf
      regexp: '^bind-address'
      line: bind-address = 0.0.0.0
~~~

Seguiremos con la creación de una base de datos llamada **mydb** que podra ser accedida desde cualquier parte. Y creamos las credenciales para un **root** con la contrasela **password**. Este usuario podra acceder a la base de datos.

**NOTA**: Fue importante en este paso hallar el comando adecuado para que este usuario pudiera acceder desde cualquier lugar.

~~~
 - name: Create mysql database
    mysql_db:
      login_unix_socket: /var/run/mysqld/mysqld.sock
      name: mydb
      state: present 

  - name: "Creating mysql user"
    mysql_user:
      name: root
      password : password
      priv: '*.*:ALL'
      host: '%'
      state: present
      login_unix_socket: /var/run/mysqld/mysqld.sock
    notify: restart mysql
~~~

Por ultimo se reinicia el servicio para que la configuración sean efectivos en el servicio.

**NOTA**: Para esto se implemento un handle por buenas practicas en Ansible pero este servicio tambien puede ser incluido perfectamente en las **task**.
~~~
handlers:
    - name: restart mysql
      service:
        name: mysql
        state: restarted
~~~

___


