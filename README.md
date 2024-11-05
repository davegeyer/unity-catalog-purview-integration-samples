# **Bi-directional integration between Databricks Unity Catalog and Microsoft Purview**
### Discoverability and interoperability samples notebook

![image](https://cdn-images-1.medium.com/max/1000/1*3Kw_fP46Wu64rwOkCWVMcA.png)

---

This notebook accompanies the following blog post on Medium: [Bi-directional integration between Databricks Unity Catalog and Microsoft Purview](https://medium.com/p/79452911f2f5)

---

__Changes:__

* [November 05, 2024] Initial release

__Requirements:__

* DBR 13.3+
* Python 3.7+

__Resources:__

* [PyApacheAtlas](https://github.com/wjohnson/pyapacheatlas) | [Documentation](https://wjohnson.github.io/pyapacheatlas-docs/latest/)
* [Azure Purview Data Map SDK for Python](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-purview-catalog/1.0.0b4/index.html)

---

_Author:_ Dave Geyer | <dave.geyer@databricks.com>     
_Last Modified:_ November 05, 2024

---

Azure Databricks Unity Catalog and Microsoft Purview together offer a comprehensive framework for data cataloging and governance. Through programmatic interaction with these platforms, we can go beyond basic discoverability to perform tasks like bulk loading and creating custom lineage.

This notebook provides a guide on how to use Microsoft Purview with Azure Databricks Unity Catalog from within an Azure Databricks Notebook. This guide covers various aspects such as:
* Initial setup and authentication for Azure Databricks and Microsoft Purview.
* Utilizing PyApacheAtlas and the Microsoft Purview Data Map SDK for data asset exploration and searching.
* Synchronizing data classifications between Microsoft Purview and Azure Databricks Unity Catalog
* Enhancing Microsoft Purview with AI-generated table and column descriptions from Azure Databricks Unity Catalog.


From the [Microsoft Purview documentation](https://medium.com/r/?url=https%3A%2F%2Flearn.microsoft.com%2Fen-us%2Fpurview%2Ftutorial-custom-types), an asset is a metadata element that describes a digital or physical resource. Microsoft Purview's flexible type system, based on [Apache Atlas](https://medium.com/r/?url=https%3A%2F%2Fatlas.apache.org%2F2.0.0%2FTypeSystem.html), allows for an expansive definition of assets, including databases, files, business policies, and more. By recognizing the properties and inheritance of these types, users can customize and extend these definitions to suit emerging business needs.

All metadata objects (assets) managed by Microsoft Purview are modeled using type definitions. Understanding the Type System is fundamental to creating new custom types in Microsoft Purview.

Essentially, a Type can be seen as a Class from Object Oriented Programming (OOP):
* It defines the properties that represent that type.
* Each type is uniquely identified by its name.
* A type can inherit from a superType. This is an equivalent concept as inheritance from OOP. A type that extends a superType will inherit the attributes of the superType.

Apache Atlas has a few pre-defined system types that are commonly used as superTypes. For example:
* **Referenceable**: This type represents all entities that can be searched for using a unique attribute called qualifiedName.
* **Asset**: This type extends from Referenceable and has other attributes such as name, description, and owner.
* **DataSet**: This type extends Referenceable and Asset. Conceptually, it can be used to represent a type that stores data. Types that extend DataSet can be expected to have a Schema. For example, a SQL table.
* **Lineage**: Lineage information helps one understand the origin of data and the transformations it may have gone through before arriving in a file or table. Lineage is calculated through DataSet and Process: DataSets (input of process) impact some other DataSets (output of process) through Process.

![image](https://cdn-images-1.medium.com/max/1000/0*UHC5GX4ZRUQ5o5jT.png)

In this notebook, we will focus on the DataSet superType and will not cover asset relationships or how to define custom entity types, such as ML models or Databricks Volumes. I plan to cover that in a future post.
