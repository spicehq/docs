---
description: Custom Datasets and Views documentation.
hidden: true
icon: table
---

# Datasets

The Spice.ai platform comes pre-loaded with a variety of community [datasets](/broken/pages/JnKPswDs1OXhUD2OxR0K).

In addition, you can define and create your own custom and private Datasets and Views, which can then be queried with SQL, cached in Spice Firecache, and published publicly to be shared with others.

### Defining a Dataset

To define a dataset, first ensure your Spice project is connected to a [GitHub repository](apps/connect-github.md), then add a dataset manifest file to the GitHub repository in the `.spice/datasets` path.

For example:

```yaml
# .spice/datasets/recent_blocks.yml
name: taxi_trips
type: append
firecache:
  enabled: true
  trigger: number
  time_column: timestamp
```

Once the manifest file is committed to the GitHub repository, navigate to the **Datasets** section. The newly defined dataset will appear in the datasets list.

### Deploy the Dataset

Click the dataset **Deploy** button. Because this dataset was Firecache enabled, the firecache status will now turn to **Ready.**

### View Dataset details

Clicking the dataset will show its details along with it's deployments.
