# Kubernetes Lab - Basic

## Lab List

* [Lab - Browse Minikube resource](lab-01.md)
* [Lab - Pod Introduction](lab-02.md)
* [Lab - Deployment Introduction](lab-03.md)
* [Lab - Service Introduction](lab-04.md)
* [Lab - ConfigMap and Secret](lab-05.md)
* [Lab - Label and annotation](lab-06.md)
* [Lab - Monitoring](lab-07.md)
* [Lab - Logging](lab-08.md)
* [Lab - Update Version](lab-09.md)

## Requirement

Docker:

* [Docker Desktop for Windows](https://hub.docker.com/editions/community/docker-ce-desktop-windows)
* [Docker Desktop for Mac](https://hub.docker.com/editions/community/docker-ce-desktop-mac)

Follow [Minikube installation](https://kubernetes.io/docs/tasks/tools/install-minikube/) setup environment.

Recommendation:

* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)
* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
* [Helm](https://github.com/helm/helm)

## On Mac OS

1. Download and install [VirtualBox](https://www.virtualbox.org/wiki/Downloads).
2. Open your terminal, and execute `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` to install Homebrew.
3. Open your terminal, and execute `brew install kubernetes-cli` to install `kubectl`.
4. Open your terminal, and execute `brew cask install minikube` to install `minikube`.
5. Open your terminal, and execute `brew install kubernetes-helm` to install `helm`.

## On Windows

Open Powershell as Administrator.

1. Download and install [VirtualBox](https://www.virtualbox.org/wiki/Downloads). Execute:

```
Invoke-WebRequest "https://download.virtualbox.org/virtualbox/6.0.8/VirtualBox-6.0.8-130520-Win.exe" -OutFile C:\virtualbox.exe;
C:\virtualbox.exe;
```

2. Download and install kubectl. Execute:

```
Invoke-WebRequest "https://storage.googleapis.com/kubernetes-release/release/v1.14.0/bin/windows/amd64/kubectl.exe" -OutFile C:\Windows\system32\kubectl.exe;
```

3. Download and install [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/). Execute:

```
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
Invoke-WebRequest "https://github.com/kubernetes/minikube/releases/download/v1.0.1/minikube-installer.exe" -OutFile C:\minikube-installer.exe;
C:\minikube-installer.exe;
```

4. Download and install [Helm](https://helm.sh/docs/using_helm/#installing-helm). Execute:

```
$version="helm-v2.14.0-windows-amd64";
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12;
Invoke-WebRequest "https://storage.googleapis.com/kubernetes-helm/$version.zip" -OutFile C:\$version.zip;
Expand-Archive -Path C:\$version.zip -DestinationPath C:\$version;

Move-Item -Path C:\$version\windows-amd64\helm.exe -Destination C:\Windows\system32\helm.exe;
Move-Item -Path C:\$version\windows-amd64\tiller.exe -Destination C:\Windows\system32\tiller.exe;
```

5. Remove all installation files under `C:\`.

* `C:\virtualbox.exe`
* `C:\minikube-installer.exe`
* `C:\helm-*`

Recommendation Browser: Chrome
