## Introduction
In this part you will perform the following tasks:
- Deploy Chuck Norris app version 2 in dev and test
- Deploy Chuck Norris app version 2 in prod


## Upgrading Chuck Norris App with Flux
Let’s assume your developers would like to deploy new version of Chuck Norris app. The new version has been pushed by developers to Docker Hub container registry as `maty0609/clus2022-chuck-app:v2`

Now let’s deploy the new version in the real GitOps fashion. We don’t want to deploy version 2 straight into production so we will want to deploy new version in `dev` environment first. Let’s go into our Chuck Norris app repository and switch to `dev` branch.

```bash
cd /home/developer/src/CLEU2023-DEVWKS-2998
git checkout dev
```

We will change image name in the file `./env/dev/deployment.yaml` from `maty0609/clus2022-chuck-app:v1` to `maty0609/clus2022-chuck-app:v2`. The whole manifest should look like this:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: chuck-norris-app
  namespace: dev
spec:
  replicas: 1
  selector:
    matchLabels:
      app: chuck-norris-app
  template:
    metadata:
      labels:
        app: chuck-norris-app
    spec:
      containers:
      - image: maty0609/clus2022-chuck-app:v2
        name: chuck-norris-app
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
      restartPolicy: Always
```

Now when we have changed the file `./env/dev/deployment.yaml` let’s push the changes:
```bash
git add .
git commit -m "Deploy v2 in dev"
git push
```

Let’s check reconciliation:
```bash
flux get kustomization -w
```

After the cluster has been reconciled we can check the new version running in `dev`. Access port `8081` and you should see our new version 2. It proves that we are now running version 2 in `dev` environment.

![Untitled](./images/chuck-norris-app-v2.png)

Everything looks great in `dev` environment so let’s change it in `prod` as well. We will change image name in the file `./app/prod.yaml`

```bash
cd /home/developer/src/CLEU2023-DEVWKS-2998
git checkout main
```

We will change image in the file `./env/prod/deployment.yaml` from `maty0609/clus2022-chuck-app:v1` to `maty0609/clus2022-chuck-app:v2`. The whole manifest should look like this:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: chuck-norris-app
  namespace: prod
spec:
  replicas: 1
  selector:
    matchLabels:
      app: chuck-norris-app
  template:
    metadata:
      labels:
        app: chuck-norris-app
    spec:
      containers:
      - image: maty0609/clus2022-chuck-app:v2
        name: chuck-norris-app
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
      restartPolicy: Always
```

Now when we have changed the file `./env/prod/deployment.yaml` let’s push changes:
```bash
git add .
git commit -m "Deploy v2 in prod"
git push
```

Let’s check reconciliation:
```bash
flux get kustomization -w
```

We can now check if the new version has been deployed in `prod`. Because the version 1 has been replaced with version 2 we will need to re-run `kubectl port-forward` for `prod` environment. In the window where you have previously ran it or in the new window run this command:
`kubectl port-forward service/chuck-norris-app 8090:8080 -n prod`

Open or refresh Chuck Norris App in `prod` environment via this link: `https://app-8080-xxxx.devenv-testing.ap-ne-1.devnetcloud.com`.


## Wrapping It Up
In this workshop you've learned the basics of how to use Flux:
- Install Flux
- Bootstrap your cluster with Flux
- Deploy the app with Flux according to GitOps best practices
- Upgrade the app with Flux

Arms with the above, you should be able to start managing your Kubernetes cluster with Flux. Hopefully you had fun, and find this enjoyable!
