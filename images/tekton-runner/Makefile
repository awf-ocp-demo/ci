SHELL := /bin/bash
OCI_CLI := podman
IMG_REG := quay.io
IMG_NS := lrossett/tekton-runner
IMG_TAG := latest
IMG_BUILD_DIR := .
IMG_BUILD_OPTS :=
IMG_FQN := ${IMG_REG}/${IMG_NS}:${IMG_TAG}

.PHONY: image/build
image/build:
	${OCI_CLI} build ${IMG_BUILD_OPTS} -t ${IMG_FQN} ${IMG_BUILD_DIR}

.PHONY: image/push
image/push:
	${OCI_CLI} push ${IMG_FQN}

.PHONY: image
image: image/build image/push
