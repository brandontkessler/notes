# Geo Maps

## Location within a bounding box

Given a bounding box, calculate the centroid

```python
coordinates = [
    [-80.47611, 37.185195],
    [-80.47611, 37.273387],
    [-80.381618, 37.273387],
    [-80.381618, 37.185195],
]

longs = np.unique([x[0] for x in coordinates])
lats = np.unique([x[1] for x in coordinates])

central_long = np.sum(longs) / 2
central_lat = np.sum(lats) / 2
```

## Plotting

```python
from mpl_toolkits.basemap import Basemap

m = Basemap(projection='merc', # mercator (close to equator)
            llcrnrlat = -35.62, # lower left corner lat
            llcrnrlon = -17.29,
            urcrnrlat = 37.73, # upper right corner lat
            urcrnrlon = 51.39)
m.fillcontinents(color='white', zorder=0) #zorder moves this to back
m.drawcoastlines(color='gray')
m.drawcountries(color='gray')

africa = pd.read_csv('africa.csv')
longs = africa['CapitalLongitude']
lats = africa['CapitalLatitude']
arabic = africa['Arabic']

m.scatter(longs.values,
          lats.values,
          latlon = True,
          c = arabic.values, # uses the arabic variable for special coloring
          cmap = 'Paired', # matplotlib colormap
          alpha = 1)
```