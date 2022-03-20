# Working with Charts

For a complete list of options available when installing a Helm chart:

```
helm install -h | less
```

## Phases of a Helm Intallation

Helm goes through 5 phases when performing an installation:

1. Load the chart.
1. Parse the values.
1. Execute the templates.
1. Render the YAML.
1. Send it to Kubernetes.

The first four phases are primarily concerned with a local representation of the data. That is, Helm is doing all of the processing on the same computer that the helm command is run on.

During the last phase, though, Helm sends that data to Kubernetes. And then the two communicate back and forth until the release is either accepted or rejected.<sup>1</sup>

## Installing Drupal

References:
* [Bitnami Drupal](https://artifacthub.io/packages/helm/bitnami/drupal) on Artifact Hub.
* [Drupal packaged by Bitnami](https://bitnami.com/stack/drupal/helm) documentation
* [Drupal packaged by Bitnami](https://github.com/bitnami/charts/tree/master/bitnami/drupal/) GitHub repo.

We could use the command below to install the chart using all default values and a generated release name:
```
helm install bitnami/drupal --generate-name
```

However, to make subsequent commands consistent for everyone, we can install the chart with the specific release name such as `hello-helm`. That way the commands below will work for everyone.
```
helm install hello-helm bitnami/drupal
```


Get a list of installed helm releases:
```
helm ls
```

To see what resources the chart created:
```
kubectl get all,pvc,secret,cm
```

Note the service type is `LoadBalancer`. In a second terminal, run `minikube tunnel`. One the tunnel is established, the service should have an external IP address:

```
kubectl get svc
```

Copy the IP address into your browser. 

## Getting More Information

Find out what information is available:

```
helm get -h
```

Helm displayed the notes after we installed the chart. To see them again we can run:
```
helm get notes hello-helm
```

The notes tell us how to get the user name and password so we can log into our running Drupal website.

We can also see the manifests Helm generated and submitted to Kubernetes:
```
helm get manifest hello-helm | less
```

To see what values were supplied when Helm installed our chart:
```
helm get values hello-helm
```

We can get all information about our release including computed values based on the chart configuration:
```
helm get all hello-helm | less
```

## Release Names and Namespaces

Release names must be unique within a namespace:
```
helm install hello-helm bitnami/drupal
```
But we can install multiple releases in the same namespaces if they have different names:
```
helm install hello-drupal bitnami/drupal
```
To see what we have now:
```
helm ls
```
and:
```
kubectl get all,pvc,secret,cm
```
In Helm, Well written templates include the release name in the resource name.

We can reuse a release name in different namespaces:
```
kubectl create ns foo
helm install hello-helm bitnami/drupal --namespace foo
```
To see what we have now:
```
helm ls --all-namespaces
```
and:
```
kubectl get all,pvc,secret,cm 
kubectl get all,pvc,secret,cm --namespace foo
```

## Uninstalling a Chart

To get the list of installed charts:

```
helm ls --all-namespaces
```

To uninstall the chart we created in the `foo` namespace:

```
helm uninstall hello-helm --namespace foo
```

We can delete the releases in our current namespace as follows:
```
helm uninstall hello-helm
helm uninstall hello-drupal
```

## Customizing our Installation

The [Bitnami Drupal GitHub Page](https://github.com/bitnami/charts/tree/master/bitnami/drupal/#parameters) has a list of parameters we can set to customize our installation.

The [values.yaml](https://github.com/bitnami/charts/blob/master/bitnami/drupal/values.yaml) chart file has the master list of parameters one can override. For this example, let's specify custom values for three parameters:

| Key | Value |
|:----|:------|
| drupalUsername | jeff |
| drupalPassword | T0pS3cr3t |
| drupalEmail | janderson141@cscc.edu |

We can do it on the command line:
```
helm install custom-vals bitnami/drupal --set drupalUsername=jeff,drupalPassword=T0pS3cr3t,drupalEmail=janderson141@cscc.edu
```

Verify the overrides with:
```
helm get values custom-vals
```
Login and verify the credentials work by running `kubectl get svc` then visiting the External IP address of our drupal service and choosing the "Log in" link.

## Installing a New Version

If you run `helm ls` you will notice we are on revision 1 of our release.  Also, running `helm get all custom-vals | grep bitnami/drupal` shows our image tag is "9.3.8-debian-10-r1". The [Bitnami Drupal Docker Hub page](https://hub.docker.com/r/bitnami/drupal/tags) show other tags we can use. Lets try `drupal:9.3.8-debian-10-r3` and override it using a values YAML file.

In a file called [myvalues.yaml](./myvalues.yaml), add the following:
```
drupalUsername: jeff
drupalPassword: T0pS3cr3t
drupalEmail: janderson141@cscc.edu
image:
  tag: drupal:9.3.8-debian-10-r3
```

Next, let's apply our changes with:

```
helm upgrade custom-vals bitnami/drupal -f myvalues.yaml
```
As you will note, the upgrade fails because the MariaDB root and user password were set to random values when we did our first installation. This brings up a related but also important point. If you specify a value for a parameter during an install or upgrade but then do not on a subsequent upgrade, it may revert back to it's default value.

The error message shows us how to set these parameters and fix our issue:

```
export MARIADB_ROOT_PASSWORD=$(kubectl get secret --namespace "helm" custom-vals-mariadb -o jsonpath="{.data.mariadb-root-password}" | base64 --decode)
export MARIADB_PASSWORD=$(kubectl get secret --namespace "helm" custom-vals-mariadb -o jsonpath="{.data.mariadb-password}" | base64 --decode)
```

With these environment variables set, we can try the upgrade again passing the extra values:
```
helm upgrade custom-vals bitnami/drupal -f myvalues.yaml --set mariadb.auth.rootPassword=$MARIADB_ROOT_PASSWORD --set mariadb.auth.password=$MARIADB_PASSWORD
```

Now, when we run `helm ls` it shows we are on revision 2. Also, if we run `helm get all custom-vals | grep bitnami/drupal` again, we can see the updated image tag.

We can also see the release history with:
```
helm history custom-vals
```

## Configuration Best Practices

* Store custom configuration values in a yaml file and version control it so there is a record of values and changes over time. 
* Avoid storing passwords in version control unless access is severely limited.
* Only use `--set` when absolutely necessary and a good use case is supplying passwords that you don't want to keep in version control.
* You can use multiple configuration files so we could have installed with the original 3 overrides in a `values-v1.yaml` then added the image tag and Maria DB passwords in a `values-v2.yaml` file passing both to the upgrade command.

## Helm Release Information

```
kubectl get secret | grep helm.release
```

## Rolling Back a Release

If we run `helm history custom-vals` we see the two releases from our work above. To roll back to version 1:
```
helm rollback custom-vals 1
```
We can check the updated history with:
```
helm history custom-vals
```

## Uninstalling our Release

```
helm uninstall custom-vals
```
Note the persistent volume claims are retained:
```
kubectl get all,pvc,secret,cm
```
To remove all in our current namespace:
```
kubectl delete pvc --all
```
Check the list of installed charts:
```
helm ls
```

## Additional Useful Options

### --dry-run

Commands like `helm install` and `helm upgrade` provide a flag named `--dry-run`. When you supply this flag, it will cause Helm to step through the first four phases (load the chart, determine the values, render the templates, format to YAML). But when the fourth phase is finished, Helm will dump a trove of information to standard output, including all of the rendered templates. Then it will exit without sending the objects to Kubernetes and without creating any release records.<sup>1</sup>

```
helm install my-release bitnami/drupal -f myvalues.yaml --dry-run
```

### helm template

While `--dry-run` is designed for debugging, `helm template` is designed to isolate the template rendering process of Helm from the installation or upgrade logic.

Earlier, we looked at the five phases of a Helm install or upgrade. The template command performs the first four phases (load the chart, determine the values, render the templates, format to YAML). But it does this with a few additional caveats:

* During helm template, Helm never contacts a remote Kubernetes server.
* The template command always acts like an installation.
* Template functions and directives that would normally require contacting a Kubernetes server will instead only return default data.
* The chart only has access to default Kubernetes kinds.

As a result, `helm template` produces consistent output run after run. More importantly, it can be run in an environment that does not have access to a Kubernetes cluster, like a continuous integration (CI) pipeline.<sup>1</sup>

```
helm template my-release bitnami/drupal -f myvalues.yaml
```

### upgrade --install

Helm allows the `--install` flag to compined with the `helm upgrade command`. This allows a release to be installed if it does not exist already, or upgraded if it does.  This is useful for continuous integration (CI) pipelines.

```
helm upgrade --install my-release bitnami/drupal -f myvalues.yaml
```

## What's Next?

Back to [getting started](../getting-started/README.md) or forward to [creating charts](../creating-charts/README.md).


## Footnotes

1. [Learning Helm](https://www.oreilly.com/library/view/learning-helm/9781492083641/) by Matt Butcher, Matt Farina, Josh Dolitsky &copy; 2021 O'Reilly Media, Inc.