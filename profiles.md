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



## 2. Common Profiles

This specification defines the following domain profiles: Grid, VerticalProfile, PointSeries, Point, MultiPointSeries, MultiPoint, PolygonSeries, Polygon, MultiPolygonSeries, MultiPolygon, Trajectory, Section. Each domain profile `<DomainProfile>` has a corresponding coverage profile named `<DomainProfile>Coverage`, and a coverage collection profile named `<DomainProfile>CoverageCollection`.

Requirements for all domain profiles defined in this specification:
- The axis and component identifiers `"x"` and `"y"` must refer to horizontal spatial coordinates, 
`"z"` to vertical spatial coordinates, and all of `"x"`, `"y"`, and `"z"` must be referenced by a spatial coordinate reference system.
- The axis and component identifier `"t"` must refer to temporal coordinates and be referenced by a temporal reference system.
- If a spatial CRS is used that has the axes longitude and latitude, or easting and northing, then the axis and component identifier `"x"` must refer to longitude / easting, and `"y"` to latitude / northing.
- A domain that states conformance to one of the profiles in this specification may have any number of additional one-coordinate axes not defined here.

Requirements for all coverage profiles defined in this specification:
- A coverage with `<DomainProfile>Coverage` profile must have a domain with `<DomainProfile>` profile.
- The axis ordering in `"axisNames"` of NdArray objects should follow the order "t", "z", "y, "x", "composite", leaving out all axes that do not exist.

Requirements for all coverage collection profiles defined in this specification:
- A coverage collection with `<DomainProfile>CoverageCollection` profile must only contain coverages with `<DomainProfile>Coverage` profile.

### Overview of domain profiles

Profile              | x | y | z | t | composite
---------------------|---|---|---|---|----------
Grid                 | + | + |[+]|[+] 
VerticalProfile      | 1 | 1 | + | [1]
PointSeries          | 1 | 1 |[1]| [+]
Point                | 1 | 1 |[1]| [1]
MultiPointSeries     |   |   |   |[+]| [+]
MultiPoint           |   |   |   |[1]| [+]
PolygonSeries        |   |   |[1]|[+]| 1
Polygon              |   |   |[1]|[1]| 1
MultiPolygonSeries   |   |   |[1]|[+]| [+]
MultiPolygon         |   |   |[1]|[1]| [+]
Trajectory           |   |   |[1]|   | [+]
Section              |   |   |[+]|   | [+]

Symbol | Description
-----|--------------------
1   | Axis with one coordinate
[1] | Optional axis with one coordinate
+   | Axis with one or more coordinates
[+] | Optional axis with one or more coordinates



### 2.1. Grid

#### Grid profile

