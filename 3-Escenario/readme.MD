
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

