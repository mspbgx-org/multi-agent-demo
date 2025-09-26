ACCOUNT_ID = 633071168319
REGION = eu-central-1
SERVICE_NAME = search-agent
SUBNET_ID = subnet-041ffe0bf7daeb276
REPO = $(ACCOUNT_ID).dkr.ecr.$(REGION).amazonaws.com/$(SERVICE_NAME)

# uv commands
install:
	uv sync

install-dev:
	uv sync --dev

run:
	uv run uvicorn src.main:app --host 0.0.0.0 --port 8000 --reload

run-prod:
	uv run uvicorn src.main:app --host 0.0.0.0 --port 8000

add:
	uv add $(PACKAGE)

add-dev:
	uv add --dev $(PACKAGE)

create-ecr:
	aws ecr create-repository --repository-name $(SERVICE_NAME) --region $(REGION)

build:
	docker build --platform linux/amd64 -t $(SERVICE_NAME) .

ecr-login:
	aws ecr get-login-password --region $(REGION) | docker login --username AWS --password-stdin $(ACCOUNT_ID).dkr.ecr.$(REGION).amazonaws.com

push: build ecr-login
	docker tag $(SERVICE_NAME) $(REPO):latest
	docker push $(REPO):latest

deploy-to-aws:
	sam build
	sam deploy --stack-name $(SERVICE_NAME) --capabilities CAPABILITY_NAMED_IAM \
	  --parameter-overrides ServiceName=$(SERVICE_NAME) SubnetId=$(SUBNET_ID) AccountId=$(ACCOUNT_ID) Region=$(REGION)
