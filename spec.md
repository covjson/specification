# The CoverageJSON Format Specification

WORK-IN-PROGRESS

<!-- Document Info -->
<table>
  <tr>
    <th>Authors</th>
    <td>
      Maik Riechert (<a href="http://www.reading.ac.uk">University of Reading</a>),
      Jon Blower (<a href="http://www.reading.ac.uk">University of Reading</a>),
      Joan Masó (<a href="http://www.creaf.cat">CREAF, Universitat Autònoma de Barcelona</a>)
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
      CoverageJSON is a geospatial coverage interchange format based on JavaScript
      Object Notation (JSON) and Concise Binary Object Representation (CBOR).
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

## TODO

- domain CRS
- for non-default CRS's it would be good to be able to provide WGS84 `"lon"`/`"lat"` coordinates
- time/vertical extents, ... in domain and/or coverage
- check qudt:quantity

## 1. Introduction

CoverageJSON is a format for encoding a variety of coverages like grids, time series, and vertical profiles, distinguished by the geometry of their spatiotemporal domain. A CoverageJSON object may represent a domain, a range, a coverage, or a collection of coverages. CoverageJSON currently supports the following domain types: Grid, Profile, PointSeries, Point, Trajectory, Section, Polygon, MultiPolygon, and MultiPolygonSeries. A range in CoverageJSON  represents coverage values and supports the common offset/factor encoding. A coverage in CoverageJSON is the combination of a domain, parameters, ranges, and additional metadata. A coverage collection represents a list of coverages.

A complete CoverageJSON data structure is always an object (in JSON terms). In CoverageJSON, an object consists of a collection of name/value pairs -- also called members. For each member, the name is always a string. Member values are either a string, number, object, array or one of the literals: true, false, and null. An array consists of elements where each element is a value as described above.

A CoverageJSON object may be serialized as either JSON or CBOR.

### 1.1. Examples

A CoverageJSON Profile coverage:

(note that `"zCRS"` is not defined exactly yet)

```js
{
  "type" : "ProfileCoverage",
  "id" : "http://.../datasets/1/coverages/123",
  "domain" : {
    "type" : "Profile",
    "x" : -128.381,
    "y" : -40.153,
    "z" : [ 5.4562, 8.9282, 14.8802, 20.8320, 26.7836, 32.7350,
            38.6863, 44.6374, 50.5883, 56.5391, 62.4897, 68.4401,
            74.3903, 80.3404, 86.2902, 92.2400, 98.1895, 104.1389,
            110.0881, 116.0371, 121.9859 ],
    "zCrs" : {
      "unit" : {
        "symbol" : "m"
      },
      "positiveUpwards" : false
    },
    "t" : "2013-01-13T11:12:20Z"
  },
  "parameters" : {
    "PSAL": {
      "id" : "http://.../datasets/1/params/PSAL",
      "type" : "Parameter",
      "description" : "The measured salinity, in practical salinity units (psu) of the sea water ",
      "unit" : {
        "symbol" : "psu"
      },
      "observedProperty" : {
        "id" : "http://foo/sea_water_salinity",
        "label" : {
          "en": "Sea Water Salinity"
        }
      }
    },
    "POTM": {
      "id" : "http://.../datasets/1/params/POTM",
      "type" : "Parameter",
      "description": {
        "en": "The potential temperature, in degrees celcius, of the sea water"
      },
      "unit" : {
        "symbol" : "°C"
      },
      "observedProperty" : {
        "id" : "http://foo/sea_water_potential_temperature",
        "label": {
          "en": "Sea Water Potential Temperature"
        }
      }
    }
  },
  "ranges" : {
    "type": "RangeSet",
    "PSAL" : "http://.../datasets/1/coverages/123/range/PSAL",
    "POTM" : "http://.../datasets/1/coverages/123/range/POTM"
  },
  "@context" : [ "https://rawgit.com/reading-escience-centre/coveragejson/master/contexts/coveragejson-base.jsonld", {
    "qudt" : "http://qudt.org/1.1/schema/qudt#",
    "unit" : "qudt:unit",
    "symbol" : "qudt:symbol",
    "PSAL" : { "@id" : "http://.../datasets/1/params/PSAL", "@type" : "@id" },
    "POTM" : { "@id" : "http://.../datasets/1/params/POTM", "@type" : "@id" }
  } ]
}
```
where `"http://.../datasets/1/coverages/123/range/PSAL"` (similarly `/POTM`) points to the following document, here shown as JSON serialization:
```js
{
  "type" : "Range",
  "values" : [ 43.9599, 43.9599, 43.9640, 43.9640, 43.9679, 43.9879, 44.0040,
               44.0120, 44.0120, 44.0159, 44.0320, 44.0320, 44.0480, 44.0559,
               44.0559, 44.0579, 44.0680, 44.0740, 44.0779, 44.0880, 44.0940 ],
  "validMin" : 30,
  "validMax" : 50
}
```
Range data can also be directly embedded into the main CoverageJSON document, making it stand-alone.

