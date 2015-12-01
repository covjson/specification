# Common CoverageJSON Profiles Specification

WORK-IN-PROGRESS

<!-- Document Info -->
<table>
  <tr>
    <th>Authors</th>
    <td>
      Maik Riechert (<a href="http://www.reading.ac.uk">University of Reading</a>),
      Jon Blower (<a href="http://www.reading.ac.uk">University of Reading</a>)
    </td>
  </tr>
  <tr>
    <th>Revision</th>
    <td>0.1</td>
  </tr>
  <tr>
    <th>Date</th>
    <td>xx xxxx 2015</td>
  </tr>
  <tr>
    <th>Abstract</th>
    <td>
      
    </td>
  </tr>
  <tr>
    <th>Copyright</th>
    <td>
      Copyright &copy; 2015 by ... . This work is licensed under a
      <a href="http://creativecommons.org/licenses/by/4.0/">
        Creative Commons Attribution 4.0 International License</a>.
    </td>
  </tr>
</table>

## 1. Introduction



## 2. Domain profiles

Requirements for all domain profiles defined in this specification:
- The axis or component identifiers `"x"` and `"y"` must refer to horizontal spatial coordinates, 
`"z"` to vertical spatial coordinates, and all of `"x"`, `"y"`, and `"z"` must be referenced by a spatial coordinate reference system.
- The axis or component identifier `"t"` must refer to temporal coordinates and be referenced by a temporal reference system.
- If a spatial CRS is used that has the axes longitude and latitude, or easting and northing, then the axis or component identifier `"x"` must refer to longitude / easting, and `"y"` to latitude / northing.


### Overview of domain profiles

Profile              | x | y | z | t | composite
---------------------|---|---|---|---|----------
Grid                 |[M]|[M]|[O]|[O] 
VerticalProfile      | M | M |[M]| O
PointSeries          | M | M | O |[M]
Point                | M | M | O | O
PolygonSeries        |   |   | O |[M]| M
Polygon              |   |   | O | O | M
MultiPolygonSeries   |   |   | O |[M]| [M]
MultiPolygon         |   |   | O | O | [M]
Trajectory           |   |   | O |   | [M]
Section              |   |   |[M]|   | [M]


M = Mandatory, axis with single coordinate

O = Optional, axis with single coordinate

[M] = Mandatory, axis with multiple coordinates

[O] = Optional, axis with multiple coordinates


### 2.1. Grid

- A Grid domain must have the axes `"x"` and `"y"` and may have the axes `"z"` and `"t"`.
- The axis order must be either `"t","z","y","x"`, `"z","y","x"`, `"t","y","x"`, or `"y","x"`,
depending on which axes are defined.

Example:
```js
{
  "type": "Domain",
  "profile": "Grid",
  "axes": {
    "x": { "values": [1,2,3] },
    "y": { "values": [20,21] },
    "z": { "values": [1] },
    "t": { "values": ["2008-01-01T04:00:00Z"] }
  },
  "rangeAxisOrder": ["t","z","y","x"],
  "referencing": [...]
}
```

### 2.2. VerticalProfile

- A VerticalProfile domain must have the axes `"x"`, `"y"`, and `"z"`, where `"x"` and `"y"` must have a single coordinate only.

Example:
```js
{
  "type": "Domain",
  "profile": "VerticalProfile",
  "axes": {
    "x": { "values": [1] },
    "y": { "values": [21] },
    "z": { "values": [1,5,20] },
    "t": { "values": ["2008-01-01T04:00:00Z"] }
  },
  "referencing": [...]
}
```

### 2.3. PointSeries

- A PointSeries domain must have the axes `"x"`, `"y"`, and `"t"` where `"x"` and `"y"` must have a single coordinate only.
- A PointSeries domain may have the axis `"z"` which must have a single coordinate only.

Example:
```js
{
  "type": "Domain",
  "profile": "PointSeries",
  "axes": {
    "x": { "values": [1] },
    "y": { "values": [20] },
    "z": { "values": [1] },
    "t": { "values": ["2008-01-01T04:00:00Z","2008-01-01T05:00:00Z"] }
  },
  "referencing": [...]
}
```

### 2.4. Point

- A Point domain must have the axes `"x"` and `"y"` and may have the axes `"z"` and `"t"` where all must have a single coordinate only.

Example:
```js
{
  "type": "Domain",
  "profile": "Point",
  "axes": {
    "x": { "values": [1] },
    "y": { "values": [20] },
    "z": { "values": [1] },
    "t": { "values": ["2008-01-01T04:00:00Z"] }
  },
  "referencing": [...]
}
```

### 2.5. Trajectory

- A Trajectory domain must have the axis `"composite"` and may have the axis `"z"` where `"z"` must have a single coordinate only.
- The axis `"composite"` must have the geometry type `"Point"` and the components `"t","x","y","z"` or `"t","x","y"`.
- The coordinate ordering of the axis `"composite"` must follow the ordering of its `"t"` component.

Example:
```js
{
  "type": "Domain",
  "profile": "Trajectory",
  "axes": {
    "composite": {
      "compositeType": "Simple",
      "components": ["t","x","y","z"],      
      "values": [
        ["2008-01-01T04:00:00Z",1,20,1],
        ["2008-01-01T04:30:00Z",2,21,3]
      ]
    }
  },
  "referencing": [...]
}
```

