REGISTRY		= cpapazaf
IMAGE			= ruby-webdriver-chrome
RUBY_VERSION_MAJOR 	?= 2.2
RUBY_VERSION_PATCH	?= 3
RUBY_VERSION		= $(RUBY_VERSION_MAJOR).$(RUBY_VERSION_PATCH)
CHROME_VERSION		?= google-chrome-stable
CHROME_DRIVER_VERSION	?= 2.24
VERSION			?= r$(RUBY_VERSION)_c$(CHROME_VERSION)_cd$(CHROME_DRIVER_VERSION)
LATEST_VERSION		?= $(VERSION)

# docker might or might not require sudo
# # detect this automatically to simplify life a bit
DOCKER=$(shell docker info >/dev/null 2>&1 && echo "docker" || echo "sudo docker")

all: build

build:
	@$(DOCKER) build \
		--build-arg RUBY_VERSION_MAJOR=$(RUBY_VERSION_MAJOR) \
		--build-arg RUBY_VERSION_PATCH=$(RUBY_VERSION_PATCH) \
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
