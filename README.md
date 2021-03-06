# proyecto base de QA
## Dependecias y datos utiles

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Lo que vamos a tener en cuenta despues de la creación del proyecto es agregar las dependencias en pom.xml y recargar el maven

##Estructura base
- src
    - main
        - java ->paquetes y clases.
        - resources
    - test
        - java -> TestRunner y definicionpasos(paquete)
        - resources -> features

### depencia para trabjar con API's Rest

    <!-- Dependencia para trabajar con API's Rest -->
        <dependency>
            <groupId>net.serenity-bdd</groupId>
            <artifactId>serenity-screenplay-rest</artifactId>
            <version>${serenity.version}</version>
        </dependency>

### dependica para trabjar con db de oracle

    <!-- Dependencia para trabajar con PLSQL -->
        <dependency>
            <groupId>com.oracle.jdbc</groupId>
            <artifactId>ojdbc8</artifactId>
            <version>12.2.0.1</version>
        </dependency>

## Paquetes

- Modelos: en este paquete estarán todos los servicios
- Preguntas: cual es el response?, entre otras.
- Tareas:  enviaremos el request
- Utiles(opcional): la url como constante

### Modelos
dentro de este paquete estaran, lo paquetes de los casos con sus respectivos request, es decir:
* PostLoginSuccessful va a tener adentro los paquetes PostLoginSuccessfulRequest y PostLoginSuccessfulResponse, con sus clases respectivas adentro.
* Ejemplo PostLoginSuccessfulRequest


            package modelos.PostLoginSuccessful.Request;
            public class PostLoginSuccessfulRequest {
                private String email;
                private String password;        
                public PostLoginSuccessfulRequest(String email, String password) {
                    this.email = email;
                    this.password = password;
                }
                public String getEmail() {
                    return email;
                }
                public void setEmail(String email) {
                    this.email = email;
                }
                public String getPassword() {
                    return password;
                }
                public void setPassword(String password) {
                    this.password = password;
                }
            }

* Ejemplo PostLoginSuccessfulResponse


        package modelos.PostLoginSuccessful.Response;
        public class PostLoginSuccessfulResponse {
            private String toker;
            public String getToker() {
                return toker;
            }
            public void setToker(String toker) {
                this.toker = toker;
            }
        }

### Pregunta
en este paquete estaran las preguntas y verificaciones(el estado, si es visible), en este caso obtendremos el response.

* Ejemplo PostLoginSuccessfulQuestion


        package preguntas;    
        import modelos.PostLoginSuccessful.Response.PostLoginSuccessfulResponse;
        import net.serenitybdd.rest.SerenityRest;
        import net.serenitybdd.screenplay.Actor;
        import net.serenitybdd.screenplay.Question;    
        public class PostLoginSuccessfulQuestion implements Question<PostLoginSuccessfulResponse> {
            @Override
            public PostLoginSuccessfulResponse answeredBy(Actor actor) {    
                return SerenityRest.lastResponse().as(PostLoginSuccessfulResponse.class);
            }
        }

### Tarea
aca vamos enviar el request.
* PostLoginSuccessfulTask


        package tareas;
        import io.restassured.http.ContentType;
        import modelos.PostLoginSuccessful.Request.PostLoginSuccessfulRequest;
        import net.serenitybdd.screenplay.Actor;
        import net.serenitybdd.screenplay.Performable;
        import net.serenitybdd.screenplay.Task;
        import net.serenitybdd.screenplay.rest.interactions.Post;    
        import static net.serenitybdd.screenplay.Tasks.instrumented;    
        public class PostLoginSuccessfulTask implements Task {
           //se envia request
            private final PostLoginSuccessfulRequest request;
            //constructor
            public PostLoginSuccessfulTask( String usuario, String password) {
                this.request = new PostLoginSuccessfulRequest(usuario,password);
            }
            public static Performable con( String usuario, String password){
                return instrumented(PostLoginSuccessfulTask.class, usuario, password);
            }    
            @Override
            public <T extends Actor> void performAs(T actor) {
                //el actor que va hacer
                actor.attemptsTo(
                        Post.to("/login").with(
                                requestSpecification -> requestSpecification.contentType(ContentType.JSON)
                                        .body(this.request)
                    )
                );    
            }
        }

### Utiles
podemos crear un constante para la url donde realizaremos la pruebas
* Constantes


        package utiles;
            public interface Constantes {
                String URl_API = "https://reqres.in/api";
            }

