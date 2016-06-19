# Common CoverageJSON Domain Types Specification

WORK-IN-PROGRESS

<!-- Document Info -->
<table class="table">
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

## Contents
{:.no_toc}

* TOC
{:toc}

## 1. Introduction

### 1.1. Definitions

- JavaScript Object Notation (JSON), and the terms object, name, value, array, string, number, and null, are defined in [IETF RFC 4627](http://www.ietf.org/rfc/rfc4627.txt).
- JSON-LD is defined in [http://www.w3.org/TR/json-ld/](http://www.w3.org/TR/json-ld/).
- The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [IETF RFC 2119](http://www.ietf.org/rfc/rfc2119.txt).

## 2. Common Domain Types

This specification defines the following domain domain types: Grid, VerticalProfile, PointSeries, Point, MultiPointSeries, MultiPoint, PolygonSeries, Polygon, MultiPolygonSeries, MultiPolygon, Trajectory, Section.

Requirements for all domain types defined in this specification:
- The axis and component identifiers `"x"` and `"y"` MUST refer to horizontal spatial coordinates, 
`"z"` to vertical spatial coordinates, and all of `"x"`, `"y"`, and `"z"` MUST be referenced by a spatial coordinate reference system.
- The axis and component identifier `"t"` MUST refer to temporal coordinates and be referenced by a temporal reference system.
- If a spatial CRS is used that has the axes longitude and latitude, or easting and northing, then the axis and component identifier `"x"` MUST refer to longitude / easting, and `"y"` to latitude / northing.
- A domain that states conformance to one of the domain types in this specification MAY have any number of additional one-coordinate axes not defined here.
- In a Coverage object, the axis ordering in `"axisNames"` of NdArray objects SHOULD follow the order "t", "z", "y, "x", "composite", leaving out all axes that do not exist or are single-valued.

### Overview of domain types

Domain Type          | x | y | z | t | composite
---------------------|---|---|---|---|----------
Grid                 | + | + |[+]|[+] 
VerticalProfile      | 1 | 1 | + |[1]
PointSeries          | 1 | 1 |[1]| +
Point                | 1 | 1 |[1]|[1]
MultiPointSeries     |   |   |   | + | +
MultiPoint           |   |   |   |[1]| +
PolygonSeries        |   |   |[1]| + | 1
Polygon              |   |   |[1]|[1]| 1
MultiPolygonSeries   |   |   |[1]| + | +
MultiPolygon         |   |   |[1]|[1]| +
Trajectory           |   |   |[1]|   | +
Section              |   |   | + |   | +

Symbol | Description
-----|--------------------
1   | Axis with one coordinate
[1] | Optional axis with one coordinate
+   | Axis with one or more coordinates
[+] | Optional axis with one or more coordinates



### 2.1. Grid

- A domain with Grid domain type MUST have the axes `"x"` and `"y"` and MAY have the axes `"z"` and `"t"`.

Domain example:

```json
{
  "type": "Domain",
  "domainType": "Grid",
  "axes": {
    "x": { "values": [1,2,3] },
    "y": { "values": [20,21] },
    "z": { "values": [1] },
    "t": { "values": ["2008-01-01T04:00:00Z"] }
  },
  "referencing": [...]
}
```

Coverage example:

```json
{
  "type" : "Coverage",
  "domain" : {
    "type" : "Domain",
    "domainType" : "Grid",
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

- A domain with VerticalProfile domain type MUST have the axes `"x"`, `"y"`, and `"z"`, where `"x"` and `"y"` MUST have a single coordinate only.

Domain example:

```json
{
  "type": "Domain",
  "domainType": "VerticalProfile",
  "axes": {
    "x": { "values": [1] },
    "y": { "values": [21] },
    "z": { "values": [1,5,20] },
    "t": { "values": ["2008-01-01T04:00:00Z"] }
  },
  "referencing": [...]
}
```

Coverage example:

```json
{
  "type" : "Coverage",
  "domain" : {
    "type": "Domain",
    "domainType": "VerticalProfile",
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
      "axisNames": ["z"],
      "shape": [3],
      "values" : [...]
    }
  }
}
```

### 2.3. PointSeries

- A domain with PointSeries domain type MUST have the axes `"x"`, `"y"`, and `"t"` where `"x"` and `"y"` MUST have a single coordinate only.
- A domain with PointSeries domain type MAY have the axis `"z"` which MUST have a single coordinate only.

Domain example:

```json
{
  "type": "Domain",
  "domainType": "PointSeries",
  "axes": {
    "x": { "values": [1] },
    "y": { "values": [20] },
    "z": { "values": [1] },
    "t": { "values": ["2008-01-01T04:00:00Z","2008-01-01T05:00:00Z"] }
  },
  "referencing": [...]
}
```

Coverage example:

```json
{
  "type" : "Coverage",
  "domain" : {
    "type": "Domain",
    "domainType": "PointSeries",
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
      "axisNames": ["t"],
      "shape": [2],
      "values" : [...]
    }
  }
}
```

### 2.4. Point

- A domain with Point domain type MUST have the axes `"x"` and `"y"` and MAY have the axes `"z"` and `"t"` where all MUST have a single coordinate only.

Domain example:

```json
{
  "type": "Domain",
  "domainType": "Point",
  "axes": {
    "x": { "values": [1] },
    "y": { "values": [20] },
    "z": { "values": [1] },
    "t": { "values": ["2008-01-01T04:00:00Z"] }
  },
  "referencing": [...]
}
```

Coverage example:

```json
{
  "type" : "Coverage",
  "domain" : {
    "type": "Domain",
    "domainType": "Point",
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
      "values" : [...]
    }
  }
}
```

### 2.5. MultiPointSeries

- A domain with MultiPointSeries domain type MUST have the axes `"composite"` and `"t"`.
- The axis `"composite"` MUST have the data type `"tuple"` and the component identifiers `"x","y","z"` or `"x","y"`.

Domain example:

```json
{
  "type": "Domain",
  "domainType": "MultiPointSeries",
  "axes": {
    "t": { "values": ["2008-01-01T04:00:00Z", "2008-01-01T05:00:00Z"] },
    "composite": {
      "dataType": "tuple",
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

Domain example without z:

```json
{
  "type": "Domain",
  "domainType": "MultiPointSeries",
  "axes": {
    "t": { "values": ["2008-01-01T04:00:00Z", "2008-01-01T05:00:00Z"] },
    "composite": {
      "dataType": "tuple",
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

Coverage example:

```json
{
  "type" : "Coverage",
  "domain" : {
    "type": "Domain",
    "domainType": "MultiPointSeries",
    "axes": {
      "t": { "values": ["2008-01-01T04:00:00Z", "2008-01-01T05:00:00Z"] },
      "composite": {
        "dataType": "tuple",
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

- A domain with MultiPoint domain type MUST have the axis `"composite"` and MAY have the axis `"t"` where `"t"` MUST have a single coordinate only.
- The axis `"composite"` MUST have the data type `"tuple"` and the component identifiers `"x","y","z"` or `"x","y"`.

Domain example:

```json
{
  "type": "Domain",
  "domainType": "MultiPoint",
  "axes": {
    "t": { "values": ["2008-01-01T04:00:00Z"] },
    "composite": {
      "dataType": "tuple",
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

Domain example without z and t:

```json
{
  "type": "Domain",
  "domainType": "MultiPoint",
  "axes": {
    "composite": {
      "dataType": "tuple",
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

Coverage example:

```json
{
  "type" : "Coverage",
  "domain" : {
    "type": "Domain",
    "domainType": "MultiPoint",
    "axes": {
      "t": { "values": ["2008-01-01T04:00:00Z"] },
      "composite": {
        "dataType": "tuple",
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
      "axisNames": ["composite"],
      "shape": [2],
      "values" : [...]
    }
  }
}
```

### 2.7. Trajectory

- A domain with Trajectory domain type MUST have the axis `"composite"` and MAY have the axis `"z"` where `"z"` MUST have a single coordinate only.
- The axis `"composite"` MUST have the data type `"tuple"` and the component identifiers `"t","x","y","z"` or `"t","x","y"`.
- The value ordering of the axis `"composite"` MUST follow the ordering of its `"t"` component as defined in the corresponding reference system.

Domain example:

```json
{
  "type": "Domain",
  "domainType": "Trajectory",
  "axes": {
    "composite": {
      "dataType": "tuple",
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

Domain example without z:

```json
{
  "type": "Domain",
  "domainType": "Trajectory",
  "axes": {
    "composite": {
      "dataType": "tuple",
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

Domain example with z defined as constant value:

```json
{
  "type": "Domain",
  "domainType": "Trajectory",
  "axes": {
    "composite": {
      "dataType": "tuple",
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

Coverage example:

```json
{
  "type" : "Coverage",
  "domain" : {
    "type": "Domain",
    "domainType": "Trajectory",
    "axes": {
      "composite": {
        "dataType": "tuple",
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

- A domain with Section domain type MUST have the axes `"composite"` and `"z"`.
- The axis `"composite"` MUST have the data type `"tuple"` and the component identifiers `"t","x","y"`.
- The value ordering of the axis `"composite"` MUST follow the ordering of its `"t"` component as defined in the corresponding reference system.

Domain example:

```json
{
  "type": "Domain",
  "domainType": "Section",
  "axes": {
    "z": { "values": [10,20,30] },
    "composite": {
      "dataType": "tuple",
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

Coverage example:

```json
{
  "type" : "Coverage",
  "domain" : {
    "type": "Domain",
    "domainType": "Section",
    "axes": {
      "z": { "values": [10,20,30] },
      "composite": {
        "dataType": "tuple",
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

Polygons in this domain domain type are defined equally to GeoJSON, except that they can only contain `[x,y]` positions (and not `z` or additional components):
- A LinearRing is an array of 4 or more `[x,y]` arrays where each of `x` and `y` is a coordinate value. The first and last `[x,y]` elements are identical.
- A Polygon is an array of LinearRing arrays. For Polygons with multiple rings, the first MUST be the exterior ring and any others MUST be interior rings or holes.

- A domain with Polygon domain type MUST have the axis `"composite"` which has a single Polygon value.
- The axis `"composite"` MUST have the data type `"polygon"` and the component identifiers `"x","y"`.
- A Polygon domain MAY have the axes `"z"` and `"t"` which both MUST have a single value only.

Domain example:

```json
{
  "type": "Domain",
  "domainType": "Polygon",
  "axes": {
    "composite": {
      "dataType": "polygon",
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

Coverage example:

```json
{
  "type" : "Coverage",
  "domain" : {
    "type": "Domain",
    "domainType": "Polygon",
    "axes": {
      "composite": {
        "dataType": "polygon",
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
      "values" : [...]
    }
  }
}
```

### 2.10. PolygonSeries

- A domain with PolygonSeries domain type MUST have the axes `"composite"` and `"t"` where `"composite"` MUST have a single Polygon value. Polygons are defined in the Polygon domain type.
- A domain with PolygonSeries domain type MAY have the axis `"z"` which MUST have a single value only.
- The axis `"composite"` MUST have the data type `"polygon"` and the component identifiers `"x","y"`.

Domain example:

```json
{
  "type": "Domain",
  "domainType": "PolygonSeries",
  "axes": {
    "composite": {
      "dataType": "polygon",
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

Coverage example:

```json
{
  "type" : "Coverage",
  "domain" : {
    "type": "Domain",
    "domainType": "PolygonSeries",
    "axes": {
      "composite": {
        "dataType": "polygon",
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
      "axisNames": ["t"],
      "shape": [2],
      "values" : [...]
    }
  }
}
```

### 2.11. MultiPolygon

- A domain with MultiPolygon domain type MUST have the axis `"composite"` where the values are Polygons. Polygons are defined in the Polygon domain type.
- The axis `"composite"` MUST have the data type `"polygon"` and the component identifiers `"x","y"`.
- A MultiPolygon domain MAY have the axes `"z"` and `"t"` which both MUST have a single value only.

Domain example:

```json
{
  "type": "Domain",
  "domainType": "MultiPolygon",
  "axes": {
    "composite": {
      "dataType": "polygon",
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

Coverage example:

```json
{
  "type" : "Coverage",
  "domain" : {
    "type": "Domain",
    "domainType": "MultiPolygon",
    "axes": {
      "composite": {
        "dataType": "polygon",
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
      "axisNames": ["composite"],
      "shape": [2],
      "values" : [...]
    }
  }
}
```

### 2.12. MultiPolygonSeries

- A domain with MultiPolygonSeries domain type MUST have the axes `"composite"` and `"t"` where the values of `"composite"` are Polygons. Polygons are defined in the Polygon domain type.
- The axis `"composite"` MUST have the data type `"polygon"` and the component identifiers `"x","y"`.
- A MultiPolygon domain MAY have the axis `"z"` which MUST have a single value only.

Domain example:

```json
{
  "type": "Domain",
  "domainType": "MultiPolygonSeries",
  "axes": {
    "composite": {
      "dataType": "polygon",
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

Coverage example:

```json
{
  "type" : "Coverage",
  "domain" : {
    "type": "Domain",
    "domainType": "MultiPolygonSeries",
    "axes": {
      "composite": {
        "dataType": "polygon",
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
      "axisNames": ["t", "composite"],
      "shape": [3, 2],
      "values" : [...]
    }
  }
}
```

