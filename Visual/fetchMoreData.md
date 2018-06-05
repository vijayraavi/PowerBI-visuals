# Pulling data in segments - fetchMoreData

## Enable segmented fetch of large datasets

For dataview segment mode, define a "window" dataReductionAlgorithm in the visual's capabilities.json for the required dataViewMapping.
The "count" will determine the window size which limits the amount of new data rows appended to the dataview in each update. 

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

New segments are appended to the existing dataview and provided to the visual as an 'update' call.

## Usage in the custom visual

The indication of whether or not more data exists is by checking the existence of: 
		```
		dataView.metadata.segment
		```

It is also possible to check whether this is the first or subsequent update by checking:
	```
	options.operationKind
	```.
	```
	VisualDataChangeOperationKind.Create
	```
means the first segment, and 
	```
	VisualDataChangeOperationKind.Append
	```
means subsequent segments.

See the code snippet below for a sample implementation:
```typescript
// CV update implementation
public update(options: VisualUpdateOptions) {
	…

	// indicates this is the first segment of new data.
	if (options.operationKind == VisualDataChangeOperationKind.Create) {
	   …   
	} 

	// on second or subesquent segments:
	if (options.operationKind == VisualDataChangeOperationKind.Append) {
	   …
	}

	// complete update implementation
	…
}
```

fetchMoreData requests could also be invoked from a UI event handler
```typescript
btn_click(){
{
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
