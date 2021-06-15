.PHONY: help build push test

PLATFORMS := linux/amd64
REGISTRY_ALIAS := t2e3n6p8
IMAGE_NAME := debian-buster-tflite

help:
	@echo 'help'

build:
	docker buildx build --progress plain --platform $(PLATFORMS) -t public.ecr.aws/$(REGISTRY_ALIAS)/$(IMAGE_NAME):latest --load .

push:
	docker buildx build --progress plain --platform $(PLATFORMS) -t public.ecr.aws/$(REGISTRY_ALIAS)/$(IMAGE_NAME):latest --push .

test:
	docker buildx build --progress plain --platform $(PLATFORMS) -t $(IMAGE_NAME)-test --load . -f Dockerfile-test