- A domain with Grid profile must have the axes `"x"` and `"y"` and may have the axes `"z"` and `"t"`.

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
  "referencing": [...]
}
```

#### GridCoverage profile

- A coverage with GridCoverage profile must have a domain with Grid profile.

Example:
```js
{
  "type" : "Coverage",
  "profile" : "GridCoverage",
  "domain" : {
    "type" : "Domain",
    "profile" : "Grid",
    "axes": {
      "x": { "values": [1,2,3] },
      "y": { "values": [20,21] },
      "z": { "values": [1] },
      "t": { "values": ["2008-01-01T04:00:00Z"] }
    },
    "referencing": [...]
  },
  "parameters" : {
    "temperature": {...}
  },
  "ranges" : {
    "temperature" : {
      "type" : "NdArray",
      "dataType": "float",
      "axisNames": ["t", "z", "y", "x"],
      "shape": [1, 1, 2, 3],
      "values" : [...]
    }
  }
}
```

### 2.2. VerticalProfile

#### VerticalProfile profile

- A domain with VerticalProfile profile must have the axes `"x"`, `"y"`, and `"z"`, where `"x"` and `"y"` must have a single coordinate only.

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

#### VerticalProfileCoverage profile

- A coverage with VerticalProfileCoverage profile must have a domain with VerticalProfile profile.

Example:
```js
{
  "type" : "Coverage",
  "profile" : "VerticalProfileCoverage",
  "domain" : {
    "type": "Domain",
    "profile": "VerticalProfile",
    "axes": {
      "x": { "values": [1] },
      "y": { "values": [21] },
      "z": { "values": [1,5,20] },
      "t": { "values": ["2008-01-01T04:00:00Z"] }
    },
    "referencing": [...]
  },
  "parameters" : {
    "temperature": {...}
  },
  "ranges" : {
    "temperature" : {
      "type" : "NdArray",
      "dataType": "float",
      "axisNames": ["t", "z", "y", "x"],
      "shape": [1, 3, 1, 1],
      "values" : [...]
    }
  }
}
```

### 2.3. PointSeries

#### PointSeries profile

- A domain with PointSeries profile must have the axes `"x"`, `"y"`, and `"t"` where `"x"` and `"y"` must have a single coordinate only.
- A domain with PointSeries profile may have the axis `"z"` which must have a single coordinate only.

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

#### PointSeriesCoverage profile

- A coverage with PointSeriesCoverage profile must have a domain with PointSeries profile.

Example:
```js
{
  "type" : "Coverage",
  "profile" : "PointSeriesCoverage",
  "domain" : {
    "type": "Domain",
    "profile": "PointSeries",
    "axes": {
      "x": { "values": [1] },
      "y": { "values": [20] },
      "z": { "values": [1] },
      "t": { "values": ["2008-01-01T04:00:00Z","2008-01-01T05:00:00Z"] }
    },
    "referencing": [...]
  },
  "parameters" : {
    "temperature": {...}
  },
  "ranges" : {
    "temperature" : {
      "type" : "NdArray",
      "dataType": "float",
      "axisNames": ["t", "z", "y", "x"],
      "shape": [2, 1, 1, 1],
      "values" : [...]
    }
  }
}
```

### 2.4. Point

#### Point profile

- A domain with Point profile must have the axes `"x"` and `"y"` and may have the axes `"z"` and `"t"` where all must have a single coordinate only.

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

#### PointCoverage profile

- A coverage with PointCoverage profile must have a domain with Point profile.

Example:
```js
{
  "type" : "Coverage",
  "profile" : "PointCoverage",
  "domain" : {
    "type": "Domain",
    "profile": "Point",
    "axes": {
      "x": { "values": [1] },
      "y": { "values": [20] },
      "z": { "values": [1] },
      "t": { "values": ["2008-01-01T04:00:00Z"] }
    },
    "referencing": [...]
  },
  "parameters" : {
    "temperature": {...}
  },
  "ranges" : {
    "temperature" : {
      "type" : "NdArray",
      "dataType": "float",
      "axisNames": ["t", "z", "y", "x"],
      "shape": [1, 1, 1, 1],
      "values" : [...]
    }
  }
}
```

### 2.5. MultiPointSeries

#### MultiPointSeries profile

- A domain with MultiPointSeries profile must have the axes `"composite"` and `"t"`.
- The axis `"composite"` must have the data type `"Tuple"` and the component identifiers `"x","y","z"` or `"x","y"`.

Example:
```js
{
  "type": "Domain",
  "profile": "MultiPointSeries",
  "axes": {
    "t": { "values": ["2008-01-01T04:00:00Z", "2008-01-01T05:00:00Z"] },
    "composite": {
      "dataType": "Tuple",
      "components": ["x","y","z"],
      "values": [
        [1, 20, 1],
        [2, 21, 3]
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
  "profile": "MultiPointSeries",
  "axes": {
    "t": { "values": ["2008-01-01T04:00:00Z", "2008-01-01T05:00:00Z"] },
    "composite": {
      "dataType": "Tuple",
      "components": ["x","y"],
      "values": [
        [1, 20],
        [2, 21]
      ]
    }    
  },
  "referencing": [...]
}
```

#### MultiPointSeriesCoverage profile

- A coverage with MultiPointSeriesCoverage profile must have a domain with MultiPointSeries profile.

Example:
```js
{
  "type" : "Coverage",
  "profile" : "MultiPointSeriesCoverage",
  "domain" : {
    "type": "Domain",
    "profile": "MultiPointSeries",
    "axes": {
      "t": { "values": ["2008-01-01T04:00:00Z", "2008-01-01T05:00:00Z"] },
      "composite": {
        "dataType": "Tuple",
        "components": ["x","y","z"],
        "values": [
          [1, 20, 1],
          [2, 21, 3],
          [2, 20, 4]
        ]
      }
    }
  },
  "parameters" : {
    "temperature": {...}
  },
  "ranges" : {
    "temperature" : {
      "type" : "NdArray",
      "dataType": "float",
      "axisNames": ["t", "composite"],
      "shape": [2, 3],
      "values" : [...]
    }
  }
}
```


### 2.6. MultiPoint

#### MultiPoint profile

- A domain with MultiPoint profile must have the axis `"composite"` and may have the axis `"t"` where `"t"` must have a single coordinate only.
- The axis `"composite"` must have the data type `"Tuple"` and the component identifiers `"x","y","z"` or `"x","y"`.

Example:
```js
{
  "type": "Domain",
  "profile": "MultiPoint",
  "axes": {
    "t": { "values": ["2008-01-01T04:00:00Z"] },
    "composite": {
      "dataType": "Tuple",
      "components": ["x","y","z"],
      "values": [
        [1, 20, 1],
        [2, 21, 3]
      ]
    }
  },
  "referencing": [...]
}
```

Example without z and t:
```js
{
  "type": "Domain",
  "profile": "MultiPoint",
  "axes": {
    "composite": {
      "dataType": "Tuple",
      "components": ["x","y"],
      "values": [
        [1, 20],
        [2, 21]
      ]
    }    
  },
  "referencing": [...]
}
```

#### MultiPointCoverage profile

- A coverage with MultiPointCoverage profile must have a domain with MultiPoint profile.

Example:
```js
{
  "type" : "Coverage",
  "profile" : "MultiPointCoverage",
  "domain" : {
    "type": "Domain",
    "profile": "MultiPoint",
    "axes": {
      "t": { "values": ["2008-01-01T04:00:00Z"] },
      "composite": {
        "dataType": "Tuple",
        "components": ["x","y","z"],
        "values": [
          [1, 20, 1],
          [2, 21, 3]
        ]
      }
    }
  },
  "parameters" : {
    "temperature": {...}
  },
  "ranges" : {
    "temperature" : {
      "type" : "NdArray",
      "dataType": "float",
      "axisNames": ["t", "composite"],
      "shape": [1, 2],
      "values" : [...]
    }
  }
}
```

### 2.7. Trajectory

#### Trajectory profile

- A domain with Trajectory profile must have the axis `"composite"` and may have the axis `"z"` where `"z"` must have a single coordinate only.
- The axis `"composite"` must have the data type `"Tuple"` and the component identifiers `"t","x","y","z"` or `"t","x","y"`.
- The value ordering of the axis `"composite"` must follow the ordering of its `"t"` component as defined in the corresponding reference system.

Example:
```js
{
  "type": "Domain",
  "profile": "Trajectory",
  "axes": {
    "composite": {
      "dataType": "Tuple",
      "components": ["t","x","y","z"],      
      "values": [
        ["2008-01-01T04:00:00Z", 1, 20, 1],
        ["2008-01-01T04:30:00Z", 2, 21, 3]
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
      "dataType": "Tuple",
      "components": ["t","x","y"],      
      "values": [
        ["2008-01-01T04:00:00Z", 1, 20],
        ["2008-01-01T04:30:00Z", 2, 21]
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
      "dataType": "Tuple",
      "components": ["t","x","y"],      
      "values": [
        ["2008-01-01T04:00:00Z", 1, 20],
        ["2008-01-01T04:30:00Z", 2, 21]
      ]
    },
    "z": { "values": [5] }
  },
  "referencing": [...]
}
```

#### TrajectoryCoverage profile

- A coverage with TrajectoryCoverage profile must have a domain with Trajectory profile.

Example:
```js
{
  "type" : "Coverage",
  "profile" : "TrajectoryCoverage",
  "domain" : {
    "type": "Domain",
    "profile": "Trajectory",
    "axes": {
      "composite": {
        "dataType": "Tuple",
        "components": ["t","x","y","z"],      
        "values": [
          ["2008-01-01T04:00:00Z", 1, 20, 1],
          ["2008-01-01T04:30:00Z", 2, 21, 3]
        ]
      }
    },
    "referencing": [...]
  },
  "parameters" : {
    "temperature": {...}
  },
  "ranges" : {
    "temperature" : {
      "type" : "NdArray",
      "dataType": "float",
      "axisNames": ["composite"],
      "shape": [2],
      "values" : [...]
    }
  }
}
```

### 2.8. Section

#### Section profile

- A domain with Section profile must have the axes `"composite"` and `"z"`.
- The axis `"composite"` must have the data type `"Tuple"` and the component identifiers `"t","x","y"`.
- The value ordering of the axis `"composite"` must follow the ordering of its `"t"` component as defined in the corresponding reference system.

Example:
```js
{
  "type": "Domain",
  "profile": "Section",
  "axes": {
    "z": { "values": [10,20,30] },
    "composite": {
      "dataType": "Tuple",
      "components": ["t","x","y"],
      "values": [
        ["2008-01-01T04:00:00Z", 1, 20],
        ["2008-01-01T04:30:00Z", 2, 21]
      ]
    }
  },
  "referencing": [...]
}
```

#### SectionCoverage profile

- A coverage with SectionCoverage profile must have a domain with Section profile.

Example:
```js
{
  "type" : "Coverage",
  "profile" : "SectionCoverage",
  "domain" : {
    "type": "Domain",
    "profile": "Section",
    "axes": {
      "z": { "values": [10,20,30] },
      "composite": {
        "dataType": "Tuple",
        "components": ["t","x","y"],
        "values": [
          ["2008-01-01T04:00:00Z", 1, 20],
          ["2008-01-01T04:30:00Z", 2, 21]
        ]
      }
    },
    "referencing": [...]
  },
  "parameters" : {
    "temperature": {...}
  },
  "ranges" : {
    "temperature" : {
      "type" : "NdArray",
      "dataType": "float",
      "axisNames": ["z", "composite"],
      "shape": [3, 2],
      "values" : [...]
    }
  }
}
```

### 2.9. Polygon

#### Polygon profile

Polygons in this domain profile are defined equally to GeoJSON, except that they can only contain `[x,y]` positions (and not `z` or additional components):
- A LinearRing is an array of 4 or more `[x,y]` arrays where each of `x` and `y` is a coordinate value. The first and last `[x,y]` elements are identical.
- A Polygon is an array of LinearRing arrays. For Polygons with multiple rings, the first must be the exterior ring and any others must be interior rings or holes.

- A domain with Polygon profile must have the axis `"composite"` which has a single Polygon value.
- The axis `"composite"` must have the data type `"Polygon"` and the component identifiers `"x","y"`.
- A Polygon domain may have the axes `"z"` and `"t"` which both must have a single value only.

Example:
```js
{
  "type": "Domain",
  "profile": "Polygon",
  "axes": {
    "composite": {
      "dataType": "Polygon",
      "components": ["x","y"],
      "values": [
        [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]  ]
      ]
    },
    "z": { "values": [2] },
    "t": { "values": ["2008-01-01T04:00:00Z"] }
  },
  "referencing": [...]
}
```

#### PolygonCoverage profile

- A coverage with PolygonCoverage profile must have a domain with Polygon profile.

Example:
```js
{
  "type" : "Coverage",
  "profile" : "PolygonCoverage",
  "domain" : {
    "type": "Domain",
    "profile": "Polygon",
    "axes": {
      "composite": {
        "dataType": "Polygon",
        "components": ["x","y"],
        "values": [
          [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]  ]
        ]
      },
      "z": { "values": [2] },
      "t": { "values": ["2008-01-01T04:00:00Z"] }
    },
    "referencing": [...]
  },
  "parameters" : {
    "temperature": {...}
  },
  "ranges" : {
    "temperature" : {
      "type" : "NdArray",
      "dataType": "float",
      "axisNames": ["t", "z", "composite"],
      "shape": [1, 1, 1],
      "values" : [...]
    }
  }
}
```

### 2.10. PolygonSeries

#### PolygonSeries profile

- A domain with PolygonSeries profile must have the axes `"composite"` and `"t"` where `"composite"` must have a single Polygon value. Polygons are defined in the Polygon profile.
- A domain with PolygonSeries profile may have the axis `"z"` which must have a single value only.
- The axis `"composite"` must have the data type `"Polygon"` and the component identifiers `"x","y"`.

Example:
```js
{
  "type": "Domain",
  "profile": "PolygonSeries",
  "axes": {
    "composite": {
      "dataType": "Polygon",
      "components": ["x","y"],
      "values": [
        [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]  ]
      ]
    },
    "z": { "values": [2] },
    "t": { "values": ["2008-01-01T04:00:00Z","2008-01-01T05:00:00Z"] }
  },
  "referencing": [...]
}
```

#### PolygonSeriesCoverage profile

- A coverage with PolygonSeriesCoverage profile must have a domain with PolygonSeries profile.

Example:
```js
{
  "type" : "Coverage",
  "profile" : "PolygonSeriesCoverage",
  "domain" : {
    "type": "Domain",
    "profile": "PolygonSeries",
    "axes": {
      "composite": {
        "dataType": "Polygon",
        "components": ["x","y"],
        "values": [
          [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]  ]
        ]
      },
      "z": { "values": [2] },
      "t": { "values": ["2008-01-01T04:00:00Z","2008-01-01T05:00:00Z"] }
    },
    "referencing": [...]
  },
  "parameters" : {
    "temperature": {...}
  },
  "ranges" : {
    "temperature" : {
      "type" : "NdArray",
      "dataType": "float",
      "axisNames": ["t", "z", "composite"],
      "shape": [2, 1, 1],
      "values" : [...]
    }
  }
}
```

### 2.11. MultiPolygon

#### MultiPolygon profile

- A domain with MultiPolygon profile must have the axis `"composite"` where the values are Polygons. Polygons are defined in the Polygon profile.
- The axis `"composite"` must have the data type `"Polygon"` and the component identifiers `"x","y"`.
- A MultiPolygon domain may have the axes `"z"` and `"t"` which both must have a single value only.

Example:
```js
{
  "type": "Domain",
  "profile": "MultiPolygon",
  "axes": {
    "composite": {
      "dataType": "Polygon",
      "components": ["x","y"],
      "values": [
        [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]  ],
        [ [ [200.0, 10.0], [201.0, 10.0], [201.0, 11.0], [200.0, 11.0], [200.0, 10.0] ] ]
      ]
    },
    "z": { "values": [2] },
    "t": { "values": ["2008-01-01T04:00:00Z"] }
  },
  "referencing": [...]
}
```

#### MultiPolygonCoverage profile

- A coverage with MultiPolygonCoverage profile must have a domain with MultiPolygon profile.

Example:
```js
{
  "type" : "Coverage",
  "profile" : "MultiPolygonCoverage",
  "domain" : {
    "type": "Domain",
    "profile": "MultiPolygon",
    "axes": {
      "composite": {
        "dataType": "Polygon",
        "components": ["x","y"],
        "values": [
          [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]  ],
          [ [ [200.0, 10.0], [201.0, 10.0], [201.0, 11.0], [200.0, 11.0], [200.0, 10.0] ] ]
        ]
      },
      "z": { "values": [2] },
      "t": { "values": ["2008-01-01T04:00:00Z"] }
    },
    "referencing": [...]
  },
  "parameters" : {
    "temperature": {...}
  },
  "ranges" : {
    "temperature" : {
      "type" : "NdArray",
      "dataType": "float",
      "axisNames": ["t", "z", "composite"],
      "shape": [1, 1, 2],
      "values" : [...]
    }
  }
}
```

### 2.12. MultiPolygonSeries

#### MultiPolygonSeries profile

- A domain with MultiPolygonSeries profile must have the axes `"composite"` and `"t"` where the values of `"composite"` are Polygons. Polygons are defined in the Polygon profile.
- The axis `"composite"` must have the data type `"Polygon"` and the component identifiers `"x","y"`.
- A MultiPolygon domain may have the axis `"z"` which must have a single value only.

Example:
```js
{
  "type": "Domain",
  "profile": "MultiPolygonSeries",
  "axes": {
    "composite": {
      "dataType": "Polygon",
      "components": ["x","y"],
      "values": [
        [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]  ],
        [ [ [200.0, 10.0], [201.0, 10.0], [201.0, 11.0], [200.0, 11.0], [200.0, 10.0] ] ]
      ]
    },
    "z": { "values": [2] },
    "t": { "values": ["2008-01-01T04:00:00Z", "2010-01-01T00:00:00Z"] }
  },
  "referencing": [...]
}
```

#### MultiPolygonSeriesCoverage profile

- A coverage with MultiPolygonSeriesCoverage profile must have a domain with MultiPolygonSeries profile.

Example:
```js
{
  "type" : "Coverage",
  "profile" : "MultiPolygonSeriesCoverage",
  "domain" : {
    "type": "Domain",
    "profile": "MultiPolygonSeries",
    "axes": {
      "composite": {
        "dataType": "Polygon",
        "components": ["x","y"],
        "values": [
          [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]  ],
          [ [ [200.0, 10.0], [201.0, 10.0], [201.0, 11.0], [200.0, 11.0], [200.0, 10.0] ] ]
        ]
      },
      "z": { "values": [2] },
      "t": { "values": ["2008-01-01T04:00:00Z", "2010-01-01T00:00:00Z", "2012-01-01T00:00:00Z"] }
    },
    "referencing": [...]
  },
  "parameters" : {
    "temperature": {...}
  },
  "ranges" : {
    "temperature" : {
      "type" : "NdArray",
      "dataType": "float",
      "axisNames": ["t", "z", "composite"],
      "shape": [3, 1, 2],
      "values" : [...]
    }
  }
}
```

