# Hyper-Datasets Page

> The Hyper-Datasets page for browsing, filtering, and managing hyper-datasets and creating new ones.

Use the Hyper-Datasets Page to navigate between and manage hyper-datasets. 

You can view the Hyper-Datasets page in Project view  
or in List view . In List 
view, all hyper-datasets are shown side-by-side. In Project view, hyper-datasets are organized according to their projects, and 
top-level projects are displayed. Click on a project card to view the project's hyper-datasets.

Click on a Hyper-Dataset card to open the dataset's [version list](datasets_versioning.md), where you can view 
and manage the dataset versions' lineage and contents. 

Filter page contents by specific fields 
or through free form search :
* My Work - Show only hyper-datasets that you created
* Tags - Choose which tags to filter by from a list of tags used in the hyper-datasets.
  * Filter by multiple tag values using the **ANY** or **ALL** options, which correspond to the logical "AND" and "OR" 
  respectively. These options appear on the top of the tag list.
  * Filter by the absence of a tag (logical "NOT") by clicking its checkbox twice. An X will appear in the tag's checkbox.
* User - Filter hyper-datasets by the user who created them.

Free form search queries hyper-dataset name, ID, tags, and project. To search using regex, click the `.*` 
icon on the search bar.

![Hyper-Dataset page](../img/hyperdatasets/datasets_01.png#light-mode-only)
![Hyper-Dataset page](../img/hyperdatasets/datasets_01_dark.png#dark-mode-only)

## Project Cards

In Project view, project cards display a project's summarized hyper-dataset information:

![Hyper-Dataset project card](../img/hyperdatasets/hyperdataset_project_card.png#light-mode-only)
![Hyper-Dataset project card](../img/hyperdatasets/hyperdataset_project_card_dark.png#dark-mode-only)

* Project name
* Number of hyper-datasets in project
* Tags used by hyper-datasets in project

Click on a project card to view its hyper-datasets.

## Hyper-Dataset Cards

In List view, the Hyper-Dataset cards display summarized dataset information: 

![Hyper-Dataset card](../img/hyperdatasets/hyperdataset_card.png#light-mode-only)
![Hyper-Dataset card](../img/hyperdatasets/hyperdataset_card_dark.png#dark-mode-only)

* Dataset name
* Time since last update. Hover over elapsed time to view date of last update
* User updating the Dataset
* If the dataset contains dataset-level metadata, the card displays the  
    `Metadata` indicator, which opens the Metadata editor on click 
* The number of versions in the Dataset
* The total number of frames in all versions of the Dataset. If an asterisk (\*) appears next to **FRAMES**, then you can hover over it and see the name of the version whose frames were last updated
* The percentage of frames annotated in all versions of the Dataset. If an asterisk (\*) appears next to **ANNOTATED**, then you can hover over it and see the name of the version whose frames were last annotated
* If the Dataset version's status is *Published*, then the Dataset's top labels appear (colors are editable). If the 
  Dataset version is *Draft*, then no labels appear
* Tags

:::note[Change Label Color]
To change the label color coding, hover over a label color, click the hand pointer, and then select a new color.
:::

### Hyper-Dataset Actions

Click  on the top right
of a dataset card to open its context menu and access dataset actions:  

![Hyper-Dataset context menu](../img/hyperdatasets/webapp_hyperdataset_card_context_menu.png#light-mode-only)
![Hyper-Dataset context menu](../img/hyperdatasets/webapp_hyperdataset_card_context_menu_dark.png#dark-mode-only)

* **Copy Dataset ID**
* **View Dataset Schema** - View the dataset's frame document schema. This shows the frames’ fields and their types.
* **Rename** - Change the dataset's name
* **Add Tag** - Add label to the dataset to help easily classify groups of datasets.
* **Edit Metadata** - Modify dataset-level metadata. This will open the metadata edit window, where you can edit the section
* **Delete** - Delete the dataset and all its versions.  
  
  :::warning
  You cannot undo the deletion of a Hyper-Dataset.
  :::
* **Move to Project** - Move the dataset to another project.

## Create New Hyper-Datasets

To create a Hyper-Dataset, click the **+ NEW DATASET** button in the top right of the page, which will open a 
**New Dataset** modal.

![Hyper-Dataset creation modal](../img/hyperdatasets/webapp_hyperdataset_creation.png#light-mode-only)
![Hyper-Dataset creation modal](../img/hyperdatasets/webapp_hyperdataset_creation_dark.png#dark-mode-only)

This creates a new Hyper-Dataset that contains a single, empty draft version.
