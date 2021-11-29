Use seaborn to color format the dataframes.
Use dataframe_image to print to png.

Dataframe image can be installed with pip or conda forge.

```python
import pandas as pd
import seaborn as sns
import dataframe_image as dfi

cm = sns.color_palette("RdYlGn", as_cmap=True)

pretty_df = df.style.background_gradient(cmap=cm)

dfi.export(pretty_df, 'images/pretty_df.png')
```