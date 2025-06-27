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

*Required if not provided via environment variables

## Entity Mapping

### Applications (`humanitecApplication`)
- **Identifier**: Application ID
- **Title**: Application name (cleaned and title-cased)
- **Properties**: Creation timestamp
- **Relations**: None

### Environments (`humanitecEnvironment`)
- **Identifier**: `{app-id}/{env-id}`
- **Title**: Environment name
- **Properties**: Type, creation date, last deployment status/date/comment
- **Relations**: Links to parent application

### Workloads (`humanitecWorkload`)
- **Identifier**: `{app-id}/{env-id}/{workload-id}`
- **Title**: Workload name (cleaned and title-cased)
- **Properties**: Status, class, driver type, definition IDs, update timestamp
- **Relations**: Links to parent environment

### Resources (`humanitecResource`)
- **Identifier**: `{app-id}/{env-id}/{resource-id}`
- **Title**: Resource definition name (cleaned and title-cased)
- **Properties**: Type, class, resource data, status, driver type
- **Relations**: Links to associated workload (if applicable)

### Secret Stores (`humanitecSecretStore`)
- **Identifier**: Secret store ID
- **Title**: Secret store name (cleaned and title-cased)
- **Properties**: Type, creation/update timestamps, description
- **Relations**: None (organization-level)

### Shared Values (`humanitecSharedValue`)
- **Identifier**: `{app-id}/{key}` or `{app-id}/{env-id}/{key}`
- **Title**: Key name (cleaned and title-cased)
- **Properties**: Value, secret flag, creation/update timestamps
- **Relations**: Links to application and environment (if applicable)

### Value Set Versions (`humanitecValueSetVersion`)
- **Identifier**: `{app-id}/{version-id}`
- **Title**: Version ID
- **Properties**: Version number, creation timestamp, creator, comment
- **Relations**: Links to parent application

### Deployment Sets (`humanitecDeploymentSet`)
- **Identifier**: `{app-id}/{set-id}`
- **Title**: Set name or ID (cleaned and title-cased)
- **Properties**: Version, creation timestamp, creator, comment
- **Relations**: Links to parent application

### Pipelines (`humanitecPipeline`)
- **Identifier**: Pipeline ID
- **Title**: Pipeline name or ID (cleaned and title-cased)
- **Properties**: Type, creation/update timestamps, description
- **Relations**: None (organization-level)

### Deployment Deltas (`humanitecDeploymentDelta`)
- **Identifier**: `{app-id}/{delta-id}`
- **Title**: Delta name or ID (cleaned and title-cased)
- **Properties**: Status, creation timestamp, creator, comment, environment
- **Relations**: Links to parent application

## API Endpoints

The integration uses the following Humanitec API endpoints:

- `GET /orgs/{orgId}/apps` - List applications
- `GET /orgs/{orgId}/apps/{appId}/envs` - List environments
- `GET /orgs/{orgId}/apps/{appId}/envs/{envId}/resources` - List resources
- `GET /orgs/{orgId}/apps/{appId}/envs/{envId}/resources/graphs/{graphId}` - Get dependency graph
- `GET /orgs/{orgId}/secretstores` - List secret stores
- `GET /orgs/{orgId}/apps/{appId}/envs/{envId}/values` - List environment shared values
- `GET /orgs/{orgId}/apps/{appId}/values` - List application shared values
- `GET /orgs/{orgId}/apps/{appId}/value-set-versions` - List value set versions
- `GET /orgs/{orgId}/apps/{appId}/sets` - List deployment sets
- `GET /orgs/{orgId}/pipelines` - List pipelines
- `GET /orgs/{orgId}/apps/{appId}/deltas` - List deployment deltas

## Error Handling

The integration includes comprehensive error handling:

- **API Errors**: HTTP errors are logged with response details
- **Missing Data**: Graceful handling of missing or empty responses
- **Network Issues**: Timeout and connection error handling
- **Validation**: Input validation for required parameters

## Logging

The integration uses structured logging with the following levels:

- **INFO**: General operation information
- **DEBUG**: Detailed API request/response information
- **ERROR**: Error conditions and failures

Logs include:
- Entity counts and sync progress
- API endpoint requests
- Error details with context
- Cache hit/miss information

## Caching

The integration implements in-memory caching for:
- Applications
- Environments (per application)
- Resources (per environment)

This reduces API calls and improves performance for large organizations.

## Development

### Project Structure
```
humanitec-integration-script/
├── integration/
│   ├── main.py                 # Main integration logic
│   └── clients/
│       ├── humanitec_client.py # Humanitec API client
│       ├── port_client.py      # Port API client
│       └── cache.py           # Caching implementation
├── resources/
│   └── blueprints.json        # Port blueprint definitions
├── requirements.txt           # Python dependencies
└── README.md                 # This file
```

### Adding New Entities

To add new entity types:

1. Add API method to `HumanitecClient`
2. Add sync method to `HumanitecExporter`
3. Add blueprint definition to `blueprints.json`
4. Update `sync_all()` method
5. Add blueprint constant to `BLUEPRINT` class

### Testing

Run syntax checks:
```bash
python -m py_compile integration/main.py integration/clients/humanitec_client.py
python -m json.tool resources/blueprints.json
```

## Troubleshooting

### Common Issues

1. **Authentication Errors**
   - Verify API key is valid and has appropriate permissions
   - Check organization ID is correct

2. **Missing Entities**
   - Ensure the integration has access to all applications/environments
   - Check API permissions for the specific endpoints

3. **Rate Limiting**
   - The integration includes built-in delays and caching
   - Monitor API usage in Humanitec dashboard

4. **Port Connection Issues**
   - Verify Port client credentials
   - Check network connectivity to Port API

### Debug Mode

Enable debug logging by setting the log level:
```python
import logging
logging.getLogger().setLevel(logging.DEBUG)
```

## Support

- **Integration Documentation**: [Port Integration Guide](https://docs.getport.io/build-your-software-catalog/custom-integration/api/ci-cd/github-workflow/guides/humanitec/)
- **Development Guide**: [Ocean Integration Development](https://ocean.getport.io/develop-an-integration/)
- **Humanitec API Docs**: [Humanitec API Documentation](https://api-docs.humanitec.com/)

## License

This integration is provided as-is for use with Port and Humanitec platforms.
