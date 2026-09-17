.DEFAULT_GOAL := help

.PHONY: help init fmt validate plan

help:
	@echo "Targets:"
	@echo "  init      terraform init"
	@echo "  fmt       terraform fmt -recursive -check"
	@echo "  validate  terraform init -backend=false && terraform validate"
	@echo "  plan      terraform plan"

init:
	terraform init

fmt:
	terraform fmt -recursive -check

validate:
	terraform init -backend=false
	terraform validate

plan:
	terraform plan
