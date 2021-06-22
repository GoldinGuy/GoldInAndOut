# GoldInAndOut

MPFI EM Core Pipeline & Gold Cluster Analysis For Freeze Fracture

### Adding additional workflows
1) In the `typings.py` file, add your workflow to the `Workflow` Enum. The enum should appear as the following:
```python
class Workflow(Enum):
    NND = 1
    CLUST = 2
    YOUR_WORKFLOW = 3 (LAST NUMBER + 1) 
```
2) Add a python file to the `worflows/` directory containing the workflow or macro you'd like to add to the GUI. Ideally, this is in the format `<WorkflowName>.py`.
3) In the `globals.py` file, add your workflow to the `WORKFLOW_METADATA` dictionary. This is in the following format:
```buildoutcfg
    @name: short abreviation of workflow, no spaces
    @type: ENUM type of Workflow
    @header: string displayed as "header"
    @desc: string displayed as "description" below header
    @hist: histogram metadata:
        @title: title of histogram
        @x_label: x_label of histogram
        @y_label: y_label of histogram
    @props: array of optional parameters in the following format:
        @title: title of prop
        @placeholder: placeholder for prop label
```
Below is a sample workflow using JSON structure.
```json
{
    "name": "CLUST",
    "type": Workflow.CLUST,
    "header": "Ward Hierarchical Clustering",
    "desc": "Cluster gold particles into groups. Optionally generate random coordinates.",
    "hist": {
            "title": "Ward Hierarchical Clusters",
            "x_label": "Cluster Value",
            "y_label": "Number of Entries",
            "x_type": "cluster"
        },
    "props": [
            {
            "title": "distance_threshold",
              "placeholder": "120"
            },
            {"title": "n_clusters",
             "placeholder": "None"
             },
            {  "title": "linkage",
              "placeholder": "ward"
            }
        ]
}
```
4) Finally, in the `workflow.py` file, in the section labeled `ADD NEW WORKFLOWS HERE`, add an elif statement for your workflow that reads like the following:
```python
 if workflow["type"] == Workflow.NND:
    self.real_df, self.rand_df = run_nnd(df=scaled_df, prog=prog_wrapper, random_coordinate_list=random_coords)
elif workflow["type"] == YOUR_WORKFLOW_ENUM:
    self.real_df, self.rand_df = your_workflow_function_from_custom_file(df=scaled_df, prog=prog_wrapper, random_coordinate_list=random_coords)
```
If your workflow requires custom parameters/props, use custom values. This will appear in the following format:
```python
 if workflow["type"] == Workflow.NND:
    self.real_df, self.rand_df = run_nnd(df=scaled_df, prog=prog_wrapper, random_coordinate_list=random_coords)
elif workflow["type"] == YOUR_WORKFLOW_ENUM:
    vals = [self.cstm_props[i].text() if self.cstm_props[i].text() else workflow['props'][i]['placeholder'] for i in range(len(self.cstm_props))]
    self.real_df, self.rand_df = your_workflow_function_from_custom_file(df=scaled_df, random_coordinate_list=random_coords, prog=prog_wrapper, distance_threshold=vals[0], n_clusters=vals[1], linkage=vals[2])
```