Example without z:
```js
{
  "type": "Domain",
  "profile": "Trajectory",
  "axes": {
    "composite": {
      "compositeType": "Simple",
      "components": ["t","x","y"],      
      "values": [
        ["2008-01-01T04:00:00Z",1,20],
        ["2008-01-01T04:30:00Z",2,21]
      ]
    }
  },
  "referencing": [...]
}
```

Example with z defined as constant value:
```js
{
  "type": "Domain",
  "profile": "Trajectory",
  "axes": {
    "composite": {
      "compositeType": "Simple",
      "components": ["t","x","y"],      
      "values": [
        ["2008-01-01T04:00:00Z",1,20],
        ["2008-01-01T04:30:00Z",2,21]
      ]
    },
    "z": { "values": [5] }
  },
  "referencing": [...]
}
```

### 2.6. Section

- A Section domain must have the axes `"composite"` and `"z"`.
- The axis `"composite"` must have the composite type `"Simple"` and the components `"t","x","y"`.
- The coordinate ordering of the axis `"composite"` must follow the ordering of its `"t"` component.
- The axis order must be `"z","composite"`.

Example:
```js
{
  "type": "Domain",
  "profile": "Section",
  "axes": {
    "z": { "values": [10,20,30] },
    "composite": {
      "compositeType": "Simple",
      "components": ["t","x","y"],
      "values": [
        ["2008-01-01T04:00:00Z",1,20],
        ["2008-01-01T04:30:00Z",2,21]
      ]
    }
  },
  "rangeAxisOrder": ["z","composite"],
  "referencing": [...]
}
```

### 2.7. Polygon

Polygons are defined equally to GeoJSON, except that they can only contain `[x,y]` positions (and not `z` or additional dimensions).

- A LinearRing is an array of 4 or more `[x,y]` arrays where each of `x` and `y` is a coordinate value. The first and last `[x,y]` elements are identical.
- A Polygon is an array of LinearRing arrays. For Polygons with multiple rings, the first must be the exterior ring and any others must be interior rings or holes.
- A Polygon domain must have the axis `"composite"` which has a single Polygon coordinate.
- The axis `"composite"` must have the geometry type `"Polygon"` and the components `"x","y"`.
- A Polygon domain may have the axes `"z"` and `"t"` which both must have a single coordinate only.

Note that each polygon represents a single coordinate within the coordinate space.

Example:
```js
{
  "type": "Domain",
  "profile": "Polygon",
  "axes": {
    "composite": {
      "compositeType": "Polygon",
      "components": ["x","y"],
      "values": [
        [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]  ]
      ]
    }
    "z": { "values": [2] },
    "t": { "values": ["2008-01-01T04:00:00Z"] }
  },
  "referencing": [...]
}
```

### 2.8. PolygonSeries

- A PolygonSeries domain must have the axes `"composite"` and `"t"` where `"composite"` must have a single Polygon coordinate.
- A PolygonSeries domain may have the axis `"z"` which must have a single coordinate only.
- The axis `"composite"` must have the geometry type "Polygon" and the components `"x","y"`.

Example:
```js
{
  "type": "Domain",
  "profile": "PolygonSeries",
  "axes": {
    "composite": {
      "compositeType": "Polygon",
      "components": ["x","y"],
      "values": [
        [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]  ]
      ]
    }
    "z": { "values": [2] },
    "t": { "values": ["2008-01-01T04:00:00Z","2008-01-01T05:00:00Z"] }
  },
  "referencing": [...]
}
```

### 2.9. MultiPolygon

- A MultiPolygon domain must have the axis `"composite"` where the coordinates are Polygons.
- The axis `"composite"` must have the geometry type `"Polygon"` and the components `"x","y"`.
- A MultiPolygon domain may have the axes `"z"` and `"t"` which both must have a single coordinate only.

Example:
```js
{
  "type": "Domain",
  "profile": "MultiPolygon",
  "axes": {
    "composite": {
      "compositeType": "Polygon",
      "components": ["x","y"],
      "values": [
        [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]  ],
        [ [ [200.0, 10.0], [201.0, 10.0], [201.0, 11.0], [200.0, 11.0], [200.0, 10.0] ] ]
      ]
    }
    "z": { "values": [2] },
    "t": { "values": ["2008-01-01T04:00:00Z"] }
  },
  "referencing": [...]
}
```

### 2.10. MultiPolygonSeries

- A MultiPolygonSeries domain must have the axes `"composite"` and `"t"` where the coordinates of `"composite"` are Polygons.
- The axis `"composite"` must have the geometry type `"Polygon"` and the components `"x","y"`.
- A MultiPolygon domain may have the axis `"z"` which must have a single coordinate only.
- The axis order must be either `"t","z","composite"` or `"t","composite"`, depending on which axes are defined.

Example:
```js
{
  "type": "Domain",
  "profile": "MultiPolygonSeries",
  "axes": {
    "composite": {
      "compositeType": "Polygon",
      "components": ["x","y"],
      "values": [
        [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]  ],
        [ [ [200.0, 10.0], [201.0, 10.0], [201.0, 11.0], [200.0, 11.0], [200.0, 10.0] ] ]
      ]
    }
    "z": { "values": [2] },
    "t": { "values": ["2008-01-01T04:00:00Z", "2010-01-01T00:00:00Z"] }
  },
  "rangeAxisOrder": ["t","z","composite"],
  "referencing": [...]
}
```


## 3. Coverage profiles

`<DomainProfile>Coverage`

## 4. Coverage collection profiles

`<DomainProfile>CoverageCollection`
