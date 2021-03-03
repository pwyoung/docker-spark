MAKEFILE_PATH:=$(dir $(realpath $(firstword $(MAKEFILE_LIST))))
.PHONY=all test-hs test-without-hs run build clean usage FORCE

# Specify which docker-compose file to use.
# https://docs.docker.com/compose/reference/envvars/#compose_file
DC_FILE:=./docker-compose.hs.yml
DOCKER_COMPOSE:=COMPOSE_FILE=$(DC_FILE) docker-compose

# Location of local spark distro
SPARK_HOME:=/spark

TEST_MASTER_AND_WORKER:=cd $(SPARK_HOME) && time ./bin/spark-submit --class org.apache.spark.examples.SparkPi --master spark://localhost:7077 /spark/examples/jars/spark-examples_2.12-3.0.2.jar 100

# For the History Server
LOCAL_SPARK_EVENTS:=/tmp/spark-events-local
# Fix the permissions on the local dir bound to the history server
PREP_HS:=mkdir -p $(LOCAL_SPARK_EVENTS) && sudo chown -R $$USER $(LOCAL_SPARK_EVENTS) && ls -ld $(LOCAL_SPARK_EVENTS)
# Run the test and write events to the dir that is monitored by the history server
TEST_HS:=cd $(SPARK_HOME) && time ./bin/spark-submit --conf "spark.eventLog.enabled=true" --conf "spark.eventLog.dir=file://$(LOCAL_SPARK_EVENTS)" --class org.apache.spark.examples.SparkPi --master spark://localhost:7077 /spark/examples/jars/spark-examples_2.12-3.0.2.jar   100
# View the history server as a web page and via the REST/JSON API
BROWSER:=xdg-open
VIEW_HS:=if command -v $(BROWSER) >/dev/null; then $(BROWSER) http://localhost:18081 & $(BROWSER) http://localhost:18081/api/v1/applications & fi

all: test

usage: FORCE
	$(info examples: "make; make test-hs; BROWSER=firefox make test-hs")

test: test-without-hs test-hs

test-hs: run
	$(PREP_HS)
	$(TEST_HS)
	$(VIEW_HS)

test-without-hs: run
	$(TEST_MASTER_AND_WORKER)

run: build
	cd $(MAKEFILE_PATH) && $(DOCKER_COMPOSE) up --no-recreate --detach
	cd $(MAKEFILE_PATH) && $(DOCKER_COMPOSE) logs

build: FORCE
	cd $(MAKEFILE_PATH)/.. && ./build.sh

clean: FORCE
	cd $(MAKEFILE_PATH) && $(DOCKER_COMPOSE) down

FORCE:
