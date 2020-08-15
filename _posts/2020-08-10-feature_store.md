---
layout: post
title: Feature Store for faster Feature Engineering
featured-img: warehouse
image: warehouse
category: [feature_engineering]
mathjax: true
summary: A feature store helps to increase feature engineering iteration speed
---

# Faster Feature Engineering: Feature Store

The process of Feature Engineering can be very repetitive and inefficient when being done manually and in an unstructured way.
In particular, ordinary Feature Engineering has the downsides that:
- the engineering process itself is not formalised, which leads to quick and dirty code writing and try and error approaches
- it is hard to remember which computation methods were already tried which might lead to unneeded calculation repetitions
- calculated single features are not preserved

In order to overcome these inefficiencies and speed up my feature engineering process, I came up with the following solution:

**Structuring of the engineering process**\
Each feature engineering method is part of an object that is initialised with required calculation parameters and that has the ability to create unique feature identifiers

**Usage of a feature store and a feature registry**\
Each calculated feature is registered in the feature registry with its identifier and the feature data stored efficiently to disk. Using its identifier it can easily be reloaded.



# Structuring of the engineering process
The main idea here is to embed the actual feature calculation method in a python class. Each object instantiated from this class is initialised with the parameters used for the actual feature calculation, together with a raw data identifier. The latter is obtained from the statistical properties of the source feature used for the engineering task.
The method's parameters together with the raw data identifier form the engineered feature identifier via the `create_identifier()` method. With this identifier the feature registry can determine whether an identical feature was already previously calculated and can be loaded from disk.

Each of these engineering processes inherits from the *FeatureProcess* class that acts like an interface each process needs to implement.


```python
class FeatureProcess:

    @abstractmethod
    def execute(self, data: Union[pd.Series, pd.DataFrame]) -> pd.Series:
        pass

    @abstractmethod
    def create_identifier(self) -> str:
        pass

```

With this interface we can define a feature process, e.g. one that calculates rolling window averages.

```python
class RollingAverageProcess(FeatureProcess):

    def __init__(self,
                 source_feature_name: str,
                 window: int,
                 data_identifier: str,
                 ):

        self.source_feature_name = source_feature_name
        self.window = window
        self.name = f'rolling_avg_w{window}_{source_feature_name}'
        self.data_identifier = data_identifier
        self.identifier_string = self.create_identifier()

    def execute(self, data):

        data.loc[:, self.name] = data[self.source_feature_name].rolling(self.window).mean()

        return data

    def create_identifier(self):

        identifier = {
            'label': self.name,
            'inputs': [
                {
                    'source_feature_name': self.source_feature_name,
                    'data_identifier': self.data_identifier,
                },
            ],
            'parameters': [
                {
                    'param_name': 'window',
                    'param_value': self.window
                }
            ],
        }

        identifier_string = json.dumps(identifier, sort_keys=True)

        return identifier_string


```

The process initialised with:
- source_feature_name: The raw feature on which the window is applied
- window: The window length
- data_identifier: A string identifying the source feature

Each process gets assigned a name that acts as the feature's column name in a pandas dataframe.


# Feature Store

The feature store is able to both remember and load previously calculated features.
For this purpose it uses a feature registry.



```python
class FeatureStore:

    ...
    ...

    def add_feature(self, identifier_string: str, feature: pd.DataFrame):
        # Add new entry to registry with mapping of identifier_string -> filepath

        unique_filename = f"{str(uuid.uuid1())}.parquet"
        feature_filepath = os.path.join(self.feature_folderpath, unique_filename)

        identifier_string_hash = self.identifier_to_hash(identifier_string)

        self.feature_registry.update(
            {
                identifier_string_hash: {
                    'filename': unique_filename,
                    'creation_datetime': pd.Timestamp.now()
                }
            }
        )

        # Store the FeatureStore object to disk
        self.save_to_disk()

        # Store the new feature to filesystem and add filepath to registry
        feature.to_parquet(feature_filepath)

    def contains_identifier_string(self, identifier_string: str) -> bool:
              # Check if registry contains the provided identifier_string

              identifier_string_hash = self.identifier_to_hash(identifier_string)

              return identifier_string_hash in self.feature_registry.keys()


    def load_feature(self, identifier_string: str) -> pd.DataFrame:

        feature_filepath = self.get_filepath(identifier_string=identifier_string)

        feature = pd.read_parquet(feature_filepath)

        return feature

```

The above methods receive feature identifier strings, which are the identifiers introduced in the feature process structuring.

When adding a feature to the store, a unique filename is created and an entry added to the registry that maps the identifier to the filename.
Then the feature store object itself is stored to the filesystem in order to make it usable after the current python process finished, and finally the feature's data is stored in the efficient parquet format.

When in a later process the store is loaded from disk the feature process identifiers can be looked up via `contains_identifier_string()` and in case the idenetifier exists in the registry,
the feature can be directly loaded via `load_feature()` without the need of recalculating it.



# M5 challenge feature engineering example
To demonstrate the usefulness of this approach, I use a rather large dataset from the Kaggle m5 Walmarkt challenge with several Gigabytes of memory consumption, so the Feature Engineering is expected to be time consuming.
A few feature processes are defined and two runthroughs executed, one in which each feature is calculated, and one in which each feature is loaded from disk via the feature store.

The whole code to run this example can be found on my github: <link>

Here an exemplary time comparison:

![alt text](./time_comparison.png "Time comparison between feature calculation and loading via the feature store")

![](assets/img/feature_store/time_comparison.png")

Usage of the feature store accelerates the runtime significantly, which is not surprising since the features are loaded and not recomputed. Usage of such a simple feature store is a very helpful tool when doing extensive Feature Engineering, especially when done on a large dataset and over a longer time period.
