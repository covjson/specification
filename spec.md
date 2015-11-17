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
    "axes": {
      "x": { "values": [-128.381] },
      "y": { "values": [-40.153] },
      "z": { "values": [
              5.4562, 8.9282, 14.8802, 20.8320, 26.7836, 32.7350,
              38.6863, 44.6374, 50.5883, 56.5391, 62.4897, 68.4401,
              74.3903, 80.3404, 86.2902, 92.2400, 98.1895, 104.1389,
              110.0881, 116.0371, 121.9859] },
      "t": { "values": ["2013-01-13T11:12:20Z"] }
    },
    "referencing": [{
      "identifiers": ["x","y"],
      "srs": {
        "type": "GeodeticCRS",
        "id": "http://www.opengis.net/def/crs/OGC/1.3/CRS84"        
      }
    }, {
      "identifiers": ["z"],
      "srs": {
        "type": "VerticalCRS",
        "datum": {
          "TBD": "TBD"
        },
        "cs": {
          "type": "VerticalCS",
          "axes": [{
            "label": {
              "en": "Depth in meters"
            },
            "direction": {
              "id": "http://www.opengis.net/def/axisDirection/OGC/1.0/down",
              "label": {
                "en": "down"
              }
            },
            "unit": {
              "id": "http://www.opengis.net/def/uom/OGC/1.0/metre",
              "symbol": "m"
            }
          }]
        }
      }
    }, {
      "identifiers": ["t"],
      "trs": {
        "type": "TemporalRS",
        "calendar": "Gregorian"
      }
    }]
  },
  "parameters" : {
    "PSAL": {
      "type" : "Parameter",
      "description" : "The measured salinity, in practical salinity units (psu) of the sea water ",
      "unit" : {
        "symbol" : "psu"
      },
      "observedProperty" : {
        "id" : "http://vocab.nerc.ac.uk/standard_name/sea_water_salinity/",
        "label" : {
          "en": "Sea Water Salinity"
        }
      }
    },
    "POTM": {
      "type" : "Parameter",
      "description": {
        "en": "The potential temperature, in degrees celcius, of the sea water"
      },
      "unit" : {
        "symbol" : "°C"
      },
      "observedProperty" : {
        "id" : "http://vocab.nerc.ac.uk/standard_name/sea_water_potential_temperature/",
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
    "symbol" : "qudt:symbol"
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
- A parameter object may have a member with the name `"label"` where the value must be an i18n object that is the name of the parameter and which should be short. Note that this should be left out if it would be identical to the `"label"` of the `"observedProperty"` member.
- A parameter object may have a member with the name `"description"` where the value must be an i18n object which is a, perhaps lengthy, textual description of the parameter.
- A parameter object must have a member with the name `"observedProperty"` where the value is an object which must have the member `"label"` and which may have the members `"id"`, `"description"`, and `"categories"`. The value of `"label"` must be an i18n object that is the name of the observed property and which should be short. If given, the value of `"id"` must be a string and should be a common identifier. If given, the value of `"description"` must be an i18n object with a textual description of the observed property. If given, the value of `"categories"` must be a non-empty array of category objects. A category object must an `"id"` and a `"label"` member,  and may have a `"description"` member. The value of `"id"` must be a string and should be a common identifier. The value of `"label"` must be an i18n object of the name of the category and should be short. If given, the value of `"description"` must be an i18n object with a textual description of the category.
- A parameter object may have a member with the name `"categoryEncoding"` where the value is an object where each key is equal to an `"id"` value of the `"categories"` array within the `"observedProperty"` member of the parameter object. There must be no duplicate keys. The value is either an integer or an array of integers where each integer must be unique within the object.
- A parameter object may have a member with the name `"unit"` where the value is an object which must have either or both the members `"label"` or/and "`symbol`", and which may have the member `"id"`. If given, the value of `"symbol"` must be a string of the symbolic notation of the unit. If given, the value of `"label"` must be an i18n object of the name of the unit and should be short. If given, the value of `"id"` must be a string and should be a common identifier.
- A parameter object must not have a `"unit"` member if the `"observedProperty"` member has a `"categories"` member.


Example for a continuous-data parameter:
```js
{
  "type" : "Parameter",
  "description" : {
    "en": "The sea surface temperature in degrees celsius."
  },
  "observedProperty" : {
    "id" : "http://vocab.nerc.ac.uk/standard_name/sea_surface_temperature/",
    "label" : {
      "en": "Sea Surface Temperature"
    },
    "description" : {
      "en": "The temperature of sea water near the surface (including the part under sea-ice, if any), and not the skin temperature."
    },
    "property": "http://sweet.jpl.nasa.gov/2.3/propTemperature.owl#Temperature",
    "matrix": "http://../sea_surface"
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
    },
    "categories": [{
      "id": "http://.../landcover1/categories/grass",
      "label": {
        "en": "Grass"
      },
      "description": {
        "en": "Very green grass."
      }
    }, {
      "id": "http://.../landcover1/categories/forest",
      "label": {
        "en": "Forest"
      }
    }, ...]
  },
  "categoryEncoding": {
    "http://.../landcover1/categories/grass": 1,
    "http://.../landcover1/categories/forest": [2,3]
  }
}
```

## 4. ParameterGroup Objects

Parameter group objects represent metadata about the values of the coverage in terms of the observed property (like water temperature), the units, and others.

- A parameter group object may have any number of members (name/value pairs).
- A parameter group object must have a member with the name `"type"` and the value `"ParameterGroup"`.
- A parameter group object may have a member with the name `"id"` where the value must be a string and should be a common identifier.
- A parameter group object may have a member with the name `"label"` where the value must be an i18n object that is the name of the parameter group and which should be short. Note that this should be left out if it would be identical to the `"label"` of the `"observedProperty"` member.
- A parameter group object may have a member with the name `"description"` where the value must be an i18n object which is a, perhaps lengthy, textual description of the parameter group.
- A parameter group object may have a member with the name `"observedProperty"` where the value is an object as specified for parameter objects.
- A parameter group object must have either or both the members `"label"` or/and `"observedProperty"`.

Example of a group describing a vector quantity:
```js
{
  "type": "ParameterGroup",
  "observedProperty": {
    "label": {
      "en": "Wind velocity"
    }
  },
  "components": ["WIND_SPEED", "WIND_DIR"]
}
```
where `"WIND_SPEED"` and `"WIND_DIR"` reference existing parameters in a CoverageJSON coverage or collection object by their short identifiers.

Example of a group describing uncertainty of a parameter:
```js
{
  "type": "ParameterGroup",
  "label": {
    "en": "Sea surface temperature with uncertainty information"
  },
  "observedProperty": {
    "id": "http://vocab.nerc.ac.uk/standard_name/sea_surface_temperature/",
    "label": {
      "en": "Sea surface temperature"
    },
    "property": "http://sweet.jpl.nasa.gov/2.3/propTemperature.owl#Temperature",
    "matrix": "http://../sea_surface"
  },
  "components": ["SST_mean", "SST_stddev"]
}
```
where `"SST_stddev"` references the following parameter:
```js
{
  "type" : "Parameter",
  "observedProperty" : {
    "label" : {
      "en": "Sea surface temperature standard deviation"
    },
    "property": "http://sweet.jpl.nasa.gov/2.3/propTemperature.owl#Temperature",
    "matrix": "http://../sea_surface",
    "statisticalMeasure": "http://www.uncertml.org/statistics/standard-deviation",
    "broader": "http://vocab.nerc.ac.uk/standard_name/sea_surface_temperature/"
  },
  "unit" : {
    "symbol" : "°C"
  }
}
```
and `"SST_mean"`:
```js
{
  "type" : "Parameter",
  "observedProperty" : {
    "label" : {
      "en": "Sea surface temperature daily mean"
    },
    "property": "http://sweet.jpl.nasa.gov/2.3/propTemperature.owl#Temperature",
    "matrix": "http://../sea_surface",
    "statisticalMeasure": "http://.../statistics/mean_daily",
    "broader": "http://vocab.nerc.ac.uk/standard_name/sea_surface_temperature/"
  },
  "unit" : {
    "symbol" : "°C"
  }
}
```

## 5. Referencing system objects

Referencing values in some system is achieved with reference systems, which are typically spatial or temporal.
The following defines common spatial and temporal reference systems.

### 5.1. Spatial Reference Systems

Example of a geodetic CRS:

Minimal:
```js
{
  "type": "GeodeticCRS",
  "id": "http://www.opengis.net/def/crs/OGC/1.3/CRS84"
}
```

Full (details TBD, currently literal WKT translation):
```js
{
  "type": "GeodeticCRS",
  "title": "Foobar",
  "id": "http://...",
  "datum": {
    "type": "Ellipsoid",
    "name": "GRS 1980",
    "semimajor": 6378137,
    "inverseflattening": 298.257222101,
    "unit": {
      "name": "metre",
      "conversionfactor": 1.0
    }
  },
  "cs": {
    "type": "EllipsoidalCS",
    "dimension": 2,
    "axes": [{
      "name": "Longitude",
      "abbrev": "lon",
      "direction": "east",
      "unit": {
        "type": "Angle",
        "name": "degree",
        "conversionfactor": 0.0174532925199433
      }
    }, {
      "name": "Latitude",
      "abbrev": "lat",
      "direction": "north",
      "unit": {
        "type": "Angle",
        "name": "degree",
        "conversionfactor": 0.0174532925199433
      }
    }]
  }
}
```

Example of a projected CRS with a common projection:
```js
{
TBD
}
```

Example of referencing a curvilinear grid:
```js
{
  "type": "ProjectedCRS",
  "cs": {
    "type": "CartesianCS",
    "dimension": 2
  },
  "baseCRS": {
    "type": "GeodeticCRS",
    "id": "http://www.opengis.net/def/crs/OGC/1.3/CRS84"
  },
  "conversionToBaseCRS": [{
    "type": "DiscreteConversion",
    "sourceAxes": [{
      "start": 0, "stop": 100, "step": 1
    }, {
      "start": 0, "stop": 50, "step": 1
    }],
    "targetCoordinates": [{
      "values": [50.3,50.2,50.3,...] 
    }, {
      "values": [-10.1,-10.1,-10.2,...] 
    }]
  }, {
    "type": "DiscreteConversion",
    "sourceAxes": [{
      "start": -0.5, "stop": 100.5, "step": 1
    }, {
      "start": -0.5, "stop": 50.5, "step": 1
    }],
    "targetCoordinates": [{
      "values": [50.1,50.2,50.2,...] 
    }, {
      "values": [-10.0,-10.1,-10.2,...] 
    }]
  }]
}
```
Note that there are two conversions given above. This is useful when the domain includes
bounds as well. One of the conversions would be for the axis coordinates, and the other
for the bounds coordinates.
The `"values"` in `"targetCoordinates"` are multi-dimensional arrays encoded as a flat
array similar to the range values of a coverage. The dimension is equal to that of the
projected CRS. The order of the objects in `"targetCoordinates"` corresponds to the
order of the axes in the base/target CRS, which in the example above would be longitude,
then latitude.


### 5.2. Temporal Reference Systems

Time is referenced by a temporal reference system (temporal RS), which is either based on a string notation
or a coordinate reference system (CRS).

- A temporal RS object must have a member `"type"` with value `"TemporalRS"` if the referenced values
are in string notation, or `"TemporalCRS"` if the values are coordinates in a temporal coordinate system.
- A temporal RS object must have a member `"calendar"` with value `"Gregorian"` or a URI.
- If the Gregorian calender is used, then `"calendar"` must have the value `"Gregorian"` and cannot be a URI.
- If the temporal RS object has the type `"TemporalCRS"` then it must have the members `"origin"` and `"unit"`.
- If `"origin"` is defined, then its value must be a string with the syntax of an RFC3339 date-time string 
(YYYY-MM-DDTHH:MM:SS[.F]Z where Z is either "Z" or an offset +|-HH:MM). The syntax shall be applicable for
calendars other than the Gregorian calendar if they use a compatible scheme (years, months, days, etc.).
- If `"unit"` is defined, then its value must be an object with a member `"symbol"` that has a value of one of
`"ms"` (milliseconds), `"s"` (seconds), `"min"` (minutes), `"h"` (hours), or `"d"` (days).
A minute is defined as 60 seconds, an hour as 60 minutes, and a day as 24 hours.
- If the temporal RS object has the type `"TemporalRS"` then the referenced values must be strings
conforming to the syntax of XXX
- If the temporal RS object has the type `"TemporalCRS"` then the referenced values must be numbers.

Example of a String-based temporal referencing system:
```js
{
  "type": "TemporalRS",
  "calendar": "Gregorian"
}
```

Example of a temporal CRS:
```js
{
  "type": "TemporalCRS",
  "calendar": "Gregorian",
  "origin": "1980-01-01T00:00:00Z",
  "unit": {
    "symbol": "s"
  }
}
```

## 6. CoverageJSON Objects

CoverageJSON documents always consist of a single object. This object (referred to as the CoverageJSON object below) represents a domain, range, coverage, or collection of coverages.

- The CoverageJSON object may have any number of members (name/value pairs).
- The CoverageJSON object must have a member with the name `"type"`. This member's value is a string that determines the type of the CoverageJSON object.
- The value of the type member must be one of: `"Grid"`, `"Profile"`, `"PointSeries"`, `"Point"`, `"Trajectory"`, `"Section"`, `"MultiPolygonSeries"`, `"MultiPolygon"`, `"Polygon"`, `"GridCoverage"`, `"ProfileCoverage"`, `"PointSeriesCoverage"`, `"PointCoverage"`, `"TrajectoryCoverage"`, `"SectionCoverage"`, `"MultiPolygonSeriesCoverage"`, `"MultiPolygonCoverage"`, `"PolygonCoverage"`, `"Range"`, or `"CoverageCollection"`. The case of the type member values must be as shown here.

### 6.1. Domain Objects

A domain object is a CoverageJSON object which defines a coordinate space and the order of the enumeration of all coordinates in that space.
It's general structure is:
```js
{
  "type": "<domaintype>",
  "axes": { ... },
  "axisOrder": [...],
  "referencing": [...]
}
```

- The value of the type member must be one of: `"Grid"`, `"Profile"`, `"PointSeries"`, `"Point"`, `"Trajectory"`, `"Section"`, `"MultiPolygonSeries"`, `"MultiPolygon"`, and `"Polygon"`.
- A domain object must have the members `"axes"`, `"referencing"`, and, if there is more than one axis with more than one coordinate, `"axisOrder"`.
- The value of `"axes"` must be an object where each key is an axis identifier and each value an axis object. An axis object must have a `"values"` member which has as value a non-empty array of axis coordinates. The values in that array must be ordered monotonically according to their ordering relation defined by the used CRS. If the axis is composite, then the axis object must have the members `"components"` and `"geometryType"`. The value of `"geometryType"` is either `"Point"` or `"Polygon"`. For `"Point"`, each axis coordinate must be an array of primitive values. For `"Polygon"`, each axis coordinate must be a GeoJSON-like Polygon coordinate array. The value of `"components"` is a non-empty array of component identifiers corresponding to the order of the inner (TBD) coordinates inside `"values"`. A composite axis is said to have composite coordinates. An axis identifier must not be used as a component identifier and vice versa. 
- The value of `"axisOrder"` must be an array of two or more axis identifiers.
- The value of `"referencing"` is an array of referencing objects. A referencing object must have a member `"identifiers"` which has as value an array of axis and/or component identifiers that are referenced in this object. Depending on the type of referencing, the ordering of the identifiers may be relevant, e.g. for 2D/3D coordinate reference systems. A referencing object must also have exactly one of the members `"srs"`, `"trs"`, or `"rs"`, where `"srs"` has as value a spatial referencing system object, `"trs"` a temporal referencing system object, and `"rs"` a referencing system object that is neither spatial nor temporal. The following section defines common types of referencing system objects.

Coordinate Space:
- A coordinate space is defined by an array `[C1, C2, ..., Cn]` where each of `C1` to `Cn` is an array of coordinates. The number of elements in a coordinate space are `|C1| * |C2| * ... * |Cn|`, where a composite coordinate counts as a single coordinate. Each element in the space can be referenced by a unique number. A coordinate space assigns a unique number to `[c1, c2, ..., cn]` by assuming an `n`-dimensional array of shape `[|C1|, |C2|, ..., |Cn|]` stored in row-major order.
- The coordinate space of a domain object is defined by the array `[C1, C2, ..., Cn]` where `C1` is the `"values"` member corresponding to the first axis identifier in the `"axisOrder"` array, or any member if no `"axisOrder"` exists. `C2` corresponds to the second axis identifier in `"axisOrder"`, continuing until the last axis identifier `Cn`.

Requirements for all domain types defined in this specification:
- The axis or component identifiers `"x"` and `"y"` must refer to horizontal spatial coordinates.
- The axis or component identifier `"z"` must refer to vertical spatial coordinates.
- The axis or component identifier `"t"` must refer to temporal coordinates.


#### 6.1.x Overview of domain types

Domain               | x | y | z | t | composite
---------------------|---|---|---|---|----------
Grid                 |[M]|[M]|[O]|[O] 
Profile              | M | M |[M]| O
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


#### 6.1.1. Grid

- A Grid domain must have the axes `"x"` and `"y"` and may have the axes `"z"` and `"t"`.
- The axis order must be t,z,y,x.

TODO should the domain types in this spec really require a fixed axis order? 

Example:
```js
{
  "type": "Grid",
  "axes": {
    "x": { "values": [1,2,3] },
    "y": { "values": [20,21] },
    "z": { "values": [1] },
    "t": { "values": ["2008-01-01T04:00:00Z"] }
  },
  "axisOrder": ["t","z","y","x"],
  "referencing": [...]
}
```

#### 6.1.2. Profile

- A Profile domain must have the axes `"x"`, `"y"`, and `"z"`, where `"x"` and `"y"` must have a single coordinate only.

Example:
```js
{
  "type": "Profile",
  "axes": {
    "x": { "values": [1] },
    "y": { "values": [21] },
    "z": { "values": [1,5,20] },
    "t": { "values": ["2008-01-01T04:00:00Z"] }
  },
  "referencing": [...]
}
```

#### 6.1.3. PointSeries

- A PointSeries domain must have the axes `"x"`, `"y"`, and `"t"` where `"x"` and `"y"` must have a single coordinate only.
- A PointSeries domain may have the axis `"z"` which must have a single coordinate only.

Example:
```js
{
  "type": "PointSeries",
  "axes": {
    "x": { "values": [1] },
    "y": { "values": [20] },
    "z": { "values": [1] },
    "t": { "values": ["2008-01-01T04:00:00Z","2008-01-01T05:00:00Z"] }
  },
  "referencing": [...]
}
```

#### 6.1.4. Point

- A Point domain must have the axes `"x"` and `"y"` and may have the axes `"z"` and `"t"` where all must have a single coordinate only.

Example:
```js
{
  "type": "Point",
  "axes": {
    "x": { "values": [1] },
    "y": { "values": [20] },
    "z": { "values": [1] },
    "t": { "values": ["2008-01-01T04:00:00Z"] }
  },
  "referencing": [...]
}
```

#### 6.1.5. Trajectory

- A Trajectory domain must have the axis `"composite"` and may have the axis `"z"` where `"z"` must have a single coordinate only.
- The axis `"composite"` must have the geometry type `"Point"` and the components `"x","y","z","t"` or `"x","y","t"`.
- The coordinate ordering of the axis `"composite"` must follow the ordering of its `"t"` component.

Example:
```js
{
  "type": "Trajectory",
  "axes": {
    "composite": {
      "geometryType": "Point",
      "components": ["x","y","z","t"],      
      "values": [
        [1,20,1,"2008-01-01T04:00:00Z"],
        [2,21,3,"2008-01-01T04:30:00Z"]
      ]
    }
  },
  "referencing": [...]
}
```

Example without z:
```js
{
  "type": "Trajectory",
  "axes": {
    "composite": {
      "geometryType": "Point",
      "components": ["x","y","t"],      
      "values": [
        [1,20,"2008-01-01T04:00:00Z"],
        [2,21,"2008-01-01T04:30:00Z"]
      ]
    }
  },
  "referencing": [...]
}
```

Example with z defined as constant value:
```js
{
  "type": "Trajectory",
  "axes": {
    "composite": {
      "geometryType": "Point",
      "components": ["x","y","t"],      
      "values": [
        [1,20,"2008-01-01T04:00:00Z"],
        [2,21,"2008-01-01T04:30:00Z"]
      ]
    },
    "z": { "values": [5] }
  },
  "referencing": [...]
}
```

#### 6.1.6. Section

- A Section domain must have the axes `"composite"` and `"z"`.
- The axis `"composite"` must have the geometry type "Point" and the components `"x","y","t"`.
- The coordinate ordering of the axis `"composite"` must follow the ordering of its `"t"` component.

Example:
```js
{
  "type": "Section",
  "axes": {
    "z": { "values": [10,20,30] },
    "composite": {
      "geometryType": "Point",
      "components": ["x","y","t"],
      "values": [
        [1,20,"2008-01-01T04:00:00Z"],
        [2,21,"2008-01-01T04:30:00Z"]
      ]
    }
  },
  "axisOrder": ["z","composite"],
  "referencing": [...]
}
```

#### 6.1.7. Polygon

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
  "type": "Polygon",
  "axes": {
    "composite": {
      "geometryType": "Polygon",
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

#### 6.1.8. PolygonSeries

- A PolygonSeries domain must have the axes `"composite"` and `"t"` where `"composite"` must have a single Polygon coordinate.
- A PolygonSeries domain may have the axis `"z"` which must have a single coordinate only.
- The axis `"composite"` must have the geometry type "Polygon" and the components `"x","y"`.

Example:
```js
{
  "type": "PolygonSeries",
  "axes": {
    "composite": {
      "geometryType": "Polygon",
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

#### 6.1.9. MultiPolygon

- A MultiPolygon domain must have the axis `"composite"` where the coordinates are Polygons.
- The axis `"composite"` must have the geometry type `"Polygon"` and the components `"x","y"`.
- A MultiPolygon domain may have the axes `"z"` and `"t"` which both must have a single coordinate only.

Example:
```js
{
  "type": "MultiPolygon",
  "axes": {
    "composite": {
      "geometryType": "Polygon",
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

#### 6.1.10. MultiPolygonSeries

- A MultiPolygonSeries domain must have the axes `"composite"` and `"t"` where the coordinates of `"composite"` are Polygons.
- The axis `"composite"` must have the geometry type `"Polygon"` and the components `"x","y"`.
- A MultiPolygon domain may have the axis `"z"` which must have a single coordinate only.

Example:
```js
{
  "type": "MultiPolygonSeries",
  "axes": {
    "composite": {
      "geometryType": "Polygon",
      "components": ["x","y"],
      "values": [
        [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]  ],
        [ [ [200.0, 10.0], [201.0, 10.0], [201.0, 11.0], [200.0, 11.0], [200.0, 10.0] ] ]
      ]
    }
    "z": { "values": [2] },
    "t": { "values": ["2008-01-01T04:00:00Z", "2010-01-01T00:00:00Z"] }
  },
  "referencing": [...]
}
```

#### 6.1.11. Axis Value Bounds

- A domain axis object may have axis value bounds defined in the member `"bounds"` where the value is an array of values of length `len*2` with `len` being the length of the `"values"` array. For each axis value at array index `i` in the `"values"` array, a lower and upper bounding value at positions `2*i` and `2*i+1`, respectively, are given in the bounds array.
- If a domain axis object has no `"bounds"` member then a bounds array may be derived automatically.

Example:
```js
{
  "type": "Grid",
  "axes": {
    "x": { "values": [1,2,3] },
    "y": {
      "values": [20,21],
      "bounds": [19.5,20.5,
                 20.5,21.5]
    },
    "z": {
      "values": [50],
      "bounds": [0,100]
    },
    "t": { "values": ["2008-01-01T04:00:00Z"] }
  },
  "axisOrder": ["t","z","y","x"],
  "referencing": [...]
}
```

### 6.2. Range Objects

A CoverageJSON object with the type `"Range"` is a range object.

- A range object must have a member with the name `"values"` where the value is an array of numbers and nulls where nulls represent missing data.
- A range object may have both or none of the `"validMin"` and `"validMax"` members where the value of each is a number. The value of `"validMin"` must be equal to or smaller than the minimum value in the `"values"` array, ignoring null. The value of `"validMax"` must be equal to or greater than the maximum value in the `"values"` array, ignoring null. `"validMin"` and `"validMax"` may be used by clients as an initial legend extent and should therefore not be too much smaller or greater than the actual extent of all values.
- If the `"values"` array of a range object does not contain nulls, then for CBOR serializations typed arrays (as in RDFxxxx) should be used for increased space and parsing efficiency.
- Note that common JSON implementations may use 64-bit floating point numbers as data type for `"values"`, therefore precision has to be taken into account. For example, only integers within the extent [-2^32, 2^32] can be accurately represented with 64-bit floating point numbers.

#### 6.2.1. Offset/Factor Encoding (CBOR-only)

A simple compression scheme typically used for storing low-resolution floating point data as small integers in binary formats is the offset/factor encoding. When using CBOR as serialization format, this encoding scheme may be used for the `"values"` array as described below.

- A range object may have both or none of the `"offset"` and `"factor"` members where the value of each is a number.
- If both `"offset"` and `"factor"` are present in a range object, each non-null value `v` in `"values"` must be converted to `v * factor + offset` when accessing it and all values in the `"values"` array must be integers or nulls. The converted value is always a floating point number and therefore this mechanism shall not be used for values that shall result in integers.

#### 6.2.2. Missing Value Encoding (CBOR-only)

If only a small amount of values in `"values"` are missing it is more space efficient to encode these missing values using a number outside the valid value extent (instead of null) so that CBOR's typed array representation for `"values"` can be applied.

- If a range object contains the `"validMin"` and `"validMax"` members it may have a member `"missing"` with value `"nonvalid"`.
- If a range object has the member `"missing"` with value `"nonvalid"`, then all missing values in `"values"` must be encoded as a number outside the `"validMin"`/`"validMax"` extent and interpreted as missing values.

### 6.3. Coverage Objects

A CoverageJSON object with the type `"<DomainType>Coverage"` is a coverage object, where `<DomainType>` is any of the domain types defined earlier.

- If a coverage has a commonly used identifier, that identifier should be included as a member of the coverage object with the name `"id"`.
- A coverage object must have a member with the name `"domain"` where the value is either a domain object or a URL. The domain type must match the `<DomainType>` part of the coverage type.
- A coverage object may have a member with the name `"parameters"` where the value is an object where each member has as name a short identifier and as value a parameter object. The identifier corresponds to the commonly known concept of "variable name" and is merely used in clients for conveniently accessing the corresponding range object.
- A coverage object must have a `"parameters"` member if the coverage object is not part of a coverage collection or if the coverage collection does not have a `"parameters"` member.
- A coverage object must have a member with the name `"ranges"` where the value is a range set object. A range set object must have a member with the name `"type"` and the value `"RangeSet"`. Any member of a range set object except `"type"` has as name any of the names in a `"parameters"` object in scope and as value either a range object or a URL. A `"parameters"` member in scope is either within the enclosing coverage object or, if part of a coverage collection, in the parent coverage collection object. The array elements of the `"values"` member of each range object must correspond to the coordinate space defined by `"domain"` in terms of element order and count. If the referenced parameter object has a `"categoryEncoding"` member, then each array element of the `"values"` member must be equal to one of the values defined in the `"categoryEncoding"` object and be interpreted as the matching category.
- A coverage object may have a member with the name `"bbox"` where the value is an array of four numbers `[west longitude, south latitude, east longitude, north latitude]` describing the bounding box of the coverage data in degrees (WGS84).

### 6.4. Coverage Collection Objects

A CoverageJSON object with the type `"CoverageCollection"` is a coverage collection object.

- A coverage collection object must have a member with the name `"coverages"`. The value corresponding to `"coverages"` is an array. Each element in the array is a coverage object as defined above.
- A coverage collection object may have a member with the name `"parameters"` where the value is a list of parameter objects.

## 7. Linked Data

### 7.1. JSON-LD Context

A linked data context in terms of JSON-LD should be established by including a `"@context"` element in the root of a CoverageJSON object which refers to the base CoverageJSON context `"https://rawgit.com/reading-escience-centre/coveragejson/master/contexts/coveragejson-base.jsonld"` and any other necessary local contexts. The base CoverageJSON context uses common vocabularies like RDF Schema, DCMI Metadata Terms, and W3C Time. Domain and range objects should *not* have a context because those data, in particular the potentially big arrays, are currently not suitable to be represented as linked data.

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

### 7.2. Linked Data Platform considerations

The [Linked Data Platform (LDP) recommendation](www.w3.org/TR/ldp/) cleanly separates RDF from non-RDF resources. In order to expose CoverageJSON documents, which may contain a mix of RDF and non-RDF content, in such a platform, the following guidelines may be used:

- Every CoverageJSON document, including those embedded in another, should be made available as a separate resource with its own URL and referenced in parent CoverageJSON documents where appropriate (e.g. a coverage should contain the URLs for its domain and range documents, no matter if they are embedded or not).
- Domain and range documents should be treated as non-RDF resources, and coverage and coverage collection documents as RDF resources.
- Domain and range documents should always be returned with a CoverageJSON content type, while coverage and coverage collection documents should be made available under RDF as well as CoverageJSON content types.
- If one of the RDF content types is requested for a coverage or coverage collection, then appropriate JSON-LD (or another RDF serialization if required) should be returned, where the domain and range objects should not be embedded and thus referenced by URL only. A coverage collection should be exposed as a LDP container.
- If one of the CoverageJSON content types is requested for any CoverageJSON document, then any compatible form of the CoverageJSON document may be returned and be treated as a non-RDF resource. Whether to embed domain and/or range may be decided with additional URL query parameters or other means.

## 8. Resolving domain and range URLs

If a domain or range is referenced by a URL in a CoverageJSON document, then the client should, whenever is appropriate, load the data from the given URL and treat the loaded data as if it was directly embedded in place of the URL. When sending HTTP requests, the `Accept` header should be set appropriately to the supported CoverageJSON media types.

## 9. Media Type, File Extensions, and Encodings

The CoverageJSON media type shall be `application/prs.coverage+json` when encoded in JSON, and `application/prs.coverage+cbor` when encoded in CBOR. Both media types have an optional parameter `profile` which is a non-empty list of space-separated URIs identifying specific constraints or conventions that apply to a CoverageJSON document according to [RFC6906](http://www.ietf.org/rfc/rfc6906.txt). The only profile URI defined in this document is `http://coveragejson.org/profiles/standalone` which asserts that all domain and range objects are directly embedded in a CoverageJSON document and not referenced by URLs.

The file extension for JSON shall be `covjson`, and for CBOR `covcbor`.

A software may only claim support for reading a CoverageJSON document encoded in CBOR if it can interpret the [typed array tags of CBOR](https://tools.ietf.org/html/draft-jroatch-cbor-tags-03).

## Appendix A. Coverage Examples

## Attribution

This specification uses ideas and sentence structures from the [GeoJSON specification](http://geojson.org/geojson-spec.html) which is licensed under a [Creative Commons Attribution 3.0 United States License](http://creativecommons.org/licenses/by/3.0/us/).
