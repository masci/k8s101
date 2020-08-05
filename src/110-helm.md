# Helm

## Helm

Helm is a command line application that manages **Charts**. A Chart is a
zip archive containing the definitions for an arbitrary number of Kubernetes
resources that an applications might require to work properly.

Like other software packages, Charts can be installed, updated or uninstalled
from a Kubernetes cluster, provided you have the right set of permissions. There
are Charts to easily deploy many well known softwares on Kubernetes: dbms like MySQL
or Postgres, full-fledged web applications like Wordpress or Joomla, network servers
like Traefik or Ambassador, they can all be installed on a Kubernetes cluster using
Helm; and if you have a custom application or service you can still leverage Helm by
writing your own Charts.

## The Helm Hub

Charts are stored in a (usually public) file storage with a well known layout
called **Helm repository**. Originally, Helm contributors maintained a central repo
of "official" charts but as it didn't scale, charts are now stored across many different
repositories.

To overcome this problem, Helm folks created the [Chart Hub](https://hub.helm.sh/), a
searchable archive listing all the available Charts and the repositories where you can
find them.

## Exercise n.1: install a Chart

Let's search for a chart to install a Mysql database server. We want to list the available
charts along with the repositories we can use to fetch them:
```sh
$ helm search hub mysql
URL                                               	CHART VERSION	APP VERSION	DESCRIPTION
https://hub.helm.sh/charts/appscode/stash-mysql   	8.0.14       	8.0.14     	stash-mysql - MySQL database backup and restore...
https://hub.helm.sh/charts/banzaicloud-stable/tidb	0.0.2        	           	A TiDB Helm chart for Kubernetes
https://hub.helm.sh/charts/banzaicloud-stable/p...	0.2.4        	v0.11.0    	A Helm chart for prometheus mysql exporter with...
https://hub.helm.sh/charts/banzaicloud-stable/m...	0.1.0        	0.2.0      	A Helm chart for deploying the Oracle MySQL Ope...
https://hub.helm.sh/charts/bitnami/mariadb        	7.7.1        	10.3.23    	Fast, reliable, scalable, and easy to use open-...
https://hub.helm.sh/charts/bitnami/phpmyadmin     	6.3.2        	5.0.2      	phpMyAdmin is an mysql administration frontend
https://hub.helm.sh/charts/bitnami/mysql          	6.14.7       	8.0.21     	Chart to create a Highly available MySQL cluster
<snip>
```

As you can see, every Chart has a `CHART VERSION` and an `APP VERSION`: the first
one refers to the version of the Chart itself while the latter refers to
the application that will be installed. This versioning schema allows to distribute
different versions of an application with the same Chart but different chart maintainers
might use different conventions, so don't be puzzled if the version numbers are not
familiar and browse the chart's docs to get more details.

The first column contains the URL of the docs for the chart: for example, if we open
`https://hub.helm.sh/charts/bitnami/mariadb` we see under the `Helm CLI` install section
that the next step would be adding a repository to our local list:
```sh
$ helm repo add bitnami https://charts.bitnami.com/bitnami
"bitnami" has been added to your repositories
```

Before proceeding with the installation we need to introduce some Helm terminology
to avoid confusion. When we install a **Chart** we create a **Release** of that
Chart, and every release has a **Release Name** that mitght or might not be
relevant to the application that's being deployed; Helm encourages to use a
so-called "pet-name" to name releases. Every **Release** has a set of **Values**
that can be passed by the user to customize the installation.

Let's install MariaDB `10.3`, we'll call this **Release** `rainbow-dash` and we
want to pass some **Values**, namely a new database, user and password we want
to create:

```sh
$ helm install rainbow-dash --set db.name=myapp,db.user=dbuser,db.password=s3cr3t bitnami/mariadb
NAME: rainbow-dash
LAST DEPLOYED: Wed Aug  5 17:24:50 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Please be patient while the chart is being deployed
<snip>
```

Helm adds labels to the objects it manages so we can list which pods are running after installing
the chart with:
```sh
$ kubectl get pods -l release=rainbow-dash
NAME                            READY   STATUS    RESTARTS   AGE
rainbow-dash-mariadb-master-0   1/1     Running   0          119s
rainbow-dash-mariadb-slave-0    1/1     Running   0          119s
```

> To have an idea of which **Values** a Chart accepts, refer to its documentation in the
> Helm Hub, for MariaDB: https://hub.helm.sh/charts/bitnami/mariadb

## Exercise n.2: update a Chart Release

Releases can be updated at any time, for example if you need to pass different
**Values** or if you want to deploy a more recent version of an application. You
can look at all the releases currently installed in your cluster by running:

```sh
$ helm ls
NAME        	NAMESPACE	REVISION	UPDATED                              	STATUS  	CHART        	APP VERSION
rainbow-dash	default  	1       	2020-08-05 17:24:50.778728 +0200 CEST	deployed	mariadb-7.7.2	10.3.23
```

Every **Release** has a **Revision**, which is fundamentally a counter that's
incremented every time a **Release** is updated. Let's try to change the password
for the default database user:

```sh
$ helm upgrade rainbow-dash --reuse-values --set db.password=r0tat3d bitnami/mariadb
Release "rainbow-dash" has been upgraded. Happy Helming!
NAME: rainbow-dash
<snip>
```

If you run again `helm ls`, you'll see how the **Revision** was bumped:

```sh
$ helm ls
NAME        	NAMESPACE	REVISION	UPDATED                              	STATUS  	CHART        	APP VERSION
rainbow-dash	default  	2       	2020-08-05 17:31:01.911759 +0200 CEST	deployed	mariadb-7.7.2	10.3.23
```

For every **Release** deployed on a cluster, you can list the **Values** currently
in use with the following command:

```sh
$ helm get values rainbow-dash
USER-SUPPLIED VALUES:
db:
  name: myapp
  password: r0tat3d
  user: dbuser
```

Note how `--reuse-values` in the `helm upgrade` command from above tells Helm to
merge all the **Values** currently in use with the ones overridden with `--set`;
in this case, the resulting **Values** are the ones from **Revision** n.1 with the
exception of `db.password` that was explicitly set.

## Exercise n.3: rollback a Chart

If an application installed with Helm is misbehaving, you can roll it back to a
previous **Revision** with the command:

```sh
$ helm rollback rainbow-dash
Rollback was a success! Happy Helming!
```

To see the history of a Helm release, you can do (note the rollback was registered):
```sh
$ helm history rainbow-dash
REVISION	UPDATED                 	STATUS    	CHART        	APP VERSION	DESCRIPTION
1       	Wed Aug  5 17:24:50 2020	superseded	mariadb-7.7.2	10.3.23    	Install complete
2       	Wed Aug  5 17:31:01 2020	superseded	mariadb-7.7.2	10.3.23    	Upgrade complete
3       	Wed Aug  5 17:32:33 2020	deployed   	mariadb-7.7.2	10.3.23    	Rollback to 1
```
