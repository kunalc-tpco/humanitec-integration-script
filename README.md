# Humanitec Integration for Port

An integration used to import Humanitec resources into Port, providing comprehensive visibility into your Humanitec applications, environments, resources, secrets, deployments, and pipelines.

## Overview

This integration syncs the following Humanitec entities to Port:

- **Applications** - Humanitec applications
- **Environments** - Application environments (dev, staging, prod, etc.)
- **Workloads** - Application workloads and modules
- **Resources** - Active resources provisioned in environments
- **Resource Graphs** - Dependency graphs between resources
- **Secret Stores** - Organization-level secret stores
- **Shared Values** - Application and environment-level configuration/secrets
- **Value Set Versions** - Versioned sets of shared values
- **Deployment Sets** - Deployment configurations
- **Pipelines** - CI/CD pipeline definitions
- **Deployment Deltas** - Deployment change definitions

## Prerequisites

- Python 3.10 or higher
- Humanitec API access with appropriate permissions
- Port API access with client credentials

## Installation

1. Clone this repository:
```bash
git clone <repository-url>
cd humanitec-integration-script
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

## Configuration

### Environment Variables

Create a `.env` file in the root directory with the following variables:

```env
# Humanitec Configuration
ORG_ID=your-humanitec-org-id
API_KEY=your-humanitec-api-key
API_URL=https://api.humanitec.com

# Port Configuration
PORT_CLIENT_ID=your-port-client-id
PORT_CLIENT_SECRET=your-port-client-secret
```

### Configuration Options

| Variable | Description | Required | Default |
|----------|-------------|----------|---------|
| `ORG_ID` | Your Humanitec organization ID | Yes | - |
| `API_KEY` | Your Humanitec API key/token | Yes | - |
| `API_URL` | Humanitec API base URL | No | `https://api.humanitec.com` |
| `PORT_CLIENT_ID` | Port client ID | Yes | - |
| `PORT_CLIENT_SECRET` | Port client secret | Yes | - |

## Usage

### Running the Integration

#### Method 1: Direct Execution
```bash
cd integration
python main.py
```

#### Method 2: With Command Line Arguments
```bash
cd integration
python main.py \
  --org-id your-humanitec-org-id \
  --api-key your-humanitec-api-key \
  --port-client-id your-port-client-id \
  --port-client-secret your-port-client-secret
```

#### Method 3: GitHub Actions
The integration can be run via GitHub Actions workflow. See the [GitHub Workflow documentation](https://docs.getport.io/build-your-software-catalog/custom-integration/api/ci-cd/github-workflow/guides/humanitec/) for setup instructions.

### Command Line Options

| Option | Description | Required | Default |
|--------|-------------|----------|---------|
| `--org-id` | Humanitec organization ID | No* | From `.env` |
| `--api-key` | Humanitec API key | No* | From `.env` |
| `--api-url` | Humanitec API URL | No | `https://api.humanitec.com` |
| `--port-client-id` | Port client ID | No* | From `.env` |
| `--port-client-secret` | Port client secret | No* | From `.env` |

*Required if not provided via environment variables or GitHub Actions secrets*

## Support

- **Integration Documentation**: [Port Integration Guide](https://docs.getport.io/build-your-software-catalog/custom-integration/api/ci-cd/github-workflow/guides/humanitec/)
- **Development Guide**: [Ocean Integration Development](https://ocean.getport.io/develop-an-integration/)
- **Humanitec API Docs**: [Humanitec API Documentation](https://api-docs.humanitec.com/)

## License

This integration is provided as-is for use with Port and Humanitec platforms.
