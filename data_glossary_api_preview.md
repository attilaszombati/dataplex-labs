
# Create a business glossary entry

```shell

curl -H "Content-Type: application/json" \
     -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
     -X POST "https://datacatalog.googleapis.com/v2/projects/vodafone-data-gov-poc/locations/europe-west1/entryGroups/dc_glossary_test/entries?entry_id=vf_hu_business_glossary" \
     --data '{
       "entry_type": "glossary",
       "core_aspects": {
         "business_context": {
           "aspect_type": "business_context",
           "json_content": {
             "description": "Glossary for Inventory",
             "contacts": ["mary.sue@company.com", "john.doe@company.com"]
           }
         }
       }
     }'
```

# Create a business term entry with relationship to the glossary entry

```shell

curl -H "Content-Type: application/json" \
     -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
    -X POST \
"https://datacatalog.googleapis.com/v2/projects/vodafone-data-gov-poc/locations/europe-west1/entryGroups/dc_glossary_test/entries?entry_id=inventory_item_3" \
--data '{
  "entry_type": "glossary_term",
  "core_aspects": {
    "business_context": {
      "aspect_type": "business_context",
      "json_content": {
        "description": "Glossary term for Inventory"
      }
    }
  },
  "core_relationships": {
    "relationship_type": "is_child_of",
    "destination_entry_name": "projects/vodafone-data-gov-poc/locations/europe-west1/entryGroups/dc_glossary_test/entries/inventory_item_2"
  }
}'
```


# Get all entry groups in the project

```shell
curl -H "Content-Type: application/json" \
     -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
     -H "X-Goog-User-Project: vodafone-data-gov-poc" \
     -X GET \
  "https://datacatalog.googleapis.com/v2/projects/vodafone-data-gov-poc/locations/europe-west1/entryGroups"
```

# Get the specific entry information

```shell
curl -H "Content-Type: application/json" \
     -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
     -H "X-Goog-User-Project: vodafone-data-gov-poc" \
  -X GET \
  "https://datacatalog.googleapis.com/v2/projects/vodafone-data-gov-poc/locations/europe-west1/entryGroups/dc_glossary_test/entries/vf_hu_business_glossary"
```

# Delete the entry from the entry group

```shell
curl -H "Content-Type: application/json" \
     -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
     -X DELETE \
  "https://datacatalog.googleapis.com/v2/projects/vodafone-data-gov-poc/locations/europe-west1/entryGroups/dc_glossary_test/entries/glossary"
```

# Update the entry

```shell
curl -H "Content-Type: application/json" \
     -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
     -H "X-Goog-User-Project: vodafone-data-gov-poc" \
     -X PATCH \
  "https://datacatalog.googleapis.com/v2/projects/vodafone-data-gov-poc/locations/europe-west1/entryGroups/dc_glossary_test/entries/glossary?update_mask=core_aspects&allow_missing=false" \
  --data '{
    "display_name": "New display name",
    "core_aspects": {
      "business_context": {
        "aspect_type": "business_context",
        "json_content": {
          "description": "New Description",
          "contacts": ["mary.sue@company.com"]
        }
      }
    }
  }'
```

# Get the specific entry group information

```shell
curl -H "Content-Type: application/json" \
     -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
     -H "X-Goog-User-Project: vodafone-data-gov-poc" \
     -X GET \
     "https://datacatalog.googleapis.com/v2/projects/vodafone-data-gov-poc/locations/europe-west1/entryGroups/dc_glossary_test"
```

#
List entry groups in DataPlex

```shell
curl -H "Content-Type: application/json" \
     -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
     -H "X-Goog-User-Project: vodafone-data-gov-poc" \
     -X GET \
     "https://datacatalog.googleapis.com/v2/projects/vodafone-data-gov-poc/locations/europe-west1/entryGroups"
```

These entry_groups won't be visible in the Data Catalog UI, but necessary for DataPlex to work.
Every entry_group has a `dc_glossary_` prefix.


# Add a relationship 

First we need to find the entry name of the table we want to add the relationship to. We can do this by using the `lookup_entry` method of the datacatalog client.
The entry_name will look something like this: 

projects/vodafone-data-gov-poc/locations/europe-west1/entryGroups/@bigquery/entries/cHJvamVjdHMvdm9kYWZvbmUtZGF0YS1nb3YtcG9jL2RhdGFzZXRzL2ltZGIvdGFibGVzL3Jldmlld3M

```python
from google.cloud import datacatalog
datacatalog_client = datacatalog.DataCatalogClient()
request = datacatalog.LookupEntryRequest()
dataset = "imdb"
table = "reviews"
request.linked_resource = f'//bigquery.googleapis.com/projects/vodafone-data-gov-poc/datasets/{dataset}/tables/{table}'
datacatalog_client.lookup_entry(request=request)
```

Now we can use the entry_name to add a relationship to the table. We can do this by using the `Data Glossary API` as follows:

```shell

curl -H "Content-Type: application/json" \
     -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
     -H "X-Goog-User-Project: vodafone-data-gov-poc" \
     -X POST "https://datacatalog.googleapis.com/v2/projects/vodafone-data-gov-poc/locations/europe-west1/entryGroups/@bigquery/entries/cHJvamVjdHMvdm9kYWZvbmUtZGF0YS1nb3YtcG9jL2RhdGFzZXRzL2ltZGIvdGFibGVzL3Jldmlld3M/relationships" \
     --data '{
       "relationship_type": "is_described_by",
       "destination_entry_name": "projects/vodafone-data-gov-poc/locations/europe-west1/entryGroups/dc_glossary_test/entries/inventory_item",
       "source_column": "review"
     }'
```

google-datacatalog-looker-connector \
  --datacatalog-project-id vodafone-data-gov-poc \
  --looker-credentials-file looker2dc-looker-credentials.ini