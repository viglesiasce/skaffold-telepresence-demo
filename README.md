# skaffold-telepresence-demo

[![Open in Cloud Shell](https://gstatic.com/cloudssh/images/open-btn.png)](https://ssh.cloud.google.com/cloudshell/open?cloudshell_git_repo=https://github.com/viglesiasce/skaffold-telepresence-demo&cloudshell_tutorial=README.md)


Originally posted here:
https://blog.getambassador.io/super-fast-inner-development-loops-for-kubernetes-with-skaffold-and-telepresence-1cd3e42ba665

## Skaffold Basics: Iterative Development

1. Start minikube.

    ```sh
    minikube start
    ```

1. Clone the sample app from the Skaffold repo.

    ```sh
    git clone git@github.com:GoogleContainerTools/skaffold.git
    cd skaffold/examples/react-reload
    ```

1. Run skaffold to start the iteration loop.

    ```sh
    skaffold dev --port-forward
    ```

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

## Introduce Telepresence for realistic testing in dev

1. Download the Telepresence binary

   ```sh
   sudo curl -fL https://app.getambassador.io/download/tel2/linux/amd64/latest/telepresence -o /usr/local/bin/telepresence
   sudo chmod +x /usr/local/bin/telepresence
   ```

1. Login to telepresence.

    ```sh
    telepresence login
    ```

1. Run a second version of the app that will emulate our production version of the app.

    ```sh
    kubectl create deployment node-production --image=react-reload --port=8080
    kubectl expose deployment node --type=LoadBalancer --port=8080 --target-port=8080
    ```
