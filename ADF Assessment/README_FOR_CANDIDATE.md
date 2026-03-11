Assessment: Data Factory transformation task

Goal

Your task is to implement the transformations required to convert the raw HR survey file into a cleansed dataset. The Data Factory artifact (linked service, datasets, dataflow, pipeline) is already present in the deployed factory. Do not modify linked services—only edit or create pipelines/data flows/datasets as prescribed.

Input dataset

- Dataset name: `employeesurvey`
- Location: ADLS Gen2 `rawsource` container
- File: `HR Employee Survey Responses.xlsx`
- Sheet: `HR Survey Reponses`

Output dataset

- Dataset name: `CleansedDestination`
- Location: ADLS Gen2 `cleansed` container
- Output format: DelimitedText (CSV)

Required transformations (step-by-step)

1) Role roll-up
- There are several boolean columns (Director, Manager, Supervisor, Staff) that indicate role membership. Create a new single column called `Role` with the following logic (choose the first matching true value in priority order):
  - If `Director` is true then `Role` = 'Director'
  - Else if `Manager` is true then `Role` = 'Manager'
  - Else if `Supervisor` is true then `Role` = 'Supervisor'
  - Else if `Staff` is true then `Role` = 'Staff'
  - Else `Role` = 'Staff'

2) Drop old role columns
- Remove the original role indicator columns: `Director`, `Manager`, `Supervisor`, `Staff`.


3) Filter out Communications Office
- Remove any rows where `Department` is 'Communications Office' (case-sensitive as in the source). 

4) Output to cleansed
- Write the transformed data to the `CleansedDestination` dataset in DelimitedText format.
- Ensure headers are written and the delimiter is comma.

Validation tests (what the interviewer will check)

- The `employeesurvey` dataset should be used as the source and `CleansedDestination` as the sink.
- The pipeline or data flow should produce rows where there is a single `Role` column and the old role columns no longer exist.
- If no role is assigned in old role column, New Role should be 'staff'
- There should be no rows with Department == 'Communications Office'.
- The pipeline should complete successfully (look at activity run history).
  

How to run and test

- Open the Data Factory in the Portal -> Author & Monitor.
- Open the Mapping Data Flow `df_raw_to_cleansed` (or create one per instructions) and ensure the transformations above are present.
- Publish changes.
- Run the pipeline `PL_raw_to_cleansed` (or create a test pipeline that executes the dataflow) and wait for completion.
- Check output in the `cleansed` container (download a sample CSV) to verify Role column, no old role columns, and no Communications Office rows.

Deliverable

- A working pipeline (or dataflow + wrapper pipeline) in the deployed Data Factory that implements the above.
- A short readme or comment in the pipeline/dataflow that explains the main transformation steps.

Good luck — if the mapping language is unfamiliar, use the Transformations GUI in Mapping Data Flows to build the required transformations.
