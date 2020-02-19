# Kubernetes Lab - Basic

## Lab List

* [Lab 01 - Browse Minikube resource](lab-01.md)
* [Lab 02 - Pod Introduction](lab-02.md)
* [Lab 03 - Deployment Introduction](lab-03.md)
* [Lab 04 - Service Introduction](lab-04.md)
* [Lab 05 - ConfigMap and Secret](lab-05.md)
* [Lab 06 - Label and annotation](lab-06.md)
* [Lab 07 - Monitoring](lab-07.md)
* [Lab 08 - Logging](lab-08.md)
* [Lab 09 - Update Version](lab-09.md)

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

Open Powershell as Administrator. And execute:

```
cd $env:userprofile
```

1. Download and install [VirtualBox](https://www.virtualbox.org/wiki/Downloads). Execute:

```
Invoke-WebRequest "https://download.virtualbox.org/virtualbox/6.0.8/VirtualBox-6.0.8-130520-Win.exe" -OutFile virtualbox.exe;

.\virtualbox.exe;
```

2. Download and install kubectl. Execute:

```
Invoke-WebRequest "https://storage.googleapis.com/kubernetes-release/release/v1.17.2/bin/windows/amd64/kubectl.exe" -OutFile C:\Windows\system32\kubectl.exe;
```

3. Download and install [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/). Execute:

```
Invoke-WebRequest "https://storage.googleapis.com/minikube/releases/latest/minikube-installer.exe" -OutFile minikube-installer.exe;

.\minikube-installer.exe;
```

4. Download and install [Helm](https://helm.sh/docs/using_helm/#installing-helm). Execute:

```
$version="helm-v3.1.0";
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12;
Invoke-WebRequest "https://get.helm.sh/$version-windows-amd64.zip" -OutFile C:\$version.zip;
Expand-Archive -Path C:\$version.zip -DestinationPath C:\$version;

Move-Item -Path C:\$version\windows-amd64\helm.exe -Destination C:\Windows\system32\helm.exe;
```

5. Remove all installation files. Execute:

```
Remove-Item .\virtualbox.exe
Remove-Item .\minikube-installer.exe
Remove-Item .\helm-* -Recurse -Force
```

Recommendation Browser: Chrome
