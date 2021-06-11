# hello-fun template repo

This accelerator repo provides a simple Hello web app based on Spring Boot and Spring Cloud Function.

It can be deployed as a standalone web app, as a Kubernetes Deployment and Service, or as a Knative Service.

## The code

> **NOTE**: The project is configured for Java 11, if you are using Java 8, then modify the `java.version` property in `pom.xml`.

The project contains the following:

```text
.
├── README.md
├── knative-logo.jpg
├── mvnw
├── mvnw.cmd
├── pom.xml
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── example
    │   │           └── hello
    │   │               └── HelloFunApplication.java
    │   └── resources
    │       └── application.properties
    └── test
        └── java
            └── com
                └── example
                    └── hello
                        └── HelloFunApplicationTests.java

12 directories, 8 files
```

You can modify the source code using [Visual Studio Code](https://code.visualstudio.com/):

```bash
code .
```

The Function that is used by this app is located at `src/main/java/com/example/helloapp/HelloFunApplication.java`

You can build the project using the provided Maven wrapper:

```bash
mvn clean package
```

## Standalone app with embedded Netty HTTP server

To run the app using the embedded server you can run this command:

```bash
mvn spring-boot:run
```

You can then access the function using `curl`:

```bash
curl -w'\n' -H 'Content-Type: text/plain' localhost:8080 -d "Fun"
```

## Kubernetes Simple Deployment and Service

This accelerator relies on using the new `buildpack` support added in Spring Boot version 2.3 to create a container image.

If you are using Minikube you should configure your terminal to use the same Docker environment:

```bash
eval $(minikube docker-env)
```

**Step 1:** Build the project and create the container image:

```
mvn clean package spring-boot:build-image
```

**Step 2:** Deploy the app to Kubernetes

```
kubectl apply -f kubernetes/app
```

Use `kubectl get all` to verify that the resources created.

Accessing the application's endpoint varies based on the type of Kubernetes cluster you are using.  For example, if minikube is being used, look for the endpoint to access using the command `minikube service list`

The README.md file from the code repository for the appliation should include a few `curl` commands to exercise the application.


## Skaffold build and deploy to Kubernetes

For a local cluster like Minikube or Docker Desktop you can simply run:

```bash
skaffold run --port-forward --profile=local --tail
```

For a remote cluster you need to specify the `default-repo` which is the registry prefix for the image that is being built. For Docker Hub the prefix would be your Docker ID, for other registries it would typically be the registry URL plus your project.

> **NOTE**: You must specify a registry prefix where you have permission to push images.

You can do this globally by running:

```bash
skaffold config set --global default-repo ${REGISTRY_PREFIX}
```

Or, you can set it for the current Kubernetes context:

```bash
skaffold config set default-repo ${REGISTRY_PREFIX}
```

Finally, you can specify it as part of the `run` command:

```bash
skaffold run --default-repo ${REGISTRY_PREFIX} --port-forward --tail
```

The `skaffold run` command will build the container image, deploy the application and port-forward the service to `localhost:8080`

Open another terminal window to interract with the application or Kubernetes API server.

You can use `kubectl get all` to verify that the resources were created.

If you leave out the `--port-forward` option then accessing the application's endpoint varies based on the type of Kubernetes cluster and ingress configuration you are using.

### Delete the application

To delete the deployment and the service from your Kubernetes cluster run:

```bash
skaffold delete
```
