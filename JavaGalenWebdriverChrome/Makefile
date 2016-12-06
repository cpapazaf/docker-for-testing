REGISTRY		= cpapazaf
IMAGE			= java-galen-webdriver-chrome
GALEN_VERSION 		?= 2.3.0
CHROME_VERSION		?= google-chrome-stable
CHROME_DRIVER_VERSION	?= 2.24
VERSION			?= g$(GALEN_VERSION)_c$(CHROME_VERSION)_cd$(CHROME_DRIVER_VERSION)
LATEST_VERSION		?= $(VERSION)

# docker might or might not require sudo
# # detect this automatically to simplify life a bit
DOCKER=$(shell docker info >/dev/null 2>&1 && echo "docker" || echo "sudo docker")

all: build

build:
	@$(DOCKER) build \
		--build-arg GALEN_VERSION=$(GALEN_VERSION) \
		--build-arg CHROME_VERSION=$(CHROME_VERSION) \
		--build-arg CHROME_DRIVER_VERSION=$(CHROME_DRIVER_VERSION) \
		--pull=true \
		-t $(REGISTRY)/$(IMAGE):$(VERSION) \
		.

latest: build
	@$(DOCKER) tag $(REGISTRY)/$(IMAGE):$(VERSION) $(REGISTRY)/$(IMAGE):latest

push:
	@$(DOCKER) push $(REGISTRY)/$(IMAGE):$(VERSION)
	@$(DOCKER) push $(REGISTRY)/$(IMAGE):latest