### 1.2. Definitions

- JavaScript Object Notation (JSON), and the terms object, name, value, array, string, number, and null, are defined in [IETF RFC 4627](http://www.ietf.org/rfc/rfc4627.txt).
- JSON-LD is defined in [http://www.w3.org/TR/json-ld/](http://www.w3.org/TR/json-ld/).
- Concise Binary Object Representation (CBOR) is defined in [IETF RFC 7049](http://tools.ietf.org/rfc/rfc7049.txt).
- The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [IETF RFC 2119](http://www.ietf.org/rfc/rfc2119.txt).

## 2. i18n Objects

An i18n object represents a string in multiple languages where each key is a language tag as defined in [BCP 47](http://tools.ietf.org/html/bcp47), and the value the string in that language. At minimum, a translation with the tag `"en"` must be given.

Example:

```js
{
  "en": "Temperature",
  "de": "Temperatur"
}
```

## 3. Parameter Objects

Parameter objects represent metadata about the values of the coverage in terms of the observed property (like water temperature), the units, and others.

- A parameter object may have any number of members (name/value pairs).
- A parameter object must have a member with the name `"type"` and the value `"Parameter"`.
- A parameter object may have a member with the name `"id"` where the value must be a string and should be a common identifier.
- A parameter object may have a member with the name `"description"` where the value must be an i18n object which is a, perhaps lengthy, textual description of the parameter.
- A parameter object must have a member with the name `"observedProperty"` where the value is an object which must have the member `"label"` and which may have the members `"id"` and `"description"`. The value of `"label"` must be an i18n object that is the name of the observed property and which should be short. If given, the value of `"id"` must be a string and should be a common identifier. If given, the value of `"description"` must be an i18n object with a textual description of the observed property.
- A parameter object may have a member with the name `"unit"` where the value is an object which must have either or both the members `"label"` or/and "`symbol`", and which may have the member `"id"`. If given, the value of `"symbol"` must be a string of the symbolic notation of the unit. If given, the value of `"label"` must be an i18n object of the name of the unit and should be short. If given, the value of `"id"` must be a string and should be a common identifier.
- A parameter object may have a member with the name `"categories"` where the value is a non-empty array of category objects. A category object must have a `"value"` or `"values"` member, a `"label"` member, and may have `"description"` and `"id"` members. The value of `"label"` must be an i18n object of the name of the category and should be short. If given, the value of `"value"` must be an integer unique within all category objects in `"categories"`. If given, the value of `"values"` must be an array of `"value"` integers. If given, the value of `"id"` must be a string and should be a common identifier. If given, the value of `"description"` must be an i18n object with a textual description of the category.
- A parameter object must not have both `"unit"` and `"categories"` members.
- A parameter object must not have both `"value"` and `"values"` members.

Example for a continuous-data parameter:
```js
{
  "type" : "Parameter",
  "id": "http://.../mydataset/params/TMP",
  "description" : {
    "en": "The sea water temperature in degrees celsius. more text if needed..."
  },
  "observedProperty" : {
    "id" : "http://foo/sea_water_temperature",
    "label" : {
      "en": "Sea Water Temperature"
    },
    "description" : {
      "en": "longer description..."
    }
  },
  "unit" : {
    "id" : "http://qudt.org/vocab/unit#DegreeCelsius",
    "label" : {
      "en": "degrees Celsius"
    },
    "symbol" : "°C"
  }
}
```

Example for a categorical-data parameter:
```js
{
  "type" : "Parameter",
  "id": "http://.../mydataset/params/LC",
  "description" : {
    "en": "The land cover category."
  },
  "observedProperty" : {
    "id" : "http://foo/land_cover",
    "label" : {
      "en": "Land Cover"
    },
    "description" : {
      "en": "longer description..."
    }
  },
  "categories": [
    {
      "id": "http://.../landcover1/categories/grass",
      "value": 1,
      "label": {
        "en": "Grass"
      },
      "description": {
        "en": "Very green grass."
      }
    }, {
      "id": "http://.../landcover1/categories/forest",
      "values": [2,3],
      "label": {
        "en": "Forest"
      }
    }, ...
  ]
}
```

## 4. CoverageJSON Objects

CoverageJSON always consists of a single object. This object (referred to as the CoverageJSON object below) represents a domain, range, coverage, or collection of coverages.

- The CoverageJSON object may have any number of members (name/value pairs).
- The CoverageJSON object must have a member with the name `"type"`. This member's value is a string that determines the type of the CoverageJSON object.
- The value of the type member must be one of: `"Grid"`, `"Profile"`, `"PointSeries"`, `"Point"`, `"Trajectory"`, `"Section"`, `"MultiPolygonSeries"`, `"MultiPolygon"`, `"Polygon"`, `"GridCoverage"`, `"ProfileCoverage"`, `"PointSeriesCoverage"`, `"PointCoverage"`, `"TrajectoryCoverage"`, `"SectionCoverage"`, `"MultiPolygonSeriesCoverage"`, `"MultiPolygonCoverage"`, `"PolygonCoverage"`, `"Range"`, or `"CoverageCollection"`. The case of the type member values must be as shown here.

### 4.1. Domain Objects

A domain object is a CoverageJSON object which defines a coordinate space and the order of the enumeration of all coordinates in that space.

- The value of the type member must be one of: `"Grid"`, `"Profile"`, `"PointSeries"`, `"Point"`, `"Trajectory"`, `"Section"`, `"MultiPolygonSeries"`, `"MultiPolygon"`, and `"Polygon"`.
- The domain object members named `"x"`, `"y"`, and `"z"`, if existing, shall refer to spatial coordinates.
- The domain object member named `"t"`, if existing, shall refer to temporal coordinates.
- Additional members of a domain object are determined by the type of domain.
- A spatial CRS applies to the members `"x"`, `"y"`, and `"z"` in that axis order and defines the reference system, units, and value type. (**TODO** is this correct?)
- A temporal CRS applies to the member `"t"` and defines the reference system, units, and value type. (**TODO** is this correct?)
- In the context of `"x"`, `"y"`, `"z"`, `"t"`, *coordinate value* refers to a value that has the corresponding CRS value type.
- If a member `"x"`, `"y"`, `"z"`, or `"t"` is an array of coordinate values, then, if not otherwise defined, the values in that array must be ordered monotonically according to their ordering relation defined by the used CRS (**FIXME**). Whenever the terms "monotonically increasing" or "monotonically decreasing" are used, the ordering relation as defined by the corresponding CRS shall be used.
- If not defined, the default spatial CRS is used.
- If not defined, the default temporal CRS is used.
- The default spatial CRS is the geographic CRS CRS84 (http://www.opengis.net/def/crs/OGC/1.3/CRS84), with `"x"` longitude, `"y"` latitude, `"z"` altitude above the WGS84 reference ellipsoid, longitude and latitude units of decimal degrees and altitude units of metres, and all values being numbers.
- The default temporal CRS is coordinated universal time (UTC) with times in `YYYY-MM-DDTHH:mm:ss[.sss]Z`, `YYYY-MM-DDTHH:mm:ss[.sss]+-HH:mm` or  `YYYY-MM-DD` string formats (subsets of ISO 8601).
- A coordinate space is defined by an array `[C1, ..., Cn]` where each of `C1` to `Cn` is an array of coordinates. The number of elements in a coordinate space are `|C1| * ... * |Cn|`. Each element in the space can be referenced by a unique number. A coordinate space assigns a unique number to `[c1, ..., cn]` by assuming an `n`-dimensional array of shape `[|C1|, ..., |Cn|]` in row-major order.
- Each domain type must define its coordinate space in terms of an array `[C1, ..., Cn]` where each of `C1` to `Cn` is an array of coordinates.

TODO CRS84 is 2D only! either we have separate horizontal CRS = CRS84 and vertical CRS = ??? or we create a 3D version of CRS84, in analogy to [EPSG:4979](http://www.epsg-registry.org/report.htm?type=selection&entity=urn:ogc:def:crs:EPSG::4979&reportDetail=long&title=WGS%2084&style=urn:uuid:report-style:default-with-code&style_name=OGP%20Default%20With%20Code). Check http://www.ogcnetwork.net/node/491 on how to create such a CRS with an axes swapping conversion

Overview:

Domain               | x | y | z | t | sequence | polygon (xy)
---------------------|---|---|---|---|----------|-------------
Grid                 |[M]|[M]|[O]|[O] 
Profile              | M | M |[M]| O
PointSeries          | M | M | O |[M]
Point                | M | M | O | O
PolygonSeries        |   |   | O |[M]|          |  M
Polygon              |   |   | O | O |          |  M
MultiPolygonSeries   |   |   | O |[M]|          | [M]
MultiPolygon         |   |   | O | O |          | [M]
Trajectory           |   |   | O |   | \[M\] (txy[z])
Section              |   |   |[M]|   | \[M\] (txy)


M = Mandatory, single coordinate in coordinate space

O = Optional, single coordinate in coordinate space

[M] = Mandatory, array of coordinates in coordinate space

[O] = Optional, array of coordinates in coordinate space


#### 4.1.1. Grid

- A Grid domain object must have the members `"x"` and `"y"` where the value of each is a non-empty array coordinate values.
- A Grid domain object may have the member `"z"` where the value is a non-empty array coordinate values.
- A Grid domain object may have the member `"t"` where the value is a non-empty array coordinate values.
- The coordinate space of a Grid domain object is defined by `[t,z,y,x]`, or `[t,y,x]`, or `[z,y,x]`, or `[y,x]`, depending on which members are defined.

Example:
```js
{
  "type": "Grid",
  "x": [1,2,3],
  "y": [20,21],
  "z": [1],
  "t": ["2008-01-01T04:00:00Z"]
}
```

#### 4.1.2. Profile

- A Profile domain object must have the members `"x"` and `"y"` where each has a coordinate value.
- A Profile domain object must have the member `"z"` where the value is a non-empty array of coordinate values.
- A Profile domain object may have the member `"t"` where the value is a coordinate value.
- The coordinate space of a Profile domain object is defined by `[[t],z,[y],[x]]` or `[z,[y],[x]]`, depending on whether `"t"` is defined.

Example:
```js
{
  "type": "Profile",
  "x": 1,
  "y": 21,
  "z": [1,5,20],
  "t": "2008-01-01T04:00:00Z"
}
```

#### 4.1.3. PointSeries

- A PointSeries domain object must have the members `"x"` and `"y"` where each has a coordinate value.
- A PointSeries domain object may have the member `"z"` where the value is a coordinate value.
- A PointSeries domain object must have the member `"t"` where the value is a non-empty array of monotonically increasing coordinate values.
- The coordinate space of a PointSeries domain object is defined by `[t,[z],[y],[x]]` or `[t,[y],[x]]`, depending on whether `"z"` is defined.

Example:
```js
{
  "type": "PointSeries",
  "x": 1,
  "y": 20,
  "z": 1,
  "t": ["2008-01-01T04:00:00Z","2008-01-01T05:00:00Z"]
}
```

#### 4.1.4. Point

- A Point domain object must have the members `"x"` and `"y"` where each has a coordinate value.
- A Point domain object may have the member `"z"` where the value is a coordinate value.
- A Point domain object may have the member `"t"` where the value is a coordinate value.
- The coordinate space of a Point domain object is defined by `[[t],[z],[y],[x]]` or `[[t],[y],[x]]`, or `[[z],[y],[x]]` or `[[y],[x]]`, depending on which members are defined.

Example:
```js
{
  "type": "Point",
  "x": 1,
  "y": 20,
  "z": 1,
  "t": "2008-01-01T04:00:00Z"
}
```

#### 4.1.5. Trajectory

- A Trajectory domain object must have the members `"x"` and `"y"` where the value of each is a non-empty array of coordinate values and both arrays must have the same length `n`. The elements of the arrays of `"x"` and `"y"` may but do not have to be ordered monotonically.
- A Trajectory domain object must have the member `"t"` where the value is a non-empty array of monotonically increasing coordinate values of length `n`.
- A Trajectory domain object may have the member `"z"` where the value is either a non-empty array of coordinate values of length `n`, or a coordinate value. If `"z"` is an array, its elements may but do not have to be ordered monotonically.
- A Trajectory domain object must have the member `"sequence"` where the value is either `["x","y","z","t"]` if `"z"` is an array, or `["x","y","t"]` if `"z"` is a coordinate value or not defined.
- The coordinate space of a Trajectory domain object is defined by `[zip(x,y,z,t)]` if `"z"` is an array, or `[zip(x,y,t)]` if `"z"` is not defined, or `[[z],zip(x,y,t)]` if `"z"` is a coordinate value. `zip` is a function which returns an array of arrays, where the i-th array contains the i-th element from each of the argument arrays in the given order.

**TODO** if t is not optional here, why should it be optional for other domain types?

Example:
```js
{
  "type": "Trajectory",
  "x": [1,2],
  "y": [20,21],
  "z": [1,3],
  "t": ["2008-01-01T04:00:00Z","2008-01-01T04:30:00Z"]
  "sequence": ["x","y","z","t"]
}
```

Example without z:
```js
{
  "type": "Trajectory",
  "x": [1,2],
  "y": [20,21],
  "t": ["2008-01-01T04:00:00Z","2008-01-01T04:30:00Z"]
  "sequence": ["x","y","t"]
}
```

Example with z defined as constant value:
```js
{
  "type": "Trajectory",
  "x": [1,2],
  "y": [20,21],
  "t": ["2008-01-01T04:00:00Z","2008-01-01T04:30:00Z"],
  "sequence": ["x","y","t"],
  "z": 5
}
```

#### 4.1.6. Section

- A Section domain object must have the members `"x"` and `"y"` where the value of each is a non-empty array of coordinate values and both arrays must have the same length `n`. The elements of the arrays of `"x"` and `"y"` may but do not have to be ordered monotonically.
- A Section domain object must have the member `"t"` where the value is a non-empty array of monotonically increasing coordinate values of length `n`.
- A Section domain object must have the member `"z"` where the value is a non-empty array of coordinate values.
- A Section domain object must have the member `"sequence"` where the value is `["x","y","t"]`.
- The coordinate space of a Section domain object is defined by `[z,zip(x,y,t)]`.

Example:
```js
{
  "type": "Section",
  "x": [1,2],
  "y": [20,21],
  "t": ["2008-01-01T04:00:00Z","2008-01-01T04:30:00Z"]
  "sequence": ["x","y","t"]
  "z": [10,20,30]
}
```

#### 4.1.7. Polygon

Polygons are defined equally to GeoJSON, except that they can only contain `[x,y]` positions (and not `z` or additional dimensions).

- A LinearRing is an array of 4 or more `[x,y]` arrays where each of `x` and `y` is a coordinate value. The first and last `[x,y]` elements are identical.
- A Polygon is an array of LinearRing arrays. For Polygons with multiple rings, the first must be the exterior ring and any others must be interior rings or holes.
- A Polygon domain object must have the member `"polygon"` where the value is a Polygon.
- A Polygon domain object may have the member `"t"` where the value is a coordinate value.
- A Polygon domain object may have the member `"z"` where the value is a coordinate value.
- The coordinate space of a Polygon domain object is defined by `[[t],[z],[polygon]]`, `[[t],[polygon]]`, `[[z],[polygon]]`, or `[[polygon]]`, depending on which members are defined.
- Note that the polygon represents a single coordinate within the coordinate space.

Example:
```js
{
  "type": "Polygon",
  "polygon": [
      [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]
      ],
  "z": 2,
  "t": "2008-01-01T04:00:00Z"
}
```

#### 4.1.8. PolygonSeries

- A PolygonSeries domain object must have the member `"polygon"` where the value is a Polygon.
- A PolygonSeries domain object must have the member `"t"` where the value is a non-empty array of monotonically increasing coordinate values.
- A PolygonSeries domain object may have the member `"z"` where the value is a coordinate value.
- The coordinate space of a PolygonSeries domain object is defined by `[t,[z],[polygon]]` or `[t,[polygon]]` depending on whether `"z"` is defined.

Example:
```js
{
  "type": "PolygonSeries",
  "polygon": [
      [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]
      ],
  "z": 2,
  "t": ["2008-01-01T04:00:00Z","2008-01-01T05:00:00Z"]
}
```

#### 4.1.9. MultiPolygon

- A MultiPolygon domain object must have the member `"polygon"` where the value is an array of Polygons.
- A MultiPolygon domain object may have the member `"t"` where the value is a coordinate value.
- A MultiPolygon domain object may have the member `"z"` where the value is a coordinate value.
- The coordinate space of a MultiPolygon domain object is defined by `[[t],[z],polygon]`, `[[t],polygon]`, `[[z],polygon]`, or `[polygon]`, depending on which members are defined.

Example:
```js
{
  "type": "MultiPolygon",
  "polygon": [
      [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ] ],
      [ [ [200.0, 10.0], [201.0, 10.0], [201.0, 11.0], [200.0, 11.0], [200.0, 10.0] ] ]
    ],
  "z": 2,
  "t": "2008-01-01T04:00:00Z"
}
```

#### 4.1.10. MultiPolygonSeries

- A MultiPolygonSeries domain object must have the member `"polygon"` where the value is an array of Polygons.
- A MultiPolygonSeries domain object must have the member `"t"` where the value is a non-empty array of monotonically increasing coordinate values.
- A MultiPolygonSeries domain object may have the member `"z"` where the value is a coordinate value.
- The coordinate space of a MultiPolygonSeries domain object is defined by `[t,[z],polygon]` or `[t,polygon]`, depending on whether the `"z"` member is defined.

Example:
```js
{
  "type": "MultiPolygonSeries",
  "polygon": [
      [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ] ],
      [ [ [200.0, 10.0], [201.0, 10.0], [201.0, 11.0], [200.0, 11.0], [200.0, 10.0] ] ]
    ],
  "z": 2,
  "t": ["2008-01-01T00:00:00Z", "2010-01-01T00:00:00Z"]
}
```

#### 4.1.11. Coordinate Bounds

- For each of the `"x"`, `"y"`, `"z"`, and `"t"` members (further called point members) of a domain object, coordinate bounds may be defined in the members `"xBounds"`, `"yBounds"`, `"zBounds"`, and `"tBounds"`, respectively, where the value of each is an array of monotonically ordered coordinate values of length `len*2` with `len` being the array length of the point member for which the bounds are given, or 1 if the point member is not an array. For a point member array, a lower and upper bounding coordinate value at positions `2*i` and `2*i+1`, respectively, are given in a bounds array for each point member coordinate value with array index `i`. For a point member which is not an array, the bounds array contains a single pair of lower and upper bounding coordinate values as first and second value, respectively. The lower and upper bounding coordinate values `l` and `u` for a given coordinate `c` must satisfy the constraint `l <= c <= u`.
- If a domain object member `"x"`, `"y"`, `"z"`, or `"t"` has no corresponding bounds member and is an array, then its bounds may be derived with the formula `bounds(m, i) = [(m[i-1] + m[i]) / 2, (m[i] + m[i+1]) / 2]` where `m` is the value of one of `"x"`, `"y"`, `"z"`, or `"t"`, and `i` is the index within the `m` array.

### 4.2. Range Objects

A CoverageJSON object with the type `"Range"` is a range object.

- A range object must have a member with the name `"values"` where the value is an array of numbers and nulls where nulls represent missing data.
- A range object may have both or none of the `"validMin"` and `"validMax"` members where the value of each is a number. The value of `"validMin"` must be equal to or smaller than the minimum value in the `"values"` array, ignoring null. The value of `"validMax"` must be equal to or greater than the maximum value in the `"values"` array, ignoring null. `"validMin"` and `"validMax"` may be used by clients as an initial legend extent and should therefore not be too much smaller or greater than the actual extent of all values.
- If the `"values"` array of a range object does not contain nulls, then for CBOR serializations typed arrays (as in RDFxxxx) should be used for increased space and parsing efficiency.
- Note that common JSON implementations may use 64-bit floating point numbers as data type for `"values"`, therefore precision has to be taken into account. For example, only integers within the extent [-2^32, 2^32] can be accurately represented with 64-bit floating point numbers.

#### 4.2.1. Offset/Factor Encoding (CBOR-only)

A simple compression scheme typically used for storing low-resolution floating point data as small integers in binary formats is the offset/factor encoding. When using CBOR as serialization format, this encoding scheme may be used for the `"values"` array as described below.

- A range object may have both or none of the `"offset"` and `"factor"` members where the value of each is a number.
- If both `"offset"` and `"factor"` are present in a range object, each non-null value `v` in `"values"` must be converted to `v * factor + offset` when accessing it and all values in the `"values"` array must be integers or nulls. The converted value is always a floating point number and therefore this mechanism shall not be used for values that shall result in integers.

#### 4.2.2. Missing Value Encoding (CBOR-only)

If only a small amount of values in `"values"` are missing it is more space efficient to encode these missing values using a number outside the valid value extent (instead of null) so that CBOR's typed array representation for `"values"` can be applied.

- If a range object contains the `"validMin"` and `"validMax"` members it may have a member `"missing"` with value `"nonvalid"`.
- If a range object has the member `"missing"` with value `"nonvalid"`, then all missing values in `"values"` must be encoded as a number outside the `"validMin"`/`"validMax"` extent and interpreted as missing values.

### 4.3. Coverage Objects

A CoverageJSON object with the type `"<DomainType>Coverage"` is a coverage object, where `<DomainType>` is any of the domain types defined earlier.

- If a coverage has a commonly used identifier, that identifier should be included as a member of the coverage object with the name `"id"`.
- A coverage object must have a member with the name `"domain"` where the value is either a domain object or a URL. The domain type must match the `<DomainType>` part of the coverage type.
- A coverage object may have a member with the name `"parameters"` where the value is an object where each member has as name a short identifier and as value a parameter object. The identifier corresponds to the commonly known concept of "variable name" and is merely used in clients for conveniently accessing the corresponding range object.
- A coverage object must have a `"parameters"` member if the coverage object is not part of a coverage collection or if the coverage collection does not have a `"parameters"` member.
- A coverage object must have a member with the name `"ranges"` where the value is a range set object. A range set object must have a member with the name `"type"` and the value `"RangeSet"`. Any member of a range set object except `"type"` has as name any of the names in a `"parameters"` object in scope and as value either a range object or a URL. A `"parameters"` member in scope is either within the enclosing coverage object or, if part of a coverage collection, in the parent coverage collection object. The array elements of the `"values"` member of each range object must correspond to the coordinate space defined by `"domain"` in terms of element order and count. If the referenced parameter object has a `"categories"` member, then each array element of the `"values"` member must be equal to one of the values defined in the `"value"` member of the category objects within `"categories"` and be interpreted as the matching category.
- A coverage object may have a member with the name `"bbox"` where the value is an array of four numbers `[west longitude, south latitude, east longitude, north latitude]` describing the bounding box of the coverage data in degrees (WGS84).

### 4.4. Coverage Collection Objects

A CoverageJSON object with the type `"CoverageCollection"` is a coverage collection object.

- A coverage collection object must have a member with the name `"coverages"`. The value corresponding to `"coverages"` is an array. Each element in the array is a coverage object as defined above.
- A coverage collection object may have a member with the name `"parameters"` where the value is a list of parameter objects.

## 5. Linked Data Context

A linked data context in terms of JSON-LD should be established by including a `"@context"` element in the root of a CoverageJSON object which refers to the base CoverageJSON context `"https://rawgit.com/reading-escience-centre/coveragejson/master/contexts/coveragejson-base.jsonld"` and any other necessary local contexts. The base CoverageJSON context uses common vocabularies like RDF Schema, DCMI Metadata Terms, and W3C Time. Range objects should *not* have a context because range data is currently not suitable to be handled as linked data.

Example:
```js
{
  "@context": [
     "https://rawgit.com/reading-escience-centre/coveragejson/master/contexts/coveragejson-base.jsonld",
     {
      "qudt" : "http://qudt.org/1.1/schema/qudt#",
      "unit" : "qudt:unit",
      "symbol" : "qudt:symbol",
      "PSAL" : { "@id" : "http://.../datasets/1/params/PSAL", "@type" : "@id" },
      "POTM" : { "@id" : "http://.../datasets/1/params/POTM", "@type" : "@id" }
     }
   ],
   "type": "GridCoverage",
   ...
}
```

All identifiers referred to in a CoverageJSON object should be URIs. These URIs should be taken from established vocabularies if available.

TODO expand

## 6. Resolving domain and range URLs

When a domain or range is referenced by a URL in a CoverageJSON document, then the client should, whenever is appropriate, load the data from the given URL and treat the loaded data as if it was directly embedded in place of the URL. When sending HTTP requests, the `Accept` header should be set appropriately to the supported CoverageJSON media types.

## 7. Media Type, File Extensions, and Encodings

The CoverageJSON media type shall be `application/prs.coverage+json` when encoded in JSON, and `application/prs.coverage+cbor` when encoded in CBOR. Both media types have an optional parameter `profile` which is a non-empty list of space-separated URIs identifying specific constraints or conventions that apply to a CoverageJSON document according to [RFC6906](http://www.ietf.org/rfc/rfc6906.txt). The only profile URI defined in this document is `http://coveragejson.org/profiles/standalone` which asserts that all domain and range objects are directly embedded in a CoverageJSON document and not referenced by URLs.

The file extension for JSON shall be `covjson`, and for CBOR `covcbor`.

## Appendix A. Coverage Examples

## Attribution

This specification uses ideas and sentence structures from the [GeoJSON specification](http://geojson.org/geojson-spec.html) which is licensed under a [Creative Commons Attribution 3.0 United States License](http://creativecommons.org/licenses/by/3.0/us/).
