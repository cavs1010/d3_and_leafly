# Creating an Interactive Map with Leaflet and D3.js 🌍

This guide will walk you through creating an interactive map using Leaflet and D3.js to visualize public park locations. We will cover setting up a tile layer, fetching data, styling markers, adding interactivity, and creating a legend.

## Step-by-Step Guide

### Step 1: Setting Up the Tile Layer 🗺️

The tile layer serves as the background of our map. We'll use OpenStreetMap tiles.

```javascript
let basemap = L.tileLayer(
  'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', 
  {
    attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
  }
);
```

### Step 2: Creating the Map Object 🌐

We create a map object with specific options such as center coordinates and zoom level.

```javascript
let map = L.map("map", {
  center: [37.7749, -122.4194], // Centered on San Francisco
  zoom: 12
});
```

### Step 3: Adding the Tile Layer to the Map 🏞️

We add the tile layer to our map.

```javascript
basemap.addTo(map);
```

### Step 4: Fetching Data 📊

We use D3.js to make a call and retrieve public park locations GeoJSON data.

```javascript
d3.json("https://data.sfgov.org/resource/94uf-amnx.geojson").then(function (data) {
  // Processing the data comes here
});
```

### Step 5: Styling the Markers 🎨

We define functions to style the markers based on properties like park size.

#### Style Info Function

Defines the style of each marker.

```javascript
function styleInfo(feature) {
  return {
    opacity: 1,
    fillOpacity: 1,
    fillColor: getColor(feature.properties.acres),
    color: "#000000",
    radius: getRadius(feature.properties.acres),
    stroke: true,
    weight: 0.5
  };
}
```

#### Color Function

Determines the marker color based on the park's size.

```javascript
function getColor(acres) {
  switch (true) {
    case acres > 50:
      return "#006400";
    case acres > 20:
      return "#228B22";
    case acres > 10:
      return "#32CD32";
    default:
      return "#7CFC00";
  }
}
```

#### Radius Function

Determines the marker radius based on the park's size.

```javascript
function getRadius(acres) {
  if (acres === 0) {
    return 1;
  }
  return Math.sqrt(acres) * 2;
}
```

### Step 6: Adding GeoJSON Layer to the Map 📍

We add the GeoJSON layer to the map, turning each feature into a circle marker and setting its style. The `L.geoJson` function allows us to add GeoJSON data to the map and provides options to style and interact with the data.

#### Adding and Styling Markers

```javascript
L.geoJson(data, {
  pointToLayer: function (feature, latlng) {
    return L.circleMarker(latlng);
  },
  style: styleInfo,
  onEachFeature: function (feature, layer) {
    // Creating a popup for each marker
    layer.bindPopup(
      `<h3>${feature.properties.parkname}</h3>
       <p><strong>Acres:</strong> ${feature.properties.acres}</p>
       <p><strong>Location:</strong> ${feature.properties.location}</p>`
    );

    // Adding interactivity: highlight feature on hover
    layer.on({
      mouseover: function (event) {
        layer = event.target;
        layer.setStyle({
          weight: 2,
          color: "#666",
          fillOpacity: 0.7
        });
      },
      mouseout: function (event) {
        geojson.resetStyle(event.target);
      },
      click: function (event) {
        map.fitBounds(event.target.getBounds());
      }
    });
  }
}).addTo(map);
```

#### Explanation of `onEachFeature` and `layer.on`

**`onEachFeature`**:
- **Purpose**: This option allows you to define what happens for each feature (in this case, each park) in the GeoJSON layer. It is used to bind a popup and add interactivity to each marker.
- **Parameters**: It takes two parameters:
  - `feature`: The GeoJSON feature being processed.
  - `layer`: The Leaflet layer representing the feature on the map.
- **Usage**: Inside `onEachFeature`, we bind a popup to the layer to display information about the park. We also add event listeners to the layer for mouseover, mouseout, and click events.

**`layer.on`**:
- **Purpose**: Adds event listeners to each layer to make the map more interactive.
- **Events**:
  - `mouseover`: This event triggers when the mouse pointer is over the layer. We change the style of the layer to highlight it.
  - `mouseout`: This event triggers when the mouse pointer leaves the layer. We reset the style of the layer.
  - `click`: This event triggers when the layer is clicked. We zoom in to the bounds of the clicked layer.

### Step 7: Creating and Adding a Legend 🗺️

We create a legend control object and add it to the map.

#### Creating the Legend

```javascript
let legend = L.control({ position: "bottomright" });

legend.onAdd = function () {
  let div = L.DomUtil.create("div", "info legend");
  let grades = [0, 10, 20, 50];
  let colors = [
    "#7CFC00",
    "#32CD32",
    "#228B22",
    "#006400"
  ];

  for (let i = 0; i < grades.length; i++) {
    div.innerHTML += "<i style='background: " + colors[i] + "'></i> " +
      grades[i] + (grades[i + 1] ? "&ndash;" + grades[i + 1] + "<br>" : "+");
  }
  return div;
};
```
#### Explanation of Creating the Legend
**Creating the Legend:**
**Purpose:** The legend provides a visual guide to the colors and their corresponding park sizes.
Steps:
1. Positioning: The legend is positioned at the bottom right of the map.
2. Creating Div: A div element is created to hold the legend content.
3. Grades and Colors: We define the size ranges (grades) and their corresponding colors.
4. Generating Legend Items: We loop through the grades and create a colored square for each grade, adding them to the div.

#### Adding the Legend to the Map

```javascript
legend.addTo(map);
```

## Summary 🚀

By following these steps, you will create an interactive map that visualizes public park locations. The key concepts covered include:

- **Setting up a tile layer**: Providing a visual background for the map. 🗺️
- **Creating a map object**: Specifying center and zoom level. 🌐
- **Fetching data with D3.js**: Retrieving and processing external data. 📊
- **Styling markers**: Customizing appearance based on data properties. 🎨
- **Adding interactivity**: Implementing hover effects and clickable features. 📍
- **Creating a legend**: Providing a visual guide for interpreting marker colors. 🗺️

### Interactivity Overview:

- **Hover Effects**: Markers change style when hovered over, making it easier to spot details. 🖱️
- **Clickable Features**: Clicking a marker zooms into its location for a closer look. 🔍
- **Popups**: Each marker displays detailed information about the park when clicked. 💬

This example can be adapted to visualize various types of geospatial data with similar techniques, enhancing your ability to create interactive and informative maps. 🌍
