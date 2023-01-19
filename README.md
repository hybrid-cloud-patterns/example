# A template of a basic GitOps based pattern

### Example Makefile
The below Makefile is an example that can be used to trigger your deployments:
```sh
.PHONY: default
default: help

.PHONY: help
# No need to add a comment here as help is described in common/
help:
	@printf "$$(grep -hE '^\S+:.*##' $(MAKEFILE_LIST) common/Makefile | sort | sed -e 's/:.*##\s*/:/' -e 's/^\(.\+\):\(.*\)/\\x1b[36m\1\\x1b[m:\2/' | column -c2 -t -s :)\n"

%:
	make -f common/Makefile $*

install: operator-deploy #post-install ## installs the pattern, inits the vault and loads the secrets
	@echo "Installed"

post-install: ## Post-install tasks
	make load-secrets
	@echo "Done"

test:
	@make -f common/Makefile PATTERN_OPTS="-f values-global.yaml -f values-example.yaml" test
```
