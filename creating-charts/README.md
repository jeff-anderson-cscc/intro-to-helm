# Creating Charts

## Hello Helm

A minimal Helm chart has two components. The Chart.yaml file and at least one template file.

[hello-helm/Chart.yaml](./hello-helm/Chart.yaml) 
```
apiVersion: v2
name: hello-helm
version: 0.1.0
```

Notes:
* The [Chart.yaml](https://helm.sh/docs/topics/charts/#the-chartyaml-file) file contains metadata about the chart.
* Every chart must have a version number. A version must follow the [Semantic Versioning 2 standard](https://semver.org/spec/v2.0.0.html).

[hello-helm/templates/pod.yaml](./hello-helm/templates/pod.yaml)
```
apiVersion: v1
kind: Pod
metadata:
  name: {{ .Chart.Name }}-{{ .Release.Name }}
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

Notes:
* The templates directory has one or more files that will generate valid kubernetes manifest files when combined with values. 
* The pod name is an example where `{{ .Chart.Name }}-{{ .Release.Name }}` is replaced by the `name` attribute from the `Chart.yaml` file and the release name with a dash in between.
* Helm and Kubernetes is written in [Go](https://go.dev/) and templates use the [Go Template syntax](https://pkg.go.dev/text/template).

Install it:
```
helm install minimal-release hello-helm
```

See what we deployed:
```
kubectl get all,pvc,secret,cm
helm ls
helm get manifest minimal-release
```

## Helm create command

```
helm create -h
```

References:
* [Charts](https://helm.sh/docs/topics/charts/) documentation.
* Best practices: [General Conventions](https://helm.sh/docs/chart_best_practices/conventions/)
* Best practices: [Values](https://helm.sh/docs/chart_best_practices/values/)
* Best practices: [Templates](https://helm.sh/docs/chart_best_practices/templates/)
* Chart template guide [Getting Started](https://helm.sh/docs/chart_template_guide/getting_started/)

```
helm create sample-chart
```

## A more complex example

* [Spring Web Essentials Database Helm Chart](https://github.com/jeffrey-anderson/swe-db-helm-chart)
* [Spring Web Essentials API Helm Chart](https://github.com/jeffrey-anderson/swe-api-helm-chart)