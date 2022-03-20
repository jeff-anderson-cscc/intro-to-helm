# Introduction to Helm

In-class demos and notes for my Introduction to [Helm](https://helm.sh) lecture.


## What Is Helm?

__Helm is an open-source package manager for Kubernetes.__ It provides the ability to provide, share, and use software built for Kubernetes. The name is a play on the nautical nature of Kubernetes (which means “ship’s captain” in Greek) and Helm (which is the steering mechanism of a ship).

Helm was created in 2015 at Deis, which was later acquired by Microsoft. What is now known as Helm Classic was introduced at the inaugural KubeCon that November. In January 2016, Helm Classic was merged with Google’s Deployment Manager for Kubernetes into the repository that is now the main Helm project.<sup>1</sup>


## Key Terms and Concepts

* A *Chart* is a Helm package. It contains all of the resource definitions necessary to run an application, tool, or service inside of a Kubernetes cluster. 
* A *Repository* is the place where charts can be collected and shared. It's like Perl's [CPAN archive](https://www.cpan.org/) or the [Fedora Package Database](https://src.fedoraproject.org/), but for Kubernetes packages.
* A *Release* is an instance of a chart running in a Kubernetes cluster. One chart can often be installed many times into the same cluster. And each time it is installed, a new *release* is created. Consider a MySQL chart. If you want two databases running in your cluster, you can install that chart twice. Each one will have its own *release*, which will in turn have its own *release name*.
* In summary, Helm installs *charts* into Kubernetes, creating a new *release* for each installation. And to find new charts, you can search Helm chart *repositories*.<sup>2</sup>

## Goals for Helm

* Make it easier to use Kubernetes.
* Provide a package manager similar to those available in operating systems.
* Emphasize security, reusability, and configurability for deploying applications to Kubernetes.<sup>3</sup>

## Package Management Features

* Helm provides package repositories and search capabilities to find what Kubernetes applications are available.
* Helm has the familiar install, upgrade, and delete commands.
* Helm defines a method for configuring packages prior to installing them.
* Additionally, Helm has tools for seeing what is already installed and how it is configured.<sup>3</sup>

## Security Features

* Users can cryptographically verify the package comes from a trusted source.
* It enables packages to be pulled over a secure network connection.
* Users can cryptographically verify the package has not been tampered with.
* The package can be easily inspected so the user can see what it does.
* Users can see what configuration the package has, and see how different inputs impact the output of a package.<sup>3</sup>

## Reusability Features

* Supports installing the same application repeatedly and predictably into the same cluster or even the same namespace in a cluster.
* Helm provides patterns for storing configuration so that the combination of a chart plus its configuration can even be done repeatedly.
* Helm was constructed so that all Kubernetes distributions could share the same package manager.<sup>3</sup>

## Configurability Features

* Helm provides tools to configure packages at installation time and to reconfigure installations during upgrades.
* Helm was not designed to be a configuration management tool however it supports the basic lifecycle with actions for install, upgrade, and delete.
* Tools like Helmfile, Flux, and Reckoner have filled in details in the larger configuration management story.
* Kubernetes supports two main formats for declaring the resources you want: JSON and YAML.<sup>3</sup>


## What's Next?

[Getting Started](./getting-started/README.md) with Helm.


## End Notes

1. [Helm Project Journey Report](https://www.cncf.io/reports/helm-project-journey-report/) &copy; 2020 by The Linux Foundation<sup>&reg;</sup>
1. [Three Big Concepts](https://helm.sh/docs/intro/using_helm/#three-big-concepts) from the Helm documentation &copy; 2022 by the Helm Authors 
1. [Learning Helm](https://www.oreilly.com/library/view/learning-helm/9781492083641/) by Matt Butcher, Matt Farina, Josh Dolitsky &copy; 2021 O'Reilly Media, Inc.