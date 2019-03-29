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
