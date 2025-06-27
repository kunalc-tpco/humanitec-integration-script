# New Features Added to Humanitec Integration

This document describes the new features that have been added to the Humanitec integration script to sync additional entities from Humanitec to Port.

## New Entity Types

The following new entity types have been added to the integration:

### 1. Secret Stores (`humanitecSecretStore`)
- **Description**: Secret stores configured in the Humanitec organization
- **Properties**:
  - `type`: The type of the secret store
  - `createdAt`: Creation timestamp
  - `updatedAt`: Last update timestamp
  - `description`: Description of the secret store
- **Relations**: None (organization-level entity)

### 2. Shared Values (`humanitecSharedValue`)
- **Description**: Shared values (secrets and configuration) at both application and environment levels
- **Properties**:
  - `value`: The actual value (may be masked for secrets)
  - `isSecret`: Boolean indicating if the value is a secret
  - `createdAt`: Creation timestamp
  - `updatedAt`: Last update timestamp
- **Relations**:
  - `humanitecApplication`: Links to the parent application
  - `humanitecEnvironment`: Links to the environment (for environment-level values)

### 3. Value Set Versions (`humanitecValueSetVersion`)
- **Description**: Versioned sets of shared values for applications
- **Properties**:
  - `version`: Version number
  - `createdAt`: Creation timestamp
  - `createdBy`: User who created the version
  - `comment`: Comment for the version
- **Relations**:
  - `humanitecApplication`: Links to the parent application

### 4. Deployment Sets (`humanitecDeploymentSet`)
- **Description**: Deployment sets that define the configuration for deployments
- **Properties**:
  - `version`: Version of the deployment set
  - `createdAt`: Creation timestamp
  - `createdBy`: User who created the deployment set
  - `comment`: Comment for the deployment set
- **Relations**:
  - `humanitecApplication`: Links to the parent application

### 5. Pipelines (`humanitecPipeline`)
- **Description**: CI/CD pipelines configured in the organization
- **Properties**:
  - `type`: Type of the pipeline
  - `createdAt`: Creation timestamp
  - `updatedAt`: Last update timestamp
  - `description`: Description of the pipeline
- **Relations**: None (organization-level entity)

### 6. Deployment Deltas (`humanitecDeploymentDelta`)
- **Description**: Changes/deltas that can be applied to deployments
- **Properties**:
  - `status`: Status of the deployment delta
  - `createdAt`: Creation timestamp
  - `createdBy`: User who created the delta
  - `comment`: Comment for the delta
  - `environment`: Target environment for the delta
- **Relations**:
  - `humanitecApplication`: Links to the parent application

## New API Methods

The following new methods have been added to the `HumanitecClient` class:

- `get_secret_stores()`: Retrieves all secret stores for the organization
- `get_shared_values(app, env)`: Retrieves shared values for a specific environment
- `get_shared_values_app_level(app)`: Retrieves shared values at the application level
- `get_value_set_versions(app)`: Retrieves value set versions for an application
- `get_deployment_sets(app)`: Retrieves deployment sets for an application
- `get_pipelines()`: Retrieves all pipelines in the organization
- `get_deployment_deltas(app)`: Retrieves deployment deltas for an application

## New Sync Methods

The following new sync methods have been added to the `HumanitecExporter` class:

- `sync_secret_stores()`: Syncs secret store entities
- `sync_shared_values()`: Syncs shared value entities (both app and environment level)
- `sync_value_set_versions()`: Syncs value set version entities
- `sync_deployment_sets()`: Syncs deployment set entities
- `sync_pipelines()`: Syncs pipeline entities
- `sync_deployment_deltas()`: Syncs deployment delta entities

## Updated Sync Order

The `sync_all()` method now includes all new sync methods in the following order:

1. Applications
2. Environments
3. Workloads
4. Resource Graphs
5. Resources
6. Secret Stores
7. Shared Values
8. Value Set Versions
9. Deployment Sets
10. Pipelines
11. Deployment Deltas

## Blueprint Definitions

New blueprint definitions have been added to `resources/blueprints.json` for all the new entity types. These blueprints include:

- Appropriate icons for each entity type
- Schema definitions with relevant properties
- Relationship definitions where applicable
- Proper data types and formats for timestamps

## Usage

The integration script will now automatically sync all the new entity types when run. No additional configuration is required beyond the existing setup.

## API Endpoints Used

The new features utilize the following Humanitec API endpoints:

- Secret stores: `GET /orgs/{orgId}/secretstores`
- Shared values (env): `GET /orgs/{orgId}/apps/{appId}/envs/{envId}/values`
- Shared values (app): `GET /orgs/{orgId}/apps/{appId}/values`
- Value set versions: `GET /orgs/{orgId}/apps/{appId}/value-set-versions`
- Deployment sets: `GET /orgs/{orgId}/apps/{appId}/sets`
- Pipelines: `GET /orgs/{orgId}/pipelines`
- Deployment deltas: `GET /orgs/{orgId}/apps/{appId}/deltas`

## Testing

A test script (`test_integration.py`) has been created to verify that all new methods and blueprints are properly implemented and accessible. 