# Vector DB and Data Management

> Klique's Hyper-Datasets for unstructured and vector data management, the built-in vector database, and on-demand Milvus and Qdrant deployments.

Klique manages unstructured and vector data through Hyper-Datasets, which are deployed with
a built-in vector database for vector search. Klique can also separately deploy standalone 
Milvus and Qdrant vector databases on demand.

## Hyper-Datasets

Klique's **Hyper-Datasets** are an abstraction of your data that
supports traceable, reproducible model development through parameterized data
access and metadata version control.

Hyper-Datasets are built for unstructured data such as text, audio, and visual
data. You can create, manage, and version datasets, and a dataset can inherit
from another so data lineages are formed and users can track when and how data
changes. In the Klique [WebApp](hyperdatasets_webapp/datasets.md), you can
view a dataset's version history and its contents, including annotations,
metadata, masks, and other information.

![Frame viewer](img/hyperdatasets/dataset_example_frame_editor.png#light-mode-only)
![Frame viewer](img/hyperdatasets/dataset_example_frame_editor_dark.png#dark-mode-only)

The premise of Hyper-Datasets is that a user-formed query is a full
representation of the dataset used by the AI process. Hyper-Datasets decouple
metadata from raw data files, so you can manipulate metadata through queries and
parameters that are tracked by the experiment manager. You can clone tasks using
different data manipulations, or [DataViews](https://clear.ml/docs/latest/docs/hyperdatasets/dataviews/), without
changing any hard-coded values, making these manipulations part of the task.

For more information, see [Hyper-Datasets](https://clear.ml/docs/latest/docs/hyperdatasets/overview/).

## Built-in vector database

Each Hyper-Dataset is deployed with a built-in vector database, and this is what
powers Hyper-Dataset vector search. The vector fields stored on frames are held
in the built-in database, and a query returns the frames most similar to a
reference vector. Similarity is computed against a chosen frame vector field,
and results respect the dataset's frame filters, so vector search combines with
metadata queries. For more information, see
[Vector Search](hyperdatasets_webapp/datasets_versioning.md#vector-search).

## Deploying vector databases

Klique also deploys standalone, detachable vector databases on demand as applications,
for RAG and other similarity-search workloads:

- **[Milvus](webapp/applications/apps_milvus.md)** is an open-source
  high-performance vector database.
- **[Qdrant](webapp/applications/apps_qdrant.md)** is an open-source vector
  database and similarity-search engine.

Each is launched as a database session served through a secure, authenticated
endpoint using the AI Application Gateway. Sessions persist their data, can be
cloned and restored from a previous session, and shut down automatically after a
configurable idle period.
