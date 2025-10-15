OTG_API := https://clab-campus-fabric-ixia-voice:8443
.DEFAULT_GOAL = help

### Generic Variables
SHELL := /bin/bash

################################################################################
# CAMPUS Fabirc Simulation
################################################################################
.PHONY: help
help: ## Display help message (*: main entry points / []: part of an entry point)
	@grep -E '^[0-9a-zA-Z_-]+\.*[0-9a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

.PHONY: start
start: ## Start container lab topology
	sudo containerlab deploy

.PHONY: stop
stop:  ## Stop container lab topology
	sudo containerlab destroy --graceful --keep-mgmt-net

.PHONY: clean
clean: ## Clean logs and files related to clab and avd
	-sudo containerlab destroy --cleanup 
	rm -f .*.clab.yml* avd/ansible.log
	sudo rm -rf clab-*
	sudo rm -rf avd/intended/

.PHONY: save
save: ## Save clab topology
	sudo containerlab save

.PHONY: build
build:  ## Build the configuration for the Network via AVD
	cd avd && ansible-playbook build.yml

.PHONY: deploy
deploy: ## Deploy the configuration for the Network to CVP or CVaaS
	cd avd && ansible-playbook deploy.yml

.PHONY: flows
flows: ## Run the traffic generator flows
	otgen run --file otg.yml --yaml -k --metrics flow --api ${OTG_API} --log debug  --timeout 20m | \
	otgen transform --metrics flow  --counters frames | \
	otgen display --mode table

.PHONY: ping-IDF1-IDF3
ping-IDF1-IDF3: ## ping from IDF1 to IDF2
	ssh admin@clab-campus-fabric-idf1-laptop ping -i 0.5 10.3.10.10
.PHONY: test
test: ## Test the Campus Fabruic using ANTA

.PHONY: update-collections
update-collections: ## Update arista.avd collections to latest release branch
	ansible-galaxy collection install arista.avd --upgrade
	ansible-galaxy collection install community.general --upgrade
	ansible-galaxy collection install ansible.posix --upgrade
	pip install "pyavd[ansible]"


