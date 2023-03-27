# Tolerancia-a-fallas-con-MicroProfile-Quarkus
#### BARAJAS GOMEZ JUAN MANUEL | 216557005 | COMPUTACION TOLERANTE A FALLAS | 27/03/2023

### OBJETIVO:
Generando un programa basico utilizando MicroProfile y Quarkus para tolerancia a fallas.

### DESARROLLO:
##### ¿Que es Java EE? (Java Enterprise Edition) 
Es una plataforma de desarrollo de software empresarial que proporciona una infraestructura 
para construir aplicaciones escalables y seguras. Se compone de un conjunto de especificaciones, 
API y tecnologías que se utilizan para desarrollar y desplegar aplicaciones empresariales.

##### ¿Que es Java SE? (Java Standard Edition)
Es la plataforma Java estándar utilizada para desarrollar aplicaciones Java en general. 
Incluye el núcleo de la plataforma Java, como el lenguaje Java, las bibliotecas de clases 
Java y la máquina virtual Java (JVM).

##### ¿Que es Jakarta EE?
Es una plataforma empresarial Java de código abierto y comunidad impulsada por Eclipse Foundation. 
Es una evolución de Java EE que se ha trasladado a la fundación Eclipse después de que Oracle transfiriera 
Java EE a la Fundación Eclipse. Proporciona una plataforma sólida para desarrollar aplicaciones empresariales 
escalables, seguras y de alta disponibilidad.

##### ¿Que es MicroProfile?
Es una especificación Java de código abierto que se centra en la construcción de aplicaciones 
basadas en microservicios en la plataforma Java EE. Proporciona una solución flexible y escalable 
para la construcción de aplicaciones basadas en microservicios que se pueden desplegar en entornos de nube.

##### ¿Que es Spring boot?
Es un marco de trabajo de código abierto para el desarrollo de aplicaciones empresariales basadas 
en Java. Proporciona una forma rápida y fácil de crear aplicaciones Java autocontenidas que se pueden 
ejecutar en diferentes entornos.

##### ¿Que es Quarkus?
Es un marco de trabajo de código abierto diseñado para el desarrollo de aplicaciones Java nativas de nube. 
Utiliza una arquitectura centrada en eventos y se centra en la eficiencia de los recursos, lo que lo hace 
ideal para la construcción de aplicaciones empresariales escalables y de alta disponibilidad que se pueden ejecutar 
en diferentes entornos.

##### ¿Que es Maven?
Es una herramienta de gestión de proyectos de código abierto que se utiliza para la construcción, gestión 
y documentación de proyectos Java. Automatiza el proceso de construcción y gestión de dependencias para proyectos 
Java y proporciona un sistema coherente para la creación y gestión de proyectos Java.

##### ¿Que es Gradle?
Es otra herramienta de gestión de proyectos de código abierto que se utiliza para la construcción, gestión y 
documentación de proyectos Java y otros tipos de proyectos de software. Proporciona una sintaxis de lenguaje DSL 
para la definición de proyectos y tareas, lo que lo hace más flexible y fácil de usar que Maven en algunas situaciones.

##### _(El codigo se puede descargar en el archivo de la parte superior)_

Creamos el proyecto Quarkus utilizando el comando Maven para la tolerancia a fallas:
```Maven
mvn io.quarkus:quarkus-maven-plugin:2.4.0.Final:create \
    -DprojectGroupId=mi-grupo-id \
    -DprojectArtifactId=mi-artifact-id \
    -Dextensions="config-yaml, fault-tolerance"
```

Creamos una clase de servicio simple que incluya una operación que pueda fallar por una división por cero:
```java
import javax.enterprise.context.ApplicationScoped;
import org.eclipse.microprofile.faulttolerance.CircuitBreaker;

@ApplicationScoped
public class MiServicio {

    @CircuitBreaker(requestVolumeThreshold = 4)
    public int dividir(int dividendo, int divisor) {
        // Simula una división por cero en cada otra invocación
        if (Math.random() > 0.5) {
            return dividendo / divisor;
        } else {
            throw new RuntimeException("¡División por cero!");
        }
    }
}
```

Creamos una clase de punto final REST para invocar la operación del servicio:
```java
import javax.inject.Inject;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;

@Path("/mi-recurso")
public class MiRecurso {

    @Inject
    MiServicio miServicio;

    @GET
    @Produces(MediaType.TEXT_PLAIN)
    public String dividir() {
        try {
            // Simula una petición a una base de datos o un servicio externo
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        try {
            // Invoca la función dividir() del servicio MiServicio
            int resultado = miServicio.dividir(10, 0);
            return "Resultado: " + resultado;
        } catch (Exception e) {
            return "¡Error! " + e.getMessage();
        }
    }
}
```
Esta clase incluye un punto final REST para invocar la operación dividir() del servicio MiServicio. La operación del recurso MiRecurso simula una petición a una base de datos o a un servicio externo mediante la llamada a Thread.sleep(1000). Después, invoca la función dividir() del servicio MiServicio y devuelve el resultado o el mensaje de error correspondiente.

Finalmente con Docker empaquetamos y ejecutamos:
```docker
./mvnw package -Dquarkus.container-image.build=true
docker run -i --rm -p 8080:8080 mi-grupo-id/mi-artifact-id
```

![imagen](https://user-images.githubusercontent.com/101375005/228090913-e1d6364d-4462-4278-bf9c-4b1ae0d0f7b3.png)

### CONCLUSIÓN:
En general, Java es un lenguaje de programación popular que se utiliza ampliamente para el desarrollo de aplicaciones empresariales. Java EE, Jakarta EE y MicroProfile son plataformas y especificaciones empresariales de Java que se utilizan para construir aplicaciones escalables, seguras y de alta disponibilidad. Spring Boot y Quarkus son marcos de trabajo de Java que simplifican el proceso de desarrollo de aplicaciones empresariales y nativas de nube. Maven y Gradle son herramientas de gestión de proyectos de Java que se utilizan para automatizar y simplificar el proceso de construcción y gestión de proyectos de software. En conjunto, estas tecnologías y herramientas de Java proporcionan a los desarrolladores la flexibilidad y las herramientas necesarias para construir aplicaciones empresariales robustas y escalables.

### REFERENCIAS:
_Caules, C. Á. (2023, 13 enero). ¿Que es Jakarta EE? Arquitectura Java. https://www.arquitecturajava.com/jakarta-ee/_

_How to install Maven on Windows. (2018, 7 noviembre). mkyong.com. Recuperado 27 de marzo de 2023, de https://mkyong.com/maven/how-to-install-maven-in-windows/_


