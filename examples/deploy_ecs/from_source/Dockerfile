# Dagster
FROM python:3.13.4-slim as dagster

COPY ./python_modules/ /tmp/python_modules/


ENV DAGSTER_HOME=/opt/dagster/dagster_home/
RUN mkdir -p $DAGSTER_HOME
WORKDIR $DAGSTER_HOME
COPY dagster.yaml workspace.yaml $DAGSTER_HOME

WORKDIR /tmp

# Install:
# - dagster so we can run `dagster-daemon run`
# - dagster-aws so we can use EcsRunLauncher
# - dagster-postgres so we can use PostgresEventStorage,
#   PostgresRunStorage, and PostgresScheduleStorage
RUN pip install \
  -e python_modules/dagster \
  -e python_modules/dagster-pipes \
  -e python_modules/libraries/dagster-aws \
  -e python_modules/libraries/dagster-postgres

RUN ! pip list --exclude-editable | grep -e dagster

WORKDIR $DAGSTER_HOME

# Webserver
FROM dagster as webserver

WORKDIR /tmp

RUN pip install \
  -e python_modules/dagster-webserver \
  -e python_modules/dagster-graphql \
  -e python_modules/dagster \
  -e python_modules/dagster-pipes

RUN ! pip list --exclude-editable | grep -e dagster

WORKDIR $DAGSTER_HOME

# User Code gRPC Server
# You can include only one set of definitions
# per code location. Consider adding more code locations
# if you require multiple sets of definition.
FROM dagster as user_code
COPY defs.py $DAGSTER_HOME
