# Option
#===============================================================
REVISION             := no-git

# Const
#===============================================================
APP_ENVS             := dev stg prd
CDK_DIR              := $(realpath $(dir $(lastword $(MAKEFILE_LIST))))

# CDK: 必要なツール類をセットアップします
cdk-setup:
	@cd $(CDK_DIR) && npm install

# CDK: 全てのソースの整形を行います
cdk-fmt:
	@cd $(CDK_DIR) && npm run fmt

# CDK: 全てのソースのLINTを実行します
cdk-lint:
	@cd $(CDK_DIR) && npm run lint

# CDK: ユニットテストを実行します
cdk-test:
	@cd $(CDK_DIR) && npm test

# CDK: ビルドを実行します
cdk-build:
	@cd $(CDK_DIR) && npm run build

# CDK: リリースビルドを実行します
cdk-release: cdk-fmt cdk-lint cdk-test

# CDK: デプロイします
cdk-deploy: .cdk-check-env .cdk-set-revision
	@cd $(CDK_DIR) && cdk deploy --require-approval never \
		--context env=$(APP_ENV) \
		--context revision=$(REVISION) \
		"$(STACK_NAME)-$(APP_ENV)"

# CDK: デプロイした環境を破棄します
cdk-destroy: .cdk-check-env .cdk-set-revision
	@cd $(CDK_DIR) && cdk destroy --force \
		--context env=$(APP_ENV) \
		--context revision=$(REVISION) \
		"$(STACK_NAME)-$(APP_ENV)"

# CDK: ローカルのtemplateとデプロイしたもののDiffを表示します
cdk-diff:.cdk-check-env .cdk-set-revision cdk-build
	@cd $(CDK_DIR) && cdk diff --context env=$(APP_ENV) \
		--context revision=$(REVISION) \
		"$(STACK_NAME)-$(APP_ENV)"

# CDK: CloudFormationのテンプレートを出力します
cdk-synth: .cdk-check-env .cdk-set-revision cdk-build
	@cd $(CDK_DIR) && cdk synth --context env=$(APP_ENV) \
		--context revision=$(REVISION) \
		"$(STACK_NAME)-$(APP_ENV)"

# internal task
#===============================================================
.cdk-check-env:
ifeq ($(filter $(APP_ENVS),$(APP_ENV)),)
	$(error "invalid APP_ENV=$(APP_ENV)")
endif
ifeq ($(STACK_NAME),)
	$(error "invalid STACK_NAME=$(STACK_NAME)")
endif
ifeq ($(CDK_DEFAULT_ACCOUNT),)
	$(error "invalid CDK_DEFAULT_ACCOUNT=$(CDK_DEFAULT_ACCOUNT)")
endif
ifeq ($(CDK_DEFAULT_REGION),)
	$(error "invalid CDK_DEFAULT_REGION=$(CDK_DEFAULT_REGION)")
endif

.cdk-set-revision:
	$(eval REVISION := $(shell if [[ $$REV = "" ]]; then git rev-parse --short HEAD; else echo $$REV;fi;))
