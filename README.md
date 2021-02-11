# skaffold-telepresence-demo

[![Open in Cloud Shell](https://gstatic.com/cloudssh/images/open-btn.png)](https://ssh.cloud.google.com/cloudshell/open?cloudshell_git_repo=https://github.com/viglesiasce/skaffold-telepresence-demo&cloudshell_tutorial=README.md&cloudshell_workspace=.)


Originally posted here:
https://blog.getambassador.io/super-fast-inner-development-loops-for-kubernetes-with-skaffold-and-telepresence-1cd3e42ba665

## Run the "production" version of the app.

In this tutorial we'll run two different versions of the app in two different clusters. One version will emulate the production environment. The other will be where we'll do development.

1. Start the production cluster with minikube.

    ```sh
    minikube start --profile prod-cluster
    ```

1. Run `kubectl proxy` in the background so we can get access to our prod service later.

    ```sh
    nohup kubectl proxy &
    ```

1. Skaffold can be used to build our image and deploy the app in one command. In this case we'll use the [Skaffold Profile](https://skaffold.dev/docs/environment/profiles/) `prod` that is configured from production.

    ```sh
    skaffold run --profile prod
    ```

## Skaffold and Cloud Code Basics: Iterative Development

In the next session we'll set up a development cluster and iterate on the changes we want to make for our app.

1. Start the development cluster with minikube.

    ```sh
    minikube start --profile dev-cluster
    ```

1.  Click
    <walkthrough-editor-spotlight spotlightId="cloud-code-status-bar">Cloud
    Code</walkthrough-editor-spotlight> in the status bar

1.  Select <walkthrough-editor-spotlight spotlightId="cloud-code-run-on-k8s">Run
    on Kubernetes</walkthrough-editor-spotlight>, confirm that you want to use
    the "dev-cluster" context

1.  Once your app is deployed, launch it with the link displayed in your
    <walkthrough-editor-spotlight spotlightId="output">Output</walkthrough-editor-spotlight>
    panel, near the text

    ```terminal
    http://localhost:8080
    Update successful
    ```

    You can open the web preview by clicking the <walkthrough-web-preview-icon/>
    button and selecting "Preview on port 8080".

## Modify the app

1. Open the <walkthrough-editor-open-file filePath="app/src/components/HelloWorld.js" startLine="6" endLine="6">HelloWorld.js</walkthrough-editor-open-file> file and change it to say `Hello From Skaffold!`.

1. Save the file. You should see Skaffold sync your chanes to the running container and reload the app autoamtically in the <walkthrough-editor-spotlight spotlightId="output">Output</walkthrough-editor-spotlight>
    panel.

1. Next, we'll make a bit bigger of a chance by changing the version of a dependency. Open the 
   <walkthrough-editor-open-file filePath="package.json" startLine="22" endLine="22">package.json</walkthrough-editor-open-file> file and downgrade the version of React from `16.13.1` to `16.13.0`.

   This change requires a rebuild of the Docker image so you'll see Skaffold do a full rebuild of your image and then redeploy your changes by updating the Kubernetes manifests to point to the newly built image.

You've now seen the tight feedback loop you can create when using Skaffold and Cloud Code. Up next we'll leverage Telepresence to start receiving some of the production traffic on this development version

## Introduce Telepresence for realistic testing in dev

1. Switch to the prodcution cluster's Kubernetes context:

    ```sh
    kubectx prod-cluster
    ```

1. Download the Telepresence binary

   ```sh
   sudo curl -fL https://app.getambassador.io/download/tel2/linux/amd64/latest/telepresence -o /usr/local/bin/telepresence
   sudo chmod +x /usr/local/bin/telepresence
   ```

1. Check that you can access the service by using Web Preview. Hold the Ctrl and click the link output by this command.

    ```sh
    echo http://localhost:8001/api/v1/namespaces/default/services/node/proxy/
    ```
