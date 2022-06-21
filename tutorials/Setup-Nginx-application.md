
### Create the pre-requisite directory

```execute
mkdir -p /home/student/code-server/nginx-application
cd /home/student/code-server/nginx-application
```

### 1 - Create the namespace

```execute
kubectl create ns nginx-app
```

### 2 - Create the configmap

Create the nginx configuration file index.html.

```execute
cat <<EOF>index.html
<!DOCTYPE html>
<html>
	<head>
	<title>OpenLabs StarterKit Tutorial</title>
	<style>
  body {
  width: 35em;
  margin: 0 auto;
  font-family: Tahoma, Verdana, Arial, sans-serif;
  }
	</style>
	</head>
	<body>
  <h1>Kubernetes and OpenShift StarterKit Tutorial</h1>
	<p>Thank you for going through the Starter Kit Tutorial.</p>
	</body>
</html>
EOF
```

Create a Configmap for the index.html file.

```execute
kubectl create configmap indexconfigmap --from-file=index.html -n nginx-app
```

### 3 - Deploy the application

Create the yaml file to deploy the service and nginx.

```execute
cat <<EOF>nginxapp.yaml
---
apiVersion: v1
kind: Service
metadata:
  name: nginxsvc
  labels:
    app: nginx
spec:
  type: NodePort
  ports:
  - port: 8080
    protocol: TCP
    name: http
    nodePort: 30100
  selector:
    app: nginx
---
apiVersion: v1
kind: Pod
metadata:
  name: starterkit-nginx
  labels:
    app: nginx
spec:
  volumes:
  - name: index-volume
    configMap:
       name: indexconfigmap
  containers:
  - name: nginxhttps
    image: registry.access.redhat.com/ubi8/nginx-120:1-7
    command:
    - nginx
    - -g 
    - "daemon off;"
    ports:
    - containerPort: 8080
    volumeMounts:
    - mountPath: /opt/app-root/src
      name: index-volume
EOF
```

Create the  application

```execute
kubectl create -f nginxapp.yaml -n nginx-app
```

Create the service

```execute
kubectl expose pod starterkit-nginx --port=8080 --name nginxsvc --type=NodePort -n nginx-app
```

### 4 - Verify the setup is complete

```execute
kubectl get svc,pod,configmap -n nginx-app
```

Check the URL in the browser
Follow the URL: http://##SSH.host##:30100 

We have completed the setup of the application.