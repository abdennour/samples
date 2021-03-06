# JIB sample application

This example shows how to leverage Okteto to develop a Java App directly in the cloud with JIB. The Java Sample App is deployed using raw Kubernetes manifests. It's based on [Baeldung's tutorial](https://www.baeldung.com/jib-dockerizing).

While you're going through this guide, join the [conversation in Slack](https://kubernetes.slack.com/messages/CM1QMQGS0/)! If you don't already have a Kubernetes slack account, [sign up here](http://slack.k8s.io/).  We'd love to hear from you 😄.

## Step 1: Install the Okteto CLI

The Okteto CLI transforms your remote Kubernetes pods into development environments with all your dev tools available to build and test your applications. It also starts a bidirectional synchronization service between your local filesystem and your remote containers to avoid the build/push/redeploy cycle. See your changes live in the cloud in seconds!

Install the Okteto CLI by running the following command in your local terminal:

```console
$ curl https://get.okteto.com -sSfL | sh
```

The Okteto CLI works in any Kubernetes cluster. If you are interested in using your existing Kubernetes cluster, jump to step 4 of the guide. However, the Okteto CLI is deeper integrated with [Okteto Cloud](https://cloud.okteto.com), the hosted version of Okteto Enterprise.

## Step 2: Login from the Okteto CLI

Run `okteto login` command to create and initialize [your Okteto account](https://cloud.okteto.com/#/?origin=docs). 

```console
$ okteto login
```

When you create an [Okteto Cloud](https://cloud.okteto.com) account, a kubernetes service account, namespace, and credentials will be automatically created for you in the [Okteto’s multi-tenant kubernetes cluster](https://cloud.okteto.com). These resources are configured automatically to include network policies, quotas, pod security policies, admission webhooks, roles, role bindings and limit ranges. This way, every developer has access to the Kubernetes cluster without interfering with other developer namespaces.

## Step 3: Switch Kubernetes namespaces and context

Run the following command to activate your personal namespace:

```console
$ okteto namespace
```

The `okteto namespace` command downloads your Kubernetes credentials from Okteto Cloud, adds them to your kubeconfig file, and sets it as the current context. Once you do this, you will have full access to your Kubernetes namespace with any Kubernetes tool.

## Step 4: Deploy the Sample Application

Get a local version of the sample application by executing the following commands in your local terminal:

```console
$ git clone https://github.com/okteto/samples
$ cd samples/jib
```

In order to deploy the application, execute:

```console
$ kubectl apply -f manifest
```

After a few seconds, check that the app is running. If you are using Okteto Cloud, the [Okteto Cloud UI](https://cloud.okteto.com) will show you the endpoint of your application.

# Compile for dev mode

In order to activate the development mode in the sample application, execute:

```
> okteto up
 ✓  Persistent volume provisioned
 ✓  Files synchronized
 ✓  Okteto Environment activated
    Namespace: pchico83
    Name:      java-dev

> gradle build
Welcome to Gradle 5.1.1!

Here are the highlights of this release:
 - Control which dependencies can be retrieved from which repositories
 - Production-ready configuration avoidance APIs

For more details see https://docs.gradle.org/5.1.1/release-notes.html

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 1m 12s
2 actionable tasks: 2 executed
okteto> gradle build

BUILD SUCCESSFUL in 3s
2 actionable tasks: 2 up-to-date
```

Now the binaries are accesible by your application, but since our production image does not hotreaload code changes, we need to restart the app by executing:

```
> okteto restart
```

Check that the app is running ok. You can use the application endpoint generated by [Okteto Cloud](https://cloud.okteto.com) to validate it.

Make a code change in `HelloController.java`, for example, to return the string *Greetings from Okteto!*, and execute again:

```
> gradle build
> okteto restart
```

Check your application again and bualá!

Your changes are online in a few seconds, no commit, docker build or push required!

> As you can see, there is a `.stignore` file in the root folder to indicate folders not synched by Okteto. In this case, we are ignoring the `build` folder to avoid synching binaries compiled in remote to our local filesystem. If you remove the `build` folder from the `.stignore` file, you could execute `gradle build` and `okteto restart` locally instead of using the Okteto Terminal (this might give your a deeper integration with your local IDE).
