## Cómo empezar en robótica e instalar software en Linux para practicar en C++ y Python

### Primeros pasos en robótica

- **Aprende los conceptos básicos:** Lee libros, asiste a cursos o ve tutoriales para comprender los conceptos fundamentales de robótica, como cinemática, dinámica, control y percepción.
- **Elige una plataforma:** Explora diferentes plataformas de robótica, como ROS (Robot Operating System) o Gazebo. Esto te proporcionará un entorno para desarrollar y simular tus programas.
- **Construye un robot simple:** Comienza con un robot pequeño y sencillo, como un coche con ruedas o un brazo robótico. Esto te permitirá poner en práctica tus conocimientos y depurar tu código.

### Instalación de software en Linux para practicar en C++ y Python

#### ROS

- **Instala ROS:** Sigue las instrucciones en [ros.org](https://www.ros.org/documentation/installation/) para instalar ROS en tu distribución de Linux.
- **Crea un espacio de trabajo:** Crea un nuevo espacio de trabajo para tu proyecto ROS.
- **Instala paquetes:** Instala los paquetes ROS necesarios para tu proyecto. Por ejemplo, `ros-kinetic-gazebo-ros`, si deseas utilizar Gazebo.

#### Gazebo

- **Instala Gazebo:** Sigue las instrucciones en [gazebosim.org](https://gazebosim.org/tutorials?cat=install) para instalar Gazebo en tu distribución de Linux.
- **Crea un mundo:** Crea un nuevo mundo Gazebo para simular tu robot.
- **Agrega modelos:** Agrega modelos 3D de tu robot y entorno al mundo Gazebo.

#### Código en C++ y Python

C++:

```c++
#include <ros/ros.h>
#include <geometry_msgs/Twist.h>

int main(int argc, char **argv) {
  ros::init(argc, argv, "robot_controller");
  ros::NodeHandle nh;

  // Crear un publicador para controlar la velocidad del robot
  ros::Publisher pub = nh.advertise<geometry_msgs::Twist>("cmd_vel", 10);

  // Crear un bucle que publique mensajes de velocidad a una frecuencia de 10 Hz
  ros::Rate rate(10);
  while (ros::ok()) {
    geometry_msgs::Twist msg;
    msg.linear.x = 0.1;
    msg.angular.z = 0.2;
    pub.publish(msg);
    rate.sleep();
  }

  return 0;
}
```

Python:

```python
import rospy
from geometry_msgs.msg import Twist

def main():
  rospy.init_node('robot_controller')
  pub = rospy.Publisher('cmd_vel', Twist, queue_size=10)
  rate = rospy.Rate(10) # 10 Hz

  while not rospy.is_shutdown():
    msg = Twist()
    msg.linear.x = 0.1
    msg.angular.z = 0.2
    pub.publish(msg)
    rate.sleep()

if __name__ == '__main__':
  main()
```

- **Crea paquetes ROS:** Crea paquetes ROS separados para tu código C++ y Python.
- **Escribe nodos:** Escribe nodos ROS en C++ y Python para controlar tu robot y procesar datos del sensor.
- **Construye y ejecuta:** Construye y ejecuta tus nodos ROS para probar tu programa.


- [Introducción a la Robótica para Principiantes](https://www.coursera.org/learn/robotics)
- [Guía de Iniciación a Ubuntu para Robótica](https://wiki.ubuntu.com/Robotics)
- [Tutoriales de Gazebo](https://gazebosim.org/docs)
- [Tutoriales de ROS](https://www.ros.org/blog/getting-started/#)
