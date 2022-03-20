# Getting Started With Helm

## Installing Helm

The quick and easy way:

```
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

For alternatives and details, see [Installing Helm](https://helm.sh/docs/intro/install/) in the Helm documentation.


## Finding Charts

`helm search hub` searches the [Artifact Hub](https://artifacthub.io/) for charts from a wide array of providers. For example, to find a chart for the [Drupal content management system (CMS)](https://www.drupal.org/)

```
helm search hub drupal
```
Returns:
```
URL                                               	CHART VERSION	APP VERSION	DESCRIPTION                                       
https://artifacthub.io/packages/helm/bitnami-ak...	11.0.19      	9.3.8      	Drupal is one of the most versatile open source...
https://artifacthub.io/packages/helm/bitnami/dr...	11.0.20      	9.3.8      	Drupal is one of the most versatile open source...
https://artifacthub.io/packages/helm/cetic/drupal 	0.1.0        	1.16.0     	Drupal is a free and open-source web content ma...
```

To widen the columns, use `--max-col-width` plus a number for how wide you want the columns to be:

```
helm search hub drupal --max-col-width 65
```
Returns:
```
URL                                                    	CHART VERSION	APP VERSION	DESCRIPTION                                                      
https://artifacthub.io/packages/helm/bitnami-aks/drupal	11.0.19      	9.3.8      	Drupal is one of the most versatile open source content manage...
https://artifacthub.io/packages/helm/bitnami/drupal    	11.0.20      	9.3.8      	Drupal is one of the most versatile open source content manage...
https://artifacthub.io/packages/helm/cetic/drupal      	0.1.0        	1.16.0     	Drupal is a free and open-source web content management framew...
```

### Security tip:

Like [Docker Hub](https://hub.docker.com/), anyone can publish anything to [Artifact Hub](https://artifacthub.io/). When searching for charts, always start by filtering on "official" charts where the chart publisher owns the software deployed by the packages. If the software you need is not available from an official source, try "verified publishers". While it is no guarantee of quality and security, official and verified publishers are generally more trustworthy. 

Regardless of the source, you can (and should) examine the manifests produced by a chart before using it by using the `--dry-run` option with `helm install`.

## Adding a repo

Drupal is available from verified publisher [Bitnami](https://bitnami.com/) which is now part of VMware. A handful of Bitnami developers were among the core contributors who designed the Helm repository system. They have contributed to the establishment of Helm’s best practices for chart development and have written many of the most widely used charts.<sup>1</sup>

Let's add Bitnami to our local repository list using the information from the [Artifact Hub Bitnami Drupal](https://artifacthub.io/packages/helm/bitnami/drupal) page.

```
helm repo add bitnami https://charts.bitnami.com/bitnami
```

To verify the result:
```
helm repo list
```

## Searching the Repo

Search the repo for Drupal charts:

```
helm search repo drupal
```

The search command matches on a variety of fields like package name, labels and descriptions. Try some searches to see this in action:

```
helm search repo "open source"
```
Search for the term database:
```
helm search repo database
```

We can also see which versions exist for a chart:
```
helm search repo drupal --versions
```
Pipe the output to the `head` command to limit the list to the first 10:
```
helm search repo drupal --versions | head -10
```

## Versions

A *chart version* is the version of the Helm chart. The *app version* is the version of the application packaged in the chart. Helm uses the chart version to make versioning decisions, such as which package is newest. As we can see in the preceding example, multiple chart versions may contain the same app version.<sup>1</sup>

## What's Next?

Back to [Helm overview](../README.md) or forward to [Working with Charts](../installing-charts/README.md).


## Footnotes

1. [Learning Helm](https://www.oreilly.com/library/view/learning-helm/9781492083641/) by Matt Butcher, Matt Farina, Josh Dolitsky &copy; 2021 O'Reilly Media, Inc.