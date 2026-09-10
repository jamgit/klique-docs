# Datasets Page

> The Datasets page for browsing, filtering, and managing datasets in project and list views.

:::tip[SDK version compatibility]
The datasets page shows datasets created with `clearml` v1.6 or newer.  
Datasets created with earlier versions of `clearml` are available in their original project.  
:::

Use the **Datasets** Page to navigate between and manage datasets. The page shows summaries 
for all datasets created using [ClearML Data](https://clear.ml/docs/latest/docs/clearml_data/).

You can view the datasets page in Project view  
or in List view . In List 
view, all datasets are shown side-by-side. In Project view, datasets are organized according to their projects, and 
top-level projects are displayed. Click on a project card to view the project's datasets.

Click on a dataset card to navigate to its [Version List](webapp_dataset_viewing.md), where you can view the 
dataset versions' lineage and contents. 

Filter page contents by specific fields 
or through free form search :
* My Work - Show only datasets that you created
* Tags - Choose which tags to filter by from a list of tags used in the datasets.
  * Filter by multiple tag values using the **ANY** or **ALL** options, which correspond to the logical "AND" and "OR" 
  respectively. These options appear on the top of the tag list.
  * Filter by the absence of a tag (logical "NOT") by clicking its checkbox twice. An X will appear in the tag's checkbox.
* User - Filter datasets by the user who created them.

Free form search queries dataset name, ID, and description. To search using regex, click the `.*` 
icon on the search bar.

![Dataset page](../../img/webapp_dataset_page.png#light-mode-only)
![Dataset page](../../img/webapp_dataset_page_dark.png#dark-mode-only)

## Project Cards

In Project view, project cards display a project's summarized dataset information:

![Project card](../../img/webapp_dataset_project_card.png#light-mode-only)
![Project card](../../img/webapp_dataset_project_card_dark.png#dark-mode-only)

* Project name
* Number of datasets in project
* Tags used by datasets in project

Click on a project card to view its datasets.

## Dataset Cards

In List view, the dataset cards display summarized dataset information:

![Dataset card](../../img/webapp_dataset_card.png#light-mode-only)
![Dataset card](../../img/webapp_dataset_card_dark.png#dark-mode-only)

* Dataset name
* Time since last update
* Number of versions
* Details about latest version
  * Number of files
  * Size
* Tags 

### Dataset Actions

Click  on the top right
of a dataset card to open its context menu and access dataset actions.  

![Dataset context menu](../../img/webapp_dataset_card_context_menu.png#light-mode-only)
![Dataset context menu](../../img/webapp_dataset_card_context_menu_dark.png#dark-mode-only)

* **Rename** - Change the dataset's name
* **Add Tag** - Add label to the dataset to help easily classify groups of dataset.
* **Delete** - Delete the dataset and all of its versions. To delete a dataset, all its versions must first be 
  archived.
