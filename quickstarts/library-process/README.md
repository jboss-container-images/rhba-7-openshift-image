## Red Hat Business Automation Kie Server Quickstart

This quickstart is intend to be used with the [RHBA Kie Server](https://github.com/jboss-container-images/rhba-7-openshift-image/tree/rhba70-dev/kieserver) image.

## How to use it?

To deploy the Library Process demo you can use the [rhba70-kieserver-basic-s2i](https://github.com/jboss-container-images/rhba-7-openshift-image/blob/rhba70-dev/templates/rhba70-kieserver-basic-s2i.yaml)

To deploy it on your OpenShift instance, just execute the following commands:

```bash
$ oc login https://<your_openshift_address>:<port>
Authentication required for https://ocp-master.cloud.com:8443 (openshift)
Username: developer
Password:
Login successful.

You have access to the following projects and can switch between them with 'oc project <projectname>':

    default
    kube-public
    kube-system
    logging
    management-infra
    nexus
    openshift
    openshift-infra
    openshift-node
  * rhba

Using project "rhba".
```
```bash
$ oc new-project rhba-kieserver
Now using project "rhba-kieserver" on server "https://ocp-master.cloud.com:8443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app centos/ruby-22-centos7~https://github.com/openshift/ruby-ex.git

to build a new example application in Ruby.
```

Make sure that you have the RHBA template installed in your OpenShift Instance:
```bash
$ oc get template rhba70-kieserver-s2i -n openshift
Error from server (NotFound): templates "rhba70-kieserver-s2i" not found
```
If you don't have it yet, just install it:

```bash
oc create -f https://raw.githubusercontent.com/jboss-container-images/rhba-7-openshift-image/rhba70-dev/templates/rhba70-kieserver-s2i.yaml -n openshift
template "rhba70-kieserver-s2i" created
```

For this template, we also need to install the secrets, which contain the certificates to configure https:
```bash
$ oc create -f https://raw.githubusercontent.com/jboss-container-images/rhba-7-openshift-image/rhba70-dev/kieserver-app-secret.yaml
```


```bash
$ oc new-app rhba70-kieserver-s2i
  --> Deploying template "openshift/rhba70-kieserver-s2i" to project rhba-kieserver

       Red Hat Business Automation Execution Server 7.0 S2I (Ephemeral with https)
       ---------
       Application template for Red Hat Business Automation Execution Server 7.0 application built using S2I.

       A new Business Automation Execution Server application has been created in your project. Please be sure to create the secret named "kieserver-app-secret" containing the keystore.jks file used for serving secure content.

       * With parameters:
          * Application Name=myapp
          * EAP Admin User=eapadmin
          * EAP Admin Password=cWTrvp4! # generated
          * KIE Server Controller User=controllerUser
          * KIE Server Controller Password=HVIIPy2! # generated
          * KIE server controller protocol=http
          * KIE server controller service=
          * KIE server controller host=
          * KIE server controller port=
          * KIE Admin User=adminUser
          * KIE Admin Password=Srkxpd0! # generated
          * KIE Server Sync Deploy=false
          * KIE Server User=executionUser
          * KIE Server Password=qjxFyb6! # generated
          * KIE Server Bypass Auth User=false
          * KIE MBeans=enabled
          * Drools Server Filter Classes=true
          * Execution Server Custom http Route Hostname=
          * Execution Server Custom https Route Hostname=
          * Server Keystore Secret Name=kieserver-app-secret
          * Server Keystore Filename=keystore.jks
          * Server Certificate Name=jboss
          * Server Keystore Password=mykeystorepass
          * KIE Server Container Deployment=rhba-kieserver-library=org.openshift.quickstarts:rhba-kieserver-library:1.4.0.Final
          * Git Repository URL=https://github.com/jboss-container-images/rhba-7-openshift-image.git
          * Git Reference=rhba70-dev
          * Context Directory=quickstarts/library-process/library
          * Github Webhook Secret=XK6KW5dB # generated
          * Generic Webhook Secret=MmEl1Rr5 # generated
          * ImageStream Namespace=openshift
          * Maven mirror URL=
          * Maven repository URL=
          * Maven repository username=
          * Maven repository password=
          * ARTIFACT_DIR=

  --> Creating resources ...
      serviceaccount "kieserver-service-account" created
      service "myapp-kieserver" created
      service "secure-myapp-kieserver" created
      route "myapp-kieserver" created
      route "secure-myapp-kieserver" created
      imagestream "myapp-kieserver" created
      buildconfig "myapp-kieserver" created
      deploymentconfig "myapp-kieserver" created
  --> Success
      Build scheduled, use 'oc logs -f bc/myapp-kieserver' to track its progress.
      Run 'oc status' to view your app.
```

Now you can deploy the [library-client](library-client) in the same or another project and test RHBA Kie Server container.

To deploy the library process client you can use the **eap64-basic-s2i** (It is available in the OpenShift Catalog) template and specify the above quickstart to be deployed.
To do so, execute the following commands:

```bash
oc new-app eap64-basic-s2i \
    -p SOURCE_REPOSITORY_URL=https://github.com/jboss-container-images/rhba-7-openshift-image.git \
    -p SOURCE_REPOSITORY_REF=rhba70-dev \
    -p CONTEXT_DIR=quickstarts/library-process
```

As result you should see something like this:
```bash
--> Deploying template "openshift/eap64-basic-s2i" to project rhba-kieserver

     JBoss EAP 6.4 (no https)
     ---------
     An example EAP 6 application. For more information about using this template, see https://github.com/jboss-openshift/application-templates.

     A new EAP 6 based application has been created in your project.

     * With parameters:
        * Application Name=eap-app
        * Custom http Route Hostname=
        * Git Repository URL=https://github.com/jboss-container-images/rhba-7-openshift-image.git
        * Git Reference=rhba70-dev
        * Context Directory=quickstarts/library-process
        * Queues=
        * Topics=
        * HornetQ Password=BWJ2yui6 # generated
        * Github Webhook Secret=6pDyrJ7T # generated
        * Generic Webhook Secret=dIlJdN3B # generated
        * ImageStream Namespace=openshift
        * JGroups Cluster Password=Oq4cTP8s # generated
        * Deploy Exploded Archives=false
        * Maven mirror URL=
        * ARTIFACT_DIR=

--> Creating resources ...
    service "eap-app" created
    route "eap-app" created
    imagestream "eap-app" created
    buildconfig "eap-app" created
    deploymentconfig "eap-app" created
--> Success
    Build scheduled, use 'oc logs -f bc/eap-app' to track its progress.
    Run 'oc status' to view your app.

```

After the application is built, access the library process client app through the route created:

```bash
$ oc get routes eap-app
NAME      HOST/PORT                                         PATH      SERVICES   PORT      TERMINATION   WILDCARD
eap-app  eap-app-rhba-kieserver.<your_openshift_suffix>               eap-app    <all>                   None
```

Note that this route should be resolvable.

And example of request would be something like this:

```bash
http://eap-app-rhba-kieserver.<your_openshift_suffix>/library?command=runRemoteRest&protocol=http&host=myapp-kieserver-rhba-kieserver.<your_openshift_suffix>&port=80&username=executionUser&password=<the_generated_kie_password>
```

The password was generated during the app creation in the previous steps, look for **KIE Server Password**.

#### Found an issue?
Feel free to report it [here](https://github.com/jboss-container-images/rhba-7-openshift-image/issues/new).
