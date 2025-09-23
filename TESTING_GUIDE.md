# Helm Charts Testing

This document describes the testing infrastructure for the CloudPirates Helm charts repository.

## Overview

We have implemented comprehensive testing for all Helm charts in this repository using multiple approaches:

1. **GitHub Actions Workflows** - Automated testing on every push/PR
2. **Local Testing Script** - For testing charts locally during development
3. **Unit Tests** - Using helm-unittest plugin

## GitHub Actions Workflows

### 1. Test Helm Charts (`test-charts.yaml`)

This workflow runs on every push and pull request to the main branch. It:

- Creates a matrix job for each chart
- Sets up a kind cluster for each chart test
- Installs, tests, and uninstalls each chart individually
- Runs unit tests using the existing `test-all-charts.sh` script

**Features:**
- Parallel execution of chart tests
- Individual kind cluster per chart (no interference)
- Comprehensive testing (lint, template, install, test, uninstall)
- Unit test execution

### 2. Chart Integration Tests (`chart-integration-tests.yaml`)

This is a more comprehensive workflow that runs:

- On a daily schedule (2 AM UTC)
- On manual trigger with optional chart selection
- With advanced features like upgrade testing and better error collection

**Features:**
- Automatic chart discovery
- Manual chart selection support
- Upgrade testing
- Detailed error collection and logging
- Parallel execution with concurrency limits

## Local Testing

### Prerequisites

Before running tests locally, ensure you have:

- [Docker](https://docs.docker.com/get-docker/)
- [kind](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Helm](https://helm.sh/docs/intro/install/)

### Using the Local Testing Script

The `test-chart-locally.sh` script allows you to test charts on your local machine:

```bash
# Test all charts
./test-chart-locally.sh

# Test a specific chart
./test-chart-locally.sh nginx

# Test without cleaning up the cluster (useful for debugging)
./test-chart-locally.sh --no-cleanup nginx

# Use a custom cluster name
./test-chart-locally.sh --cluster-name my-test-cluster redis
```

### Manual Testing

You can also test charts manually:

```bash
# Create a kind cluster
kind create cluster --name chart-test

# Add required repositories
helm repo add cloudpirates https://charts.cloudpirates.io
helm repo update

# Test a specific chart
cd charts/nginx
helm dependency update
helm lint .
helm install test-nginx . --create-namespace --namespace test-nginx

# Verify the installation
kubectl get all -n test-nginx
helm test test-nginx -n test-nginx

# Cleanup
helm uninstall test-nginx -n test-nginx
kubectl delete namespace test-nginx
kind delete cluster --name chart-test
```

## Unit Testing

Unit tests use the [helm-unittest](https://github.com/helm-unittest/helm-unittest) plugin and can be run using:

```bash
# Run all unit tests
./test-all-charts.sh

# Install helm unittest plugin if not already installed
helm plugin install https://github.com/helm-unittest/helm-unittest

# Run tests for a specific chart
helm unittest charts/nginx
```

## Testing Strategy

Our testing strategy covers multiple aspects:

1. **Syntax Validation**: YAML syntax and Kubernetes resource validation
2. **Template Rendering**: Ensure templates render without errors
3. **Linting**: Follow Helm best practices
4. **Installation**: Actual deployment in a Kubernetes cluster
5. **Functionality**: Run Helm tests if defined
6. **Upgrade**: Test chart upgrades
7. **Cleanup**: Ensure proper resource cleanup

## Chart Test Requirements

For charts to be properly tested, they should:

1. **Have valid dependencies**: All dependencies should be properly defined in `Chart.yaml`
2. **Include tests**: Define Helm tests in the `templates/tests/` directory
3. **Use proper labels**: Follow Kubernetes and Helm labeling conventions
4. **Handle upgrades**: Be upgrade-safe and backwards compatible
5. **Resource limits**: Define appropriate resource requests and limits

### Example Test Structure

```
charts/mychart/
├── Chart.yaml
├── values.yaml
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── tests/
│       ├── test-connection.yaml
│       └── test-api-response.yaml
└── tests/           # helm-unittest tests
    └── deployment_test.yaml
```

## Troubleshooting

### Common Issues

1. **Dependency Issues**: Ensure all dependencies are properly defined and accessible
2. **Resource Conflicts**: Use unique namespaces and release names
3. **Timeout Issues**: Increase timeouts for slow-starting applications
4. **Permission Issues**: Ensure proper RBAC configurations

### Debugging Failed Tests

```bash
# Check what went wrong with a specific chart
./test-chart-locally.sh --no-cleanup mychart

# Inspect the cluster state
kubectl get all --all-namespaces
kubectl describe pod <pod-name> -n <namespace>
kubectl logs <pod-name> -n <namespace>

# Check Helm releases
helm list --all-namespaces

# Don't forget to cleanup when done
kind delete cluster --name helm-chart-test
```

## Contributing

When adding new charts or modifying existing ones:

1. Run local tests before submitting a PR
2. Ensure all tests pass in the GitHub Actions
3. Add appropriate unit tests for new functionality
4. Update this documentation if adding new testing features

## Monitoring and Maintenance

The testing infrastructure should be regularly maintained:

- Update tool versions (kind, kubectl, helm) as needed
- Review and update test timeouts based on application requirements  
- Monitor test execution times and optimize as needed
- Keep dependencies up to date
