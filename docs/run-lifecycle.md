#Lifecycle

Marquez captures the runs of a job, and changes as they happen.

Run states:
```
NEW -(start)-> RUNNING -(complete)-> COMPLETED
                       -(abort)-> ABORTED
                       -(fail)-> FAILED
```

## Static inspection of job lineage
When the job lineage can be inspected from it's static definition the metadata is captured as follows:

### When the run of the job starts
 - PUT /namespaces/{name} (for every namespace referred to bellow)
 Before referring to a namespace we must ensure its metadata has been updated
 - PUT /sources/{name} (for every source referred to by a dataset bellow)
 Before referring to a datasource we must ensure its metadata has been updated
 - PUT /namespaces/{namespace}/datasets/{name} (for every dataset reffered to as an input or ouput of the job bellow)
  Before referring to an input or output dataset we must ensure it's metadata has been updated. This is the dataset as it is inspected before the job starts.
 - PUT /namespaces/{namespace}/jobs/{name}
 Now that we have updated all the dependencies of the job we can update the job itself
 - POST /namespaces/{namespace}/jobs/{name}/runs
 We create a run for this job
 - POST /namespaces/{namespace}/jobs/{name}/runs/{id}/start
 We start the run

### When the run of the job ends
 - PUT /namespaces/{namespace}/datasets/{name} (for every dataset the job wrote to)
  We update the definition of each dataset this job wrote to. For example the schema might have changed
- POST /namespaces/{namespace}/jobs/{name}/runs/{id}/complete
 We mark the run as succeful (similarly 'fail' for a failed job)

## Dynamic inspection of job lineage
If the job can not be introspected statically, we will have to capture the information as the job is running. Then all the updates will be sent when the job is actually completing. (both steps above are executed when the job finishes)