# DevOps and Cloud Based Software  

# 1. Introduction 

In this tutorial will use GitOps practices with FastAPI including CI/CD pipelines, code quality tools, and automated testing.

## Resources
* FastAPI
  - [Official Documentation](https://fastapi.tiangolo.com/)
  - [Tutorial - User Guide](https://fastapi.tiangolo.com/tutorial/)

* GitOps
  - [GitOps Principles](https://www.gitops.tech/)
  - [GitHub Actions Documentation](https://docs.github.com/en/actions)

* Code Quality
  - [Ruff Documentation](https://docs.astral.sh/ruff/)
  - [Black Documentation](https://black.readthedocs.io/)
  - [Pre-commit Documentation](https://pre-commit.com/)

* Testing
  - [Pytest Documentation](https://docs.pytest.org/)

* Docker
  - [Docker Documentation](https://docs.docker.com/)

* Kubernetes & Helm
  - [Kubernetes Documentation](https://kubernetes.io/docs/home/)
  - [Helm Documentation](https://helm.sh/docs/)
  - [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)




# 2. Tutorial 

This tutorial will teach you:
- Building REST APIs with FastAPI
- Containerizing applications with Docker
- Setting up CI/CD pipelines with GitHub Actions
- Implementing code quality checks
- Using pre-commit hooks for automated code validation
- Following GitOps principles

Prerequisites:
- Python 3.11 or higher
- Git
- Docker (optional, for containerization)
- GitHub account

## Getting Started
* Clone the Repository:
   ```bash
   git clone https://github.com/DevOps-and-Cloud-based-Software/fastapi-gitops.git
   cd fastapi-gitops-starter
   ```

* Set Up Python Environment:
   
   ```bash
   # Create a virtual environment
   python -m venv venv
   
   # Activate the virtual environment
   # On Linux/MacOS:
   source venv/bin/activate
   # On Windows:
   venv\Scripts\activate
   
   # Install dependencies
   pip install -r requirements.txt
   ```

* Run the Application:
   ```bash
   uvicorn app.main:app --reload
   ```

* Visit http://localhost:8000 to see your API running!

* Explore the API:
  - API Documentation: http://localhost:8000/GitOps-Starter/docs
  - Root endpoint: http://localhost:8000/GitOps-Starter
  - Health check: http://localhost:8000/GitOps-Starter/health
  - List items: http://localhost:8000/GitOps-Starter/api/items
  - Get specific item: http://localhost:8000/GitOps-Starter/api/items/1

## Testing

* Run Tests:
   ```bash
   pytest
   ```

  * Run Tests with Coverage:
   ```bash
   pytest --cov=app --cov-report=html
   ```

* View the coverage report by opening `htmlcov/index.html` in your browser.

##  Code Quality

* Linting with Ruff:

   ```bash
   # Check for issues
   ruff check app/ tests/
   
   # Fix auto-fixable issues
   ruff check app/ tests/ --fix
   ```

* Code Formatting with Black

   ```bash
   # Check formatting
   black --check app/ tests/
   
   # Format code
   black app/ tests/
   ```

## Pre-commit Hooks

Pre-commit hooks automatically check your code before each commit, ensuring
consistent code quality.

* Setup Pre-commit:

   ```bash
   # Install pre-commit
   pip install pre-commit
   
   # Install the git hooks
   pre-commit install
   ```

* Using Pre-commit:

   Pre-commit will now run automatically on `git commit`. You can also run it manually:
   
   ```bash
   # Run on all files
   pre-commit run --all-files
   
   # Run on staged files
   pre-commit run
   ```

* The pre-commit hooks include:
  - Trailing whitespace removal
  - End of file fixer

## Docker

* Build the Docker Image:
   ```bash
   docker build -t fastapi-gitops-starter .
   ```

* Run the Container:

   ```bash
   docker run -p 8000:8000 fastapi-gitops-starter
   ```
* Access the API at http://localhost:8000/GitOps-Starter/

## Minikube Setup

If you want to test the Kubernetes deployment locally, you can use Minikube.

* Install Minikube: Follow the instructions at the [Minikube installation guide](https://minikube.sigs.k8s.io/docs/start/).

* Start Minikube with Ingress and Ingress-DNS Addons:
   ```bash
   minikube start --addons=ingress,ingress-dns
   ```

* Add Minikube IP to /etc/hosts:
* Get the Minikube IP:
   ```bash
   minikube ip
   ```
* Add the following line to your `/etc/hosts` file:
   ```
   <MINIKUBE_IP> minikube.test
   ```
   Replace `<MINIKUBE_IP>` with the IP address obtained from the previous command.


## Kubernetes Deployment with Helm

This repository includes a Helm chart for deploying the application to Kubernetes.

* Prerequisites
  - Kubernetes 1.19+
  - Helm 3.0+

* Install the Helm Chart: 

   ```bash
   helm install my-release ./helm/fastapi-gitops-starter
   ```

* Uninstall the Helm Chart: 
   
   ```bash
   helm uninstall my-release
   ```

Look at the `helm/README.md` for more details on configuration options.
Make sure you understand how to set up the Horizontal Pod Autoscaler (HPA) for
scaling based on load and ingress configuration for accessing the application
including host and paths.

## Exercise 1: Add pre-commit Hooks
1. Open `.pre-commit-config.yaml`
2. Add a new hook to check:
   * if we try to commit large files
   * To check YAML files for syntax errors (make sure to exclude helm chart files)
   * To sort imports in Python files
   * To check for security issues using
   * To make sure we do not commit secrets using
   * To check code style



## Exercise 2: Add a New Endpoint

1. Open `app/main.py`
2. Add a new endpoint to create an item:

```python
@app.post("/api/items")
async def create_item(name: str, description: str):
    """Create a new item."""
    return {
        "id": 999,
        "name": name,
        "description": description,
        "created": True
    }
```

3. Write a test in `tests/test_main.py`
4. Run tests to verify


## Exercise 3: Add a CI Pipeline

1. Open `.github/workflows/ci-cd.yml`
2. Add a step to lint the code using Ruff
3. Add a step to run tests with coverage. The pipeline should fail if coverage is below 80%
4. Add a step to build the Docker image if tests pass. If we do a release, tag the image appropriately (with its version and the tag 'latest') and push it to GitHub registry


## Exercise 4: Deploy on a K8s "production" Cluster

1. Set up a Kubernetes cluster (e.g., using Minikube or a cloud provider)
2. Deploy the application using the Helm chart
3. Set up the scaling parameters in `custom-values.yaml` to handle more load by enabling Horizontal Pod Autoscaler (HPA). Set targetCPUUtilizationPercentage to 10%
4. Perform a load test using `hey`
```commandline
hey -z 30s  http://minikube.test/GitOps-Starter/api/items
```
6. Monitor the scaling of pods in the cluster:
```bash
kubectl get hpa -n default -w
```
7. Note how much time it takes for the pods to scale up and down based on the load

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request
6. Ensure all tests pass and code quality checks are successful
7. Merge

## Tips

1. **Start Simple**: Understand each component before moving to the next
2. **Read the Logs**: When something fails, the CI/CD logs contain valuable information

# 3 Questions

## Pre-commit Hooks
1. How can pre-commit hooks improve code quality and consistency?
2. How do you configure pre-commit hooks to run only on specific file types or directories?

## Code Coverage
1. What are the benefits of maintaining high code coverage in a project?
2. What other metrics can be used to assess code quality besides code coverage?

## CI/CD and Deployment
1. What are some strategies for rolling back deployments in case of failures in the CI/CD pipeline?
2. How can you set up automatic deployments in a production environment while minimizing downtime and ensuring KPOs are met?

## Kubernetes and Helm
1. The auto-scaling did not work as expected. What could be the possible reasons?
2. How does Horizontal Pod Autoscaling (HPA) work in Kubernetes?