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

## 1. Introduction

CoverageJSON is a format for encoding a variety of coverages like grids, time series, and vertical profiles, distinguished by the geometry of their spatiotemporal domain. A CoverageJSON object may represent a domain, a range, a coverage, or a collection of coverages. CoverageJSON currently supports the following domain types: Grid, Profile, Trajectory, PointSeries, and Polygon. A range in CoverageJSON  represents coverage values and supports the common offset/factor encoding. A coverage in CoverageJSON is the combination of a domain, parameters, ranges, and additional metadata. A coverage collection represents a list of coverages.

A complete CoverageJSON data structure is always an object (in JSON terms). In CoverageJSON, an object consists of a collection of name/value pairs -- also called members. For each member, the name is always a string. Member values are either a string, number, object, array or one of the literals: true, false, and null. An array consists of elements where each element is a value as described above.

A CoverageJSON object may be serialized as either JSON or CBOR.

### 1.1. Examples

A CoverageJSON coverage collection:

```js
TODO
```

### 1.2. Definitions

- JavaScript Object Notation (JSON), and the terms object, name, value, array, string, and number, are defined in [IETF RFC 4627](http://www.ietf.org/rfc/rfc4627.txt).
- JSON-LD is defined in [http://www.w3.org/TR/json-ld/](http://www.w3.org/TR/json-ld/).
- Concise Binary Object Representation (CBOR) is defined in [IETF RFC 7049](http://tools.ietf.org/rfc/rfc7049.txt).
- The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [IETF RFC 2119](http://www.ietf.org/rfc/rfc2119.txt).

## TODO

- domain coordinate bounds
- domain CRS
- for non-default CRS's it would be good to be able to provide WGS84 `"lon"`/`"lat"` coordinates
- be careful with wording when saying that multiple members "may" exist: do both have to exist or each individually?
- handle CBOR typed arrays within range, data type / precision issues, unpacking etc.
- check qudt:quantity
- levels of measurement, categories etc.
- define profiles (stand-alone, with URLs)

## 2. Parameter Objects

Parameter objects represent metadata about the values of the coverage in terms of the observed property (like water temperature), the units, and others.

- A parameter object must have a member with the name `"type"` and the value `"Parameter"`.
- A parameter object must have a member with the name `"id"` where the value must be a unique identifier within the scope of the CoverageJSON object. TODO forward ref, unclean
- A parameter object may have a member with the name `"description"` where the value is a, perhaps lengthy, textual description of the parameter.
- A parameter object must have a member with the name `"observedProperty"` where the value is an object which must have the member `"label"` and which may have the members `"id"` and `"description"`. The value of `"label"` must be a string which should be short. If given, the value of `"id"` should be a common identifier. If given, the value of `"description"` must be a textual description of the property.
- A parameter object may have a member with the name `"unit"` where the value is an object which must have either or both the members `"label"` or/and "`symbol`", and which may have the member `"id"`. If given, the value of `"symbol"` must be the symbolic notation of the unit. If given, the value of `"label"` must be a short name of the unit. If given, the value of `"id"` should be a common identifier.

Example:
```js
{
  "type" : "Parameter",
  "id" : "TEMP",
  "description" : "The sea water temperature in degrees celsius.",
  "observedProperty" : {
    "id" : "http://foo/sea_water_temperature",
    "label" : "Sea Water Temperature"
  },
  "unit" : {
    "id" : "http://qudt.org/vocab/unit#DegreeCelsius",
    "label" : "degrees Celsius",
    "symbol" : "°C"
  }
}
```

## 3. CoverageJSON Objects

CoverageJSON always consists of a single object. This object (referred to as the CoverageJSON object below) represents a domain, range, coverage, or collection of coverages.

- The CoverageJSON object may have any number of members (name/value pairs).
- The CoverageJSON object must have a member with the name `"type"`. This member's value is a string that determines the type of the CoverageJSON object.
- The value of the type member must be one of: `"Grid"`, `"Profile"`, `"Trajectory"`, `"PointSeries"`, `"Polygon"`, `"Range"`, `"Coverage"`, or `"CoverageCollection"`. The case of the type member values must be as shown here.

### 3.1. Domain Objects

A domain object is a CoverageJSON object which defines a coordinate space and the order of the enumeration of all coordinates in that space.

- The value of the type member must be one of: `"Grid"`, `"Profile"`, `"Trajectory"`, `"PointSeries"`, and `"Polygon"`. (**TODO** add more types)
- The domain object members named `"x"`, `"y"`, and `"z"`, if existing, shall refer to spatial coordinates.
- The domain object member named `"t"`, if existing, shall refer to temporal coordinates.
- Additional members of a domain object are determined by the type of domain.
- A spatial CRS applies to the members `"x"`, `"y"`, and `"z"` in that axis order and defines the reference system, units, and value type. (**TODO** is this correct?)
- A temporal CRS applies to the member `"t"` and defines the reference system, units, and value type. (**TODO** is this correct?)
- In the context of `"x"`, `"y"`, `"z"`, `"t"`, *coordinate value* refers to a value that has the corresponding CRS value type.
- If not defined, the default spatial CRS is used.
- If not defined, the default temporal CRS is used.
- The default spatial CRS is the geographic CRS CRS84 (http://www.opengis.net/def/crs/OGC/1.3/CRS84), with `"x"` longitude, `"y"` latitude, `"z"` altitude above the WGS84 reference ellipsoid, longitude and latitude units of decimal degrees and altitude units of metres, and all values being numbers.
- The default temporal CRS is coordinated universal time (UTC) with times in `YYYY-MM-DDTHH:mm:ss[.sss]Z`, `YYYY-MM-DDTHH:mm:ss[.sss]+-HH:mm` or  `YYYY-MM-DD` string formats (subsets of ISO 8601).
- A coordinate space is defined by an array `[C1, ..., Cn]` where each of `C1` to `Cn` is an array of coordinates. The number of elements in a coordinate space are `|C1| * ... * |Cn|`. Each element in the space can be referenced by a unique number. A coordinate space assigns a unique number to `[c1,...,cn]` by assuming an `n`-dimensional array of shape `[|C1|,...,|Cn|]` in row-major order.
- Each domain type must define its coordinate space in terms of an array `[C1,...,Cn]` where each of `C1` to `Cn` is an array of coordinates.

TODO CRS84 is 2D only! either we have separate horizontal CRS = CRS84 and vertical CRS = ??? or we create a 3D version of CRS84, in analogy to [EPSG:4979](http://www.epsg-registry.org/report.htm?type=selection&entity=urn:ogc:def:crs:EPSG::4979&reportDetail=long&title=WGS%2084&style=urn:uuid:report-style:default-with-code&style_name=OGP%20Default%20With%20Code). Check http://www.ogcnetwork.net/node/491 on how to create such a CRS with an axes swapping conversion

Overview:

Domain               | x | y | z | t | sequence | polygon (xy)
---------------------|---|---|---|---|----------|-------------
Grid                 |[M]|[M]|[O]|[O] 
Profile              | M | M |[M]| O
PointSeries          | M | M | O |[M]
Point                | M | M | O | O
PolygonProfileSeries |   |   |[M]|[M]|          | M
PolygonProfile       |   |   |[M]| O |          | M
PolygonSeries        |   |   | O |[M]|          | M
Polygon              |   |   | O | O |          | M
Trajectory           |   |   | O |   | \[M\] (txy[z])
Section              |   |   |[M]|   | \[M\] (txy)
MultiPointSeries     |   |   | O |[M]| \[M\] (xy[z])


M = Mandatory, single coordinate in coordinate space

O = Optional, single coordinate in coordinate space

[M] = Mandatory, array of coordinates in coordinate space

[O] = Optional, array of coordinates in coordinate space


#### 3.1.1. Grid

- A Grid domain object must have the members `"x"` and `"y"` where the value of each is a non-empty array of coordinate values.
- A Grid domain object may have the member `"z"` where the value is a non-empty array of coordinate values.
- A Grid domain object may have the member `"t"` where the value is a non-empty array of coordinate values.
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

#### 3.1.2. Profile

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

#### 3.1.3. PointSeries

- A PointSeries domain object must have the members `"x"` and `"y"` where each has a coordinate value.
- A PointSeries domain object may have the member `"z"` where the value is a coordinate value.
- A PointSeries domain object must have the member `"t"` where the value is a non-empty array of coordinate values.
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

#### 3.1.4. Point

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

#### 3.1.5. Trajectory

- A Trajectory domain object must have the member `"sequence"` where the value is an array and each element is an array `[t,x,y,z]` or `[t,x,y]` where each of `t`, `x`, `y`, and `z` is a coordinate value.
- A Trajectory domain object may have the member `"z"` if the `"sequence"` member does not include a z-component. The value of `"z"` is a coordinate value.
- The coordinate space of a Trajectory domain object is defined by `[sequence]`, or `[[z],sequence]`, depending on whether `"z"` is defined.

**TODO** if t is not optional here, why should it be optional for others?

Example:
```js
{
  "type": "Trajectory",
  "sequence": [
    ["2008-01-01T04:00:00Z", 1, 20, 1],
    ["2008-01-01T04:30:00Z", 2, 22, 4]
   ]
}
```

Example without z:
```js
{
  "type": "Trajectory",
  "sequence": [
    ["2008-01-01T04:00:00Z", 1, 20],
    ["2008-01-01T04:30:00Z", 2, 22]
   ]
}
```

Example with z defined separately as constant value:
```js
{
  "type": "Trajectory",
  "sequence": [
    ["2008-01-01T04:00:00Z", 1, 20],
    ["2008-01-01T04:30:00Z", 2, 22]
   ],
  "z": 5
}
```

#### 3.1.6. Section

- A Section domain object must have the member `"sequence"` where the value is an array and each element is an array `[t,x,y]` where each of `t`, `x`, and `y` is a coordinate value.
- A Section domain object must have the member `"z"` where the value is a non-empty array of coordinate values.
- The coordinate space of a Section domain object is defined by `[z,sequence]`.

Example:
```js
{
  "type": "Section",
  "sequence": [
    ["2008-01-01T04:00:00Z", 1, 20],
    ["2008-01-01T04:30:00Z", 2, 22]
   ],
  "z": [10,20,30]
}
```

#### 3.1.3. MultiPointSeries

- A MultiPointSeries domain object must have the member `"sequence"` where the value is an array and each element is an array `[x,y,z]` or `[x,y]` where each of `x`, `y`, and `z` is a coordinate value.
- A MultiPointSeries domain object may have the member `"z"` if the `"sequence"` member does not include a z-component. The value of `"z"` is a coordinate value.
- A MultiPointSeries domain object must have the member `"t"` where the value is a non-empty array of coordinate values.
- The coordinate space of a MultiPointSeries domain object is defined by `[t,sequence]` or `[t,[z],sequence]`, depending on whether `"z"` is defined.

Example:
```js
{
  "type": "MultiPointSeries",
  "sequence": [
    [1, 20, 2],
    [2, 22, 5]
   ],
  "t": ["2008-01-01T04:00:00Z","2008-01-01T05:00:00Z"]
}
```

Example without `z`:
```js
{
  "type": "MultiPointSeries",
  "sequence": [
    [1, 20],
    [2, 22]
   ],
  "t": ["2008-01-01T04:00:00Z","2008-01-01T05:00:00Z"]
}
```


Example with `z` defined separately as constant value:
```js
{
  "type": "MultiPointSeries",
  "sequence": [
    [1, 20],
    [2, 22]
   ],
  "z": 5,
  "t": ["2008-01-01T04:00:00Z","2008-01-01T05:00:00Z"]
}
```

#### 3.1.7. Polygon

Polygons are defined equally to GeoJSON, except that they can only contain `[x,y]` positions (and not `z` or additional dimensions).

- A LinearRing is an array of 4 or more `[x,y]` arrays where each of `x` and `y` is a coordinate value. The first and last `[x,y]` elements are identical.
- A Polygon is an array of LinearRing arrays. For Polygons with multiple rings, the first must be the exterior ring and any others must be interior rings or holes.
- A Polygon domain object must have the member `"polygon"` where the value is a Polygon.
- A Polygon domain object may have the members `"t"` and `"z"` where the value of each is a coordinate value.
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

#### 3.1.8. PolygonProfile

- A PolygonProfile domain object must have the member `"polygon"` where the value is a Polygon.
- A PolygonProfile domain object must have the member `"z"` where the value is a non-empty array of coordinate values.
- A PolygonProfile domain object may have the member `"t"` where the value is a coordinate value.
- The coordinate space of a PolygonProfile domain object is defined by `[[t],z,[polygon]]` or `[z,[polygon]]`, depending on whether `"t"` is defined.

Example:
```js
{
  "type": "PolygonProfile",
  "polygon": [
      [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]
      ],
  "z": [0,-10,-20],
  "t": "2008-01-01T04:00:00Z"
}
```

#### 3.1.9. PolygonSeries

- A PolygonSeries domain object must have the member `"polygon"` where the value is a Polygon.
- A PolygonSeries domain object must have the member `"t"` where the value is a non-empty array of coordinate values.
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

#### 3.1.10. PolygonProfileSeries

- A PolygonProfileSeries domain object must have the member `"polygon"` where the value is a Polygon.
- A PolygonProfileSeries domain object must have the members `"t"` and `"z"` where the value of each is a non-empty array of coordinate values.
- The coordinate space of a PolygonProfileSeries domain object is defined by `[t,z,[polygon]]`.

Example:
```js
{
  "type": "PolygonProfileSeries",
  "polygon": [
      [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]
      ],
  "z": [0,-10,-20],
  "t": ["2008-01-01T04:00:00Z","2008-01-01T05:00:00Z"]
}
```

### 3.2. Range Objects

A CoverageJSON object with the type `"Range"` is a range object.

- A range object must have a member with the name `"values"` where the value is an array of numbers and nulls.
- A range object may have two members with the names `"offset"` and `"factor"` where the value of each is a number. If those two members are present, a value `v` must be converted to `v * factor + offset` when accessing it and all values in the `"values"` array must be integers. The converted value is always a floating point number and therefore this mechanism shall not be used for values that shall result in integers.
- If a range object contains the `"offset"` and `"factor"` members it may have a member with the name `"missing"` where the value is an integer. When converting a value `v` which is equal to the value in `"missing"` then this value should be considered as missing.
- A range object may have two members with the names `"min"` and `"max"` where the value of each is a number. The value of `"min"` must be equal to the minimum value in the `"values"` array, ignoring null. The value of `"max"` must be equal to the maximum value in the `"values"` array, ignoring null.
- Note that common JSON implementations will use 64-bit floats as data type for `"values"`, therefore precision has to be taken into account both for range objects with and without `"offset"` and `"factor"` members. For example, only integers within the extent [-2^32, 2^32] can be accurately represented with 64-bit floats.

**TODO** does offset/factor even make sense for JSON?? you probably need 16 bit integers anyway and encoding them in JSON is probably not much more compact then their floating point representation; possibly define that for CBOR only

### 3.3. Coverage Objects

A CoverageJSON object with the type `"Coverage"` is a coverage object.

- If a coverage has a commonly used identifier, that identifier should be included as a member of the coverage object with the name `"id"`.
- A coverage object must have a member with the name `"domain"` where the value is either a domain object or a URL.
- A coverage object may have a member with the name `"parameters"` where the value is a list of parameter objects.
- A coverage object must have a `"parameters"` member if the coverage object is not part of a coverage collection or if the coverage collection does not have a `"parameters"` member.
- A coverage object must have a member with the name `"ranges"` where the value is a range set object. A range set object must have a member with the name `"type"` and the value `"RangeSet"`. Any member of a range set object except `"type"` has as name a parameter ID and as value either a range object or a URL. The elements of the `"values"` member of each range object must correspond to the coordinate space defined by `"domain"` in terms of element order and count.

### 3.4. Coverage Collection Objects

A CoverageJSON object with the type `"CoverageCollection"` is a coverage collection object.

- A coverage collection object must have a member with the name `"coverages"`. The value corresponding to `"coverages"` is an array. Each element in the array is a coverage object as defined above.
- A coverage collection object may have a member with the name `"parameters"` where the value is a list of parameter objects.

## 4. Linked Data Context

A linked data context in terms of JSON-LD should be established by including a `"@context"` element in the root of a CoverageJSON object with the recommended value `"http://.../contexts/coveragejson.jsonld"` which uses common vocabularies like RDF Schema, DCMI Metadata Terms, and W3C Time.

All identifiers referred to in a CoverageJSON object should be URIs from established vocabularies if available.

TODO expand

## 5. Resolving domain and range URLs

When a domain or range is referenced by a URL, then the client should, whenever is appropriate, load the data at the given URL and replace the URL with the loaded object. When sending HTTP requests, the `Accept` header should be set appropriately to the supported CoverageJSON media types.

## 6. Media Type, File Extensions, and Encodings

The CoverageJSON media type shall be `application/prs.coverage+json` when encoded in JSON, and `application/prs.coverage+cbor` when encoded in CBOR.

The file extension for JSON shall be `covjson`, and for CBOR `covcbor`.

## Appendix A. Coverage Examples

## Attribution

This specification uses ideas and sentence structures from the [GeoJSON specification](http://geojson.org/geojson-spec.html) which is licensed under a [Creative Commons Attribution 3.0 United States License](http://creativecommons.org/licenses/by/3.0/us/).
