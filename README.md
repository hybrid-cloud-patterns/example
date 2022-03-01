# A template of a basic GitOps based pattern

### Example Makefile
The below Makefile is an example that can be used to trigger your deployments:
```sh
BOOTSTRAP=1
ARGO_TARGET_NAMESPACE=demo-namespace
PATTERN=vessel-id
COMPONENT=datacenter
SECRET_NAME="argocd-env"
TARGET_REPO=$(shell git remote show origin | grep Push | sed -e 's/.*URL://' -e 's%:[a-z].*@%@%' -e 's%:%/%' -e 's%git@%https://%' )
CHART_OPTS=-f common/examples/values-secret.yaml -f values-global.yaml -f values-datacenter.yaml --set global.targetRevision=main --set global.valuesDirectoryURL="https://github.com/pattern-clone/pattern/raw/main/" --set global.pattern="dema-pattern" --set global.namespace="pattern-namespace"
NAME=$(shell basename `pwd`)

.PHONY: default
default: show

%:
	echo "Delegating $* target"
	make -f common/Makefile $*

install: deploy
ifeq ($(BOOTSTRAP),1)
	echo "Bootstrapping Demo Pattern"
endif

predeploy:
	./scripts/precheck.sh

update: upgrade
ifeq ($(BOOTSTRAP),1)
	echo "Bootstrapping Demo Pattern"
	make bootstrap
endif

bootstrap:

test:
	make -f common/Makefile CHARTS="$(wildcard charts/datacenter/*)" PATTERN_OPTS="-f values-datacenter.yaml" test
	make -f common/Makefile CHARTS="$(wildcard charts/factory/*)" PATTERN_OPTS="-f values-factory.yaml" test
```
