# JHipster Online Operator

This repository hosts the JHipster Online Operator, designed to seamlessly deploy JHipster Online on Red Hat OpenShift and Kubernetes environments.

JHipster Online provides an intuitive web interface for generating JHipster projects, allowing you to define your application architecture using a JDL (JHipster Domain Language) model and generate the code directly into a GitHub repository.

## Overview

The JHipster Online Operator manages the deployment and lifecycle of the JHipster Online application. It leverages the power of Kubernetes Operators to provide a native Kubernetes experience for JHipster Online users.

This artifact includes:

* **JHipster 8.8.0**: For generating Spring Boot 3.4.1 projects.
* **generator-jhipster-quarkus 3.4.0**: For generating Quarkus 3.11.1 projects.
* **JDL Studio**: A web-based tool for creating and visualizing JDL models, which can be added as pull requests to your repository.

## Getting Started

To get started with the JHipster Online Operator, you'll need a running Kubernetes or OpenShift cluster and `podman` installed.

### Prerequisites

* **OpenShift or Kubernetes Cluster**: A running cluster (e.g., Minikube, OpenShift Local/CRC, or a cloud-managed cluster) and cluster-admin user.
* **`oc` or `kubectl`**: Command-line tool configured to connect to your cluster.
* **`podman`**: Required to run the operator bundle locally.
* **`operator-sdk`**: The operator SDK is used to build and deploy the operator.

### Installation

Follow these steps to deploy the JHipster Online Operator:

1.  **Ensure you have `podman` installed** and configured to interact with your Docker daemon or a local `podman` environment.
2.  **Make sure your `kubeconfig` file is correctly set up** and pointing to your target Kubernetes/OpenShift cluster. For Windows users, replace `/root/.kube/config` with the actual path to your `kubeconfig` file.
3.  **Run the operator bundle** using the `operator-sdk` container:

    ```bash
    podman run --rm -it -v "$HOME/.kube/config:/root/.kube/config:ro" -e KUBECONFIG=/root/.kube/config quay.io/operator-framework/operator-sdk:v1.34.1 run bundle quay.io/maximilianopizarro/jhipster-online-operator-bundle:v0.1.0
    ```

    This command will deploy the JHipster Online Operator into your cluster.

### Usage

Once the operator is running, you can create instances of JHipster Online by defining `JhipsterOnline` custom resources. Here's an example `JhipsterOnline` custom resource (from the `alm-examples` in the CSV):

