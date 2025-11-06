# Data Factory
A Data Factory is a Microsoft Azure Service that allows us to connect to multiple Azure services through `Linked Services`. Ultimately what we want to achieve with these `linked services` is to be able to extract data from multiple sources/files, perform some changes/transformation on that data then load it up another service/create a new file on a destination.

1. We find the Data Factory service on `Analytics > Data factories`
2. Create one and select the same resource group we have used with previous services.
3. Give it a name: `dwcafactory25`
4. Review + create
5. Once done, launch up `Data Factory Studio`
6. We will see a list of options on the left navbar, the next step is to create a linked service.

# Linked Services
A linked service is a service from within Azure Data Factory/Synapse, it allows us to link up against multiple sources of data, it can be a storage account, database or some external source.

## Storage Acount
1. From within our `Data Factory Studio`, we want to navigate to `Manage`
2. From `Manage`, select `Linked services` and select `Azure Data Lake Storage Gen2`
3. Authentication type is `Account key`
4. Choose the subscription type `Azure students` 
5. Select the storage account we created earlier
6. Test the connection to see if it is successful.

## Azure SQL Database
1. Create a linked service for `Azure SQL Database`
2. Fill out the connection details
3. Choose the table we created earlier

# Pipeline
A pipeline is series of tasks than can be triggered to perform movement or transformation on data on a scheduled basis.

1. From within our `Data Factory Studio`, we want to navigate to `Author`
2. Create a new pipeline
3. From the activies list, choose `Move and transform`, select `Data flow` and drag it into the canvas.
4. With the Data flow select, change the tab to `Settings`, and select a new `Data Flow`.
5. Here we are going to choose our source.
    - Click on the arrow pointing down and `add source`
    - Scroll down to Dataset and click `new`
    - Here we are going to select `Azure Data Lake Storage Gen2` and `DelimitedText`
    - Now we need to select our `Linked Service` 
    - Then select one of the files, we are going to start with `Flipkart_Mobiles.csv`
        - Then create a new Dataset for `Flipkart_Mobiles_Brands.csv`

# Data flow
A Data flow is a series of tasks that perform a specific set of activities.

For this project our data flow is going to perform 3 operations

### Extract data from Mobiles
- We want to extract the data from the mobiles csv raw as it is.
- We want to make some slight adjusts to the data types, so that the `join` operation we are doing later doesnt fail
- On `Projection`, change `Brand` to an integer, because these are going to be `id`'s of `integer` type.
- Optionally, we can change other stuff as well if we want, but for now these steps are "just enough" for our data to be combined.

### Extract data from Brands
- We want to extract the data from the brands csv raw as it is.
- We want to make some slight adjusts to the data types, so that the `join` operation we are doing later doesnt fail
- On `Projection`, change `Id` to an integer, because these are going to be `id`'s of `integer` type.
- Now that our Mobiles dataset `Brand` column is an integer and Brands dataset `Id` column is an integer, we should be able to do a join operation.

### Join the data
- We create new task to `Join` the data
- On the `Left` table we choose Mobiles and on the `Right` table we choose Brands.
- The join type will be `Left Outer`.
- The condition will be `Left = Brand` and `Right = Id`
- Remember we changed both of those to `integer`? It matters now, that the type matchs, because we cant join strings.

### Select the data
- The final step, after the join is done is to select what data to `Project` when loading the data to the Data warehouse.
- We add a `Select` task, that takes the data from the `Join`.
- Here we choose what to keep and what to exclude
- Ultimately we dont care about `Brands@Id`, so we can remove this and replace it with `Brands@Brands`
- We can keep the name for this column as Brand
- Finally we can inspect the output to see if we are happy with it.

### Sink
- The last step is to load the data
- The incoming stream will be the `Select` node.
- Then we need to select where to load this data, so on dataset we select our Linked Service that is the `Azure SQL Database`
- We dont need to bother with the rest of the configurations.