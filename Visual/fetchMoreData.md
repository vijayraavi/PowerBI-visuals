# Pulling data in segments - fetchMoreData

## Enable segmented fetch of long data sets

For dataview segment mode, define "window" dataReductionAlgorithm on capabilities.json, for the required dataViewMapping.
The "count" will set the window size, that limits the amount of new data rows aggreated to the dataview on each update.

```json
  "dataViewMappings": [
        {
            "table": {
                "rows": {
                    "for": {
                        "in": "values"
                    },
                    "dataReductionAlgorithm": {
                        "window": {
                            "count": 100
                        }
                    }
                }
            }
        }
    ]
```

## Usage on custom visual

```typescript
// CV update implementation
public update(options: VisualUpdateOptions) {
	…

	// special handling of the 1st segment of new data.
	if (options.operationKind == VisualDataChangeOperationKind.Create) {
	   …   
	} 

	// on 2nd and later segments:
	if (options.operationKind == VisualDataChangeOperationKind.Append) {
	   …
	}

	// complete update implementation
	…

	// fetchMoreData request could also be invoked from UI event handler
	// check if more data is expected for the current dataview
	if (dataView.metadata.segment) {
		//request for more data if available
		let request_accepted: bool = this.host.fetchMoreData();
		// handle rejection
		if (!request_accepted) {
				…
		}
	}

}
```
