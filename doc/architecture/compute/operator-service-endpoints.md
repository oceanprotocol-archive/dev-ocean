# Compute Service Endpoints

## Status
  
  
### GET /api/v1/operator/status
   
   
Get all workflows and corresponding stats

Parameters
```
        owner: String object containg user ETH address (optional)
        agreementID: String object containing agreementID (optional)
        workflowID: String object containing workflowID (optional)
```

Returns

An Array of objects, each object describing a workflow. If the array is empty, then the search yeld no results

Each object will contain:
```
        owner:The owner of this compute job
        agreementID:
        workflowID:
        createdat:Date/Time of job creation
        finishedat:Date/Time when job finished
        status:  Int, see below for list
        configlogURL: URL to get the configuration log (for admins only)
        publishlogURL: URL to get the publish log (for admins only)
        algologURL: URL to get the algo log (for user)
        outputsURL: Array of URLs for algo outputs
```

Status description:
| status   | Description|
|----------|------------|
|  1       | Job started        |
|  2       | Configuring volumes|
|  3       | Running algorith|
|  4       | Uploading results |
|  5       | Job finished |