```yaml
apiVersion: maximilianopizarro.github.io/v1alpha1
kind: JhipsterOnline
metadata:
  name: jhipsteronline-sample
spec:
  # Configure your JHipster Online instance here.
  # For example, set your GitHub client ID and secret:
  env:
    APPLICATION_GITHUB_CLIENT-ID: "YOUR_GITHUB_CLIENT_ID"
    APPLICATION_GITHUB_CLIENT-SECRET: "YOUR_GITHUB_CLIENT_SECRET"
    # Other environment variables like database connection
    # SPRING_DATASOURCE_URL: "jdbc:mariadb://mariadb:3306/jhipsteronline"
    # SPRING_DATASOURCE_USERNAME: "jhipster"
    # SPRING_DATASOURCE_PASSWORD: "jhipster"
  # Set the image for JHipster Online
  image:
    repository: quay.io/maximilianopizarro/jhipster-online
    tag: quarkus
  # Enable route for OpenShift or Ingress for Kubernetes if needed
  route:
    enabled: true
  # replicaCount: 1
  # autoscaling:
  #   enabled: false
  #   minReplicas: 1
  #   maxReplicas: 100
  #   targetCPUUtilizationPercentage: 80
````

You would apply this (or a similar) YAML file to your cluster to create an instance of JHipster Online:

```bash
kubectl apply -f your-jhipsteronline-instance.yaml
```

Feel free to customize the `JhipsterOnline` Custom Resource Definition (CRD) instance to suit your needs, such as configuring GitHub integration, database connections, and resource allocations.

### Configuration

The `JhipsterOnline` custom resource supports various configuration options to tailor your JHipster Online deployment:

| Parameter                      | Description                                                                                                                                                                                                                                                                                                | Default                                           |
| :----------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------ |
| `replicaCount`                 | Number of JHipster Online replicas.                                                                                                                                                                                                                                                                        | `1`                                               |
| `image.repository`             | Container image repository.                                                                                                                                                                                                                                                                                | `quay.io/maximilianopizarro/jhipster-online`      |
| `image.tag`                    | Container image tag.                                                                                                                                                                                                                                                                                       | `quarkus`                                         |
| `image.pullPolicy`             | Container image pull policy.                                                                                                                                                                                                                                                                               | `IfNotPresent`                                    |
| `imagePullSecrets`             | Secrets for pulling images from private registries.                                                                                                                                                                                                                                                        | `[]`                                              |
| `fullnameOverride`             | Override the full name of the chart.                                                                                                                                                                                                                                                                       | `""`                                              |
| `nameOverride`                 | Override the name of the chart.                                                                                                                                                                                                                                                                            | `""`                                              |
| `serviceAccount.create`        | Specifies whether a service account should be created.                                                                                                                                                                                                                                                     | `false`                                           |
| `serviceAccount.automount`     | Automount API credentials for the service account.                                                                                                                                                                                                                                                         | `false`                                           |
| `serviceAccount.annotations`   | Annotations to add to the service account.                                                                                                                                                                                                                                                                 | `{}`                                              |
| `serviceAccount.name`          | The name of the service account to use. If not set and `serviceAccount.create` is `true`, a name is generated using the fullname template.                                                                                                                                                                | `""`                                              |
| `podAnnotations`               | Annotations to add to the pod.                                                                                                                                                                                                                                                                             | `{}`                                              |
| `podLabels`                    | Labels to add to the pod.                                                                                                                                                                                                                                                                                  | `{}`                                              |
| `podSecurityContext`           | Pod-level security context.                                                                                                                                                                                                                                                                                | `{}`                                              |
| `securityContext`              | Container-level security context.                                                                                                                                                                                                                                                                          | `{}`                                              |
| `service.type`                 | Kubernetes Service type.                                                                                                                                                                                                                                                                                   | `ClusterIP`                                       |
| `service.port`                 | Service port.                                                                                                                                                                                                                                                                                              | `8080`                                            |
| `ingress.enabled`              | Enable Ingress for JHipster Online.                                                                                                                                                                                                                                                                        | `false`                                           |
| `ingress.className`            | IngressClass name (Kubernetes 1.18+).                                                                                                                                                                                                                                                                      | `""`                                              |
| `ingress.annotations`          | Annotations for Ingress.                                                                                                                                                                                                                                                                                   | `{}`                                              |
| `ingress.hosts`                | Hosts for Ingress.                                                                                                                                                                                                                                                                                         | `[{"host": "chart-example.local", "paths": [{"path": "/", "pathType": "ImplementationSpecific"}]}]` |
| `ingress.tls`                  | TLS configuration for Ingress.                                                                                                                                                                                                                                                                             | `[]`                                              |
| `route.enabled`                | Enable OpenShift Route for JHipster Online.                                                                                                                                                                                                                                                                | `true`                                            |
| `autoscaling.enabled`          | Enable horizontal pod autoscaling.                                                                                                                                                                                                                                                                         | `false`                                           |
| `autoscaling.minReplicas`      | Minimum number of replicas for autoscaling.                                                                                                                                                                                                                                                                | `1`                                               |
| `autoscaling.maxReplicas`      | Maximum number of replicas for autoscaling.                                                                                                                                                                                                                                                                | `100`                                             |
| `autoscaling.targetCPUUtilizationPercentage` | Target CPU utilization percentage for autoscaling.                                                                                                                                                                                                                                   | `80`                                              |
| `resources`                    | CPU/memory requests and limits for the JHipster Online container.                                                                                                                                                                                                                                          | `{}` (defaults to Kubernetes/OpenShift defaults)   |
| `nodeSelector`                 | Node labels for pod assignment.                                                                                                                                                                                                                                                                            | `{}`                                              |
| `tolerations`                  | Tolerations for node taints.                                                                                                                                                                                                                                                                               | `[]`                                              |
| `affinity`                     | Affinity rules for pod scheduling.                                                                                                                                                                                                                                                                         | `{}`                                              |
| `livenessProbe`                | Liveness probe configuration for the JHipster Online container.                                                                                                                                                                                                                                            | `{"httpGet": {"path": "/jdl-studio/", "port": 8080}}` |
| `readinessProbe`               | Readiness probe configuration for the JHipster Online container.                                                                                                                                                                                                                                           | `{"httpGet": {"path": "/jdl-studio/", "port": 8080}}` |
| `env`                          | Environment variables for the JHipster Online container. Crucial for configuring GitHub integration and database connections. | `{}`                                              |
| `volumes`                      | Arbitrary volumes to be mounted into the pod.                                                                                                                                                                                                                                                              | `[]`                                              |
| `volumeMounts`                 | Arbitrary volume mounts to be added to the container.                                                                                                                                                                                                                                                      | `[]`                                              |

### Contributing

Contributions are welcome\! If you find a bug or have a feature request, please open an issue or submit a pull request.

### License

This project is licensed under the Apache 2.0 License - see the [LICENSE](https://www.google.com/search?q=LICENSE) file for details.

```

