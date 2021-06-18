# Kubernetes Operators

[![ci](https://github.com/nearform/web-dev-perf/actions/workflows/ci.yml/badge.svg)](https://github.com/nearform/web-dev-perf/actions/workflows/ci.yml)

This repository contains an example Kubernetes Operator generated by [Operator-Framework](https://operatorframework.io/) based on [Kubebuilder](https://kubebuilder.io/).

## Motivation

We want to give a base knowdlege of concepts and design of [Kubernetes Operator]() written in [Go]() and how it should be implemented and used locally.
Details are described in our [Nearform Blog](https://www.nearform.com/blog/)


## Setup

### Install the operator framework
```bash
$ curl -LO https://github.com/operator-framework/operator-sdk/releases/download/v1.8.0/operator-sdk_linux_amd64
$ chmod +x operator-sdk_linux_amd64 && sudo mv operator-sdk_linux_amd64 /usr/local/bin/operator-sdk
```

### Create a kinD cluster locally

How to Install [kinD](https://kind.sigs.k8s.io/), please look to the [install instruction](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)

```bash
$ kind create cluster --kubeconfig kinD/kind.kubeconf --config kinD/config.yml
$ export KUBECONFIG=$(pwd)/kinD/kind.kubeconf
```

### Setup a docker registry

We need a [docker](https://docs.docker.com/engine/install/) registry locally to be used by kinD to pull our kubernetes controller.
```bash
$ docker run -d --restart=always -p "127.0.0.1:5000:5000" --name "kind-registry" registry:2
$ docker network connect "kind" "kind-registry" || true
$ kubectl apply -f kinD/manifest.yml
```

## Deploy

```bash
$ make docker-build docker-push IMG="localhost:5000/template-operator:v0.0.1"
$ make deploy IMG="localhost:5000/template-operator:v0.0.1"
$ kubectl apply -f config/samples/core_v1alpha1_template.yaml
```

## Developing

You can create a new Operator Project.

```bash
$ cd $PROJECT_HOME
$ operator-sdk init --repo github.com/Nearform/template-operator --domain nearform.com
$ operator-sdk create api --group core --version v1alpha1 --kind Template --resource --controller
```

The controller needs to be installed into your local kinD cluster
```bash
make install
```

The Operator SDK created an CRD for you, you can install now.
```bash
kubectl apply -f config/samples/core_v1alpha1_template.yaml
```

The controller can be run locally for debugging and testing.
```bash
make run 
```