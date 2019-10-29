CircleCI orbs
---

Execute your Ghost Inspector tests or suites using CircleCI `v2.1` orbs.

### Build status
| Command | Status |
| --- | --- |
| `ghostinspector/execute-suite` | ![Orb Suite](https://api.ghostinspector.com/v1/suites/5bdb1f808faf2b12926dcd62/status-badge) |
| `ghostinspector/execute-test` | ![test suite](https://api.ghostinspector.com/v1/tests/5bdb20218faf2b12926dd9c9/status-badge) |
| `ghostinspector/test-docker-app` | ![docker suite](https://api.ghostinspector.com/v1/suites/5bdb3d05d69d3646d07b4af1/status-badge) |


## Usage

We offer three commands for testing your application using CircleCI orbs and Ghost Inspector:

 * `ghostinspector/execute-suite` - Execute your test suite and optionally wait for the results
 * `ghostinspector/execute-test` - Execute a single test and optionally wait for the results
 * `ghostinspector/test-docker-app` - Execute your test suite against your containerized application 

### Set up
Set up an environment variable in your CircleCI build called `GI_API_KEY`. This will be used
automatically by all commands. You may also pass the `api-key` parameter explicitly.

### Examples
Example executing a test suite:
```
version: 2.1
orbs:
  ghostinspector: ghostinspector/test-runner@1.0.0
jobs:
  build:
    executor: ghostinspector/default
    steps:
      - ghostinspector/execute-suite:
          id: <my-suite-id>
          extra-params: '{"myVar": "foo", "otherVar": "bar"}'
          wait: true
```
Example executing a single test:
```
version: 2.1
orbs:
  ghostinspector: ghostinspector/test-runner@1.0.0
jobs:
  build:
    executor: ghostinspector/default
    steps:
      - ghostinspector/execute-test:
          id: <my-test-id>
          extra-params: '{"foo": "bar", "other": "var"}'
          wait: true
```

Example executing a test suite against a running Docker container:
```
version: 2.1
orbs:
  ghostinspector: ghostinspector/test-runner@1.0.0
executors:
  docker-executor:
    docker:
      - image: docker:17.05.0-ce-git
jobs:
  build_and_test:
    executor: docker-executor
    steps:
      - setup_remote_docker
      - run:
          name: Set up temporary docker network
          command: docker network create my-docker-network
      - run:
          name: Start your application container, specifying application port and docker network
          command: |
            docker run -d \
              -e PORT=8080 \
              --network my-docker-network \
              --name my-app \
              my-docker-image
      - ghostinspector/test-docker-app:
          api-key: <my-ghost-inspector-api-key>
          suite-id: <my-ghost-inspector-suite-id>
          network: my-docker-network
          vpn-token: <my-ngrok-token>
          vpn-target: my-docker-image:8080
```
