# The CoverageJSON Format Specification

WORK-IN-PROGRESS

<!-- Document Info -->
<table>
  <tr>
    <th>Authors</th>
    <td>
      Maik Riechert (<a href="http://www.reading.ac.uk">University of Reading</a>),
      <a href="http://www.met.reading.ac.uk/users/users/1659">Jon Blower</a> (<a href="http://www.reading.ac.uk">University of Reading</a>)
    </td>
  </tr>
  <tr>
    <th>Revision</th>
    <td>0.1</td>
  </tr>
  <tr>
    <th>Date</th>
    <td>02 March 2016</td>
  </tr>
  <tr>
    <th>Abstract</th>
    <td>
      CoverageJSON is a geospatial coverage interchange format based on JavaScript
      Object Notation (JSON).
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

The following items are (major) outstanding issues to be resolved for the first version: 
- [#45](https://github.com/Reading-eScience-Centre/coveragejson/issues/45)
  Representation of multiple time axes
- [#51](https://github.com/Reading-eScience-Centre/coveragejson/issues/51)
  Version number inclusion / evolution of format

## 1. Introduction

CoverageJSON is a format for encoding coverage data like grids, time series, and vertical profiles, distinguished by the geometry of their spatiotemporal domain. A CoverageJSON object may represent a domain, a range, a coverage, or a collection of coverages. A range in CoverageJSON  represents coverage values. A coverage in CoverageJSON is the combination of a domain, parameters, ranges, and additional metadata. A coverage collection represents a list of coverages.

A complete CoverageJSON data structure is always an object (in JSON terms). In CoverageJSON, an object consists of a collection of name/value pairs -- also called members. For each member, the name is always a string. Member values are either a string, number, object, array or one of the literals: true, false, and null. An array consists of elements where each element is a value as described above.

### 1.1. Example

A CoverageJSON grid coverage of global air temperature:

```js
{
  "type" : "Coverage",
  "profile": "GridCoverage",
  "domain" : {
    "type": "Domain",
    "profile": "Grid",
    "axes": {
      "x": { "start": -179.5, "stop": 179.5, "num": 360 },
      "y": { "start": -89.5, "stop": 89.5, "num": 180 },
      "t": { "values": ["2013-01-13T00:00:00Z"] }
    },
    "rangeAxisOrder": ["t","y","x"],
    "referencing": [{
      "components": ["x","y"],
      "system": {
        "type": "GeodeticCRS",
        "id": "http://www.opengis.net/def/crs/OGC/1.3/CRS84"        
      }
    }, {
      "components": ["t"],
      "system": {
        "type": "TemporalRS",
        "calendar": "Gregorian"
      }
    }]
  },
  "parameters" : {
    "TEMP": {
      "type" : "Parameter",
      "description" : {
        "en": "The air temperature measured in degrees Celsius."
      },
      "unit" : {
        "label": {
          "en": "Degree Celsius"
        },
        "symbol": {
          "value": "Cel"
          "type": "http://www.opengis.net/def/uom/UCUM/"
        }
      },
      "observedProperty" : {
        "id" : "http://vocab.nerc.ac.uk/standard_name/air_temperature/",
        "label" : {
          "en": "Air temperature",
          "de": "Lufttemperatur"
        }
      }
    }
  },
  "ranges" : {
    "TEMP" : "http://.../coverages/123/ranges/TEMP"
  }
}
```
where `"http://.../coverages/123/ranges/TEMP"` points to the following document:
```js
{
  "type" : "Range",
  "values" : [ 27.1, 24.1, null, 25.1, ... ], // 360*180 values,
  "dataType": "float"
}
```
Range data can also be directly embedded into the main CoverageJSON document, making it stand-alone.

### 1.2. Differences to OGC Coverage Implementation Schema (CIS)

The candidate OGC standard [Coverage Implementation Schema 1.1](http://www.opengeospatial.org/pressroom/pressreleases/2345) (short CIS)
defines a coverage model targeted towards OGC service types like Web Coverage Service (WCS)
and is the successor of the
["GML 3.2.1 Application Schema – Coverages" version 1.0](https://portal.opengeospatial.org/files/?artifact_id=48553) (short GMLCOV).

The model of CoverageJSON can be seen as a mix of CIS and the data cube-based [NetCDF file format](https://en.wikipedia.org/wiki/NetCDF). 

The following lists some areas where the model used by CoverageJSON departs from CIS:
- CIS enforces exactly one coordinate reference system (CRS) per coverage, CoverageJSON allows CRSs to be associated with a given combination of components (TODO introduce components before).
- CIS has separate domain concepts for grids vs other types, CoverageJSON always uses collections of orthogonal axes for organizing domains, whether gridded or not.
- CIS has no specific model for describing categories of a categorical parameter, CoverageJSON defines such a model.
- CIS has no notion of semantically grouping parameters (e.g. velocity = speed + direction), CoverageJSON allows that.

### 1.3. Definitions

- JavaScript Object Notation (JSON), and the terms object, name, value, array, string, number, and null, are defined in [IETF RFC 4627](http://www.ietf.org/rfc/rfc4627.txt).
- JSON-LD is defined in [http://www.w3.org/TR/json-ld/](http://www.w3.org/TR/json-ld/).
- The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [IETF RFC 2119](http://www.ietf.org/rfc/rfc2119.txt).

## 2. i18n Objects

An i18n object represents a string in multiple languages where each key is a language tag as defined in [BCP 47](http://tools.ietf.org/html/bcp47), and the value is the string in that language.

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
- A parameter object may have a member with the name `"unit"` where the value is an object which must have either or both the members `"label"` or/and "`symbol`", and which may have the member `"id"`. If given, the value of `"symbol"` must either be a string of the symbolic notation of the unit, or an object with the members `"value"` and `"type"` where `"value"` is the symbolic unit notation and `"type"` is a URI which references the unit serialization scheme that is used. If given, the value of `"label"` must be an i18n object of the name of the unit and should be short. If given, the value of `"id"` must be a string and should be a common identifier. It is strongly recommended to reference a unit serialization scheme to allow automatic unit conversion.
- If [UCUM](http://unitsofmeasure.org) is used as unit serialization scheme, then the `"type"` value of `"symbol"` shall be `"http://www.opengis.net/def/uom/UCUM/"`.
- A parameter object must not have a `"unit"` member if the `"observedProperty"` member has a `"categories"` member.


Example for a continuous-data parameter:
```js
{
  "type" : "Parameter",
  "description" : {
    "en": "The sea surface temperature in degrees Celsius."
  },
  "observedProperty" : {
    "id" : "http://vocab.nerc.ac.uk/standard_name/sea_surface_temperature/",
    "label" : {
      "en": "Sea Surface Temperature"
    },
    "description" : {
      "en": "The temperature of sea water near the surface (including the part under sea-ice, if any), and not the skin temperature."
    }
  },
  "unit" : {
    "label" : {
      "en": "Degree Celsius"
    },
    "symbol": {
      "value": "Cel"
      "type": "http://www.opengis.net/def/uom/UCUM/"
    }
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

Parameter group objects represent logical groups of parameters, for example vector quantities.

- A parameter group object may have any number of members (name/value pairs).
- A parameter group object must have a member with the name `"type"` and the value `"ParameterGroup"`.
- A parameter group object may have a member with the name `"id"` where the value must be a string and should be a common identifier.
- A parameter group object may have a member with the name `"label"` where the value must be an i18n object that is the name of the parameter group and which should be short. Note that this should be left out if it would be identical to the `"label"` of the `"observedProperty"` member.
- A parameter group object may have a member with the name `"description"` where the value must be an i18n object which is a, perhaps lengthy, textual description of the parameter group.
- A parameter group object may have a member with the name `"observedProperty"` where the value is an object as specified for parameter objects.
- A parameter group object must have either or both the members `"label"` or/and `"observedProperty"`.
- A parameter group object must have a member with the name `"members"` where the value is a non-empty array of parameter identifiers (see 6.3 Coverage objects).

Example of a group describing a vector quantity:
```js
{
  "type": "ParameterGroup",
  "observedProperty": {
    "label": {
      "en": "Wind velocity"
    }
  },
  "members": ["WIND_SPEED", "WIND_DIR"]
}
```
where `"WIND_SPEED"` and `"WIND_DIR"` reference existing parameters in a CoverageJSON coverage or collection object by their short identifiers.

Example of a group describing uncertainty of a parameter:
```js
{
  "type": "ParameterGroup",
  "label": {
    "en": "Daily sea surface temperature with uncertainty information"
  },
  "observedProperty": {
    "id": "http://vocab.nerc.ac.uk/standard_name/sea_surface_temperature/",
    "label": {
      "en": "Sea surface temperature"
    }
  },
  "members": ["SST_mean", "SST_stddev"]
}
```
where `"SST_mean"` references the following parameter:
```js
{
  "type" : "Parameter",
  "observedProperty" : {
    "label" : {
      "en": "Sea surface temperature daily mean"
    },
    "statisticalMeasure": "http://www.uncertml.org/statistics/mean",
    "statisticalPeriod": "P1D",
    "broader": "http://vocab.nerc.ac.uk/standard_name/sea_surface_temperature/"
  },
  "unit" : {
    "label": {
      "en": "Kelvin"
    },
    "symbol": {
      "value": "K"
      "type": "http://www.opengis.net/def/uom/UCUM/"
    }
  }
}
```
and `"SST_stddev"`:
```js
{
  "type" : "Parameter",
  "observedProperty" : {
    "label" : {
      "en": "Sea surface temperature standard deviation of daily mean"
    },
    "statisticalMeasure": "http://www.uncertml.org/statistics/standard-deviation",
    "broader": "http://vocab.nerc.ac.uk/standard_name/sea_surface_temperature/"
  },
  "unit" : {
    "label": {
      "en": "Kelvin"
    },
    "symbol": {
      "value": "K"
      "type": "http://www.opengis.net/def/uom/UCUM/"
    }
  }
}
```

## 5. Reference system objects

Referencing values in some system is achieved with reference systems, which are typically spatial or temporal,
but can also be identifier-based.
The following defines common types of such reference systems.

### 5.1. Spatial Reference Systems

Example of a geodetic CRS:
```js
{
  "type": "GeodeticCRS",
  "id": "http://www.opengis.net/def/crs/OGC/1.3/CRS84"
}
```

Example of a projected CRS (here [British National Grid](http://spatialreference.org/ref/epsg/osgb-1936-british-national-grid/)):
```js
{
  "type": "ProjectedCRS",
  "id": "http://www.opengis.net/def/crs/EPSG/0/27700"
}
```

Example of a vertical CRS with embedded detail information:
```js
{
  "type": "VerticalCRS",
  "id": "http://www.opengis.net/def/crs/EPSG/0/5703"
  "datum": {
    "id": "http://www.opengis.net/def/datum/EPSG/0/5103",
    "label": {
      "en": "North American Vertical Datum 1988"
    }
  },
  "cs": {
    "id": "http://www.opengis.net/def/cs/EPSG/0/6499",
    "axes": [{
      "id": "http://www.opengis.net/def/axis/EPSG/0/114",
      "name": {
        "en": "Gravity-related height"
      },
      "direction": "up",
      "unit": {
        "symbol": "m"
      }
    }]
  }
}
```


### 5.2. Temporal Reference Systems

Time is referenced by a temporal reference system (temporal RS).
In this specification, only a string-based notation for time values is defined.

- A temporal RS object must have a member `"type"`. The only currently defined value of it is `"TemporalRS"`.
- A temporal RS object must have a member `"calendar"` with value `"Gregorian"` or a URI.
- If the Gregorian calender is used, then `"calendar"` must have the value `"Gregorian"` and cannot be a URI.
- A temporal RS object may have a member `"timeScale"` with a URI as value. 
  If omitted, the time scale defaults to UTC (`"http://www.opengis.net/def/trs/BIPM/0/UTC"`).
  If the time scale is UTC, the `"timeScale"` member must be omitted.
- If the calendar is based on years, months, days, then the referenced values should use one of the following
  ISO8601-based lexical representations:
  - YYYY
  - ±XYYYY (where X stands for extra year digits)
  - YYYY-MM
  - YYYY-MM-DD
  - YYYY-MM-DDTHH:MM:SS[.F]Z where Z is either "Z" or a time scale offset +|-HH:MM
- If calendar dates with reduced precision are used in a lexical representation (e.g. `"2016"`), then
  a client should interpret those dates in that reduced precision.

Example:
```js
{
  "type": "TemporalRS",
  "calendar": "Gregorian"
}
```

### 5.3. Identifier-based Reference Systems

Identifier-based reference systems (identifier RS) .

- An identifier RS object must have a member `"type"` with value `"IdentifierRS"`.
- An identifier RS object may have a member `"id"` where the value must be a string and should be a common identifier for the reference system.
- An identifier RS object may have a member `"label"` where the value must be an i18n object that is the name of the reference system.
- An identifier RS object must have a member `"targetConcept"` where the value is an object that must have a member `"label"` where the value must be an i18n object that is the name of the concept which is referenced in the system.
- An identifier RS object may have a member `"identifiers"` where the value is an object where each key is an identifier referenced by the identifier RS and each value an object describing the referenced concept, equal to `"targetConcept"`.
- Domain values associated to an identifier RS must be strings.

Example of a geographic identifier reference system:
```js
{
  "type": "IdentifierRS",
  "id": "https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2",
  "label": { "en": "ISO 3166-1 alpha-2 codes" },
  "targetConcept": {
    "id": "http://dbpedia.org/resource/Country",
    "label": {"en": "Country", "de": "Land" }
  },
  "identifiers": {
    "de": {
      "id": "http://dbpedia.org/resource/Germany",
      "label": { "de": "Deutschland", "en": "Germany" }
    },
    "gb": {
      "id": "http://dbpedia.org/resource/United_Kingdom",
      "label": { "de": "Vereinigtes Königreich", "en": "United Kingdom" }
    }
  }
}
```
The domain values in the above example would be `"de"` and `"gb"`.


## 6. CoverageJSON Objects

CoverageJSON documents always consist of a single object. This object (referred to as the CoverageJSON object below) represents a domain, range, coverage, or collection of coverages.

- The CoverageJSON object may have any number of members (name/value pairs).
- The CoverageJSON object must have a member with the name `"type"` which has as value one of: `"Domain"`, `"Range"`, `"Coverage"`, or `"CoverageCollection"`. The case of the type member values must be as shown here.

### 6.1. Domain Objects

A domain object is a CoverageJSON object which defines a coordinate space and the order of the enumeration of all coordinates in that space.
Its general structure is:
```js
{
  "type": "Domain",
  "profile": "...",
  "axes": { ... },
  "rangeAxisOrder": [...],
  "referencing": [...]
}
```

- The value of the type member must be `"Domain"`.
- For interoperability reasons it is strongly recommended that a domain object has the member `"profile"` with a string value to indicate that the domain follows a certain structure (e.g. a time series, a vertical profile, a spatio-temporal 4D grid). See the ["Common CoverageJSON Profiles Specification"](profiles.md), which forms part of this specification, for details. Custom profiles not part of this specification may be given by full URIs only.
- A domain object must have the members `"axes"` and, if there is more than one axis with more than one axis value, `"rangeAxisOrder"`.
- The value of `"axes"` must be an object where each key is an axis identifier and each value an axis object as defined below. 
- The value of `"rangeAxisOrder"` must be an array of all axis identifiers of the domain object. It determines in which order range values must be stored (see "Coordinate Space" below).
- A domain object may have the member `"referencing"` where the value is an array of reference system connection objects as defined below.
- A domain object must have a `"referencing"` member if the domain object is not part of a coverage collection or if the coverage collection does not have a `"referencing"` member.

#### 6.1.1. Axis Objects

- An axis object must have either a `"values"` member or, as a compact notation for a regularly spaced numeric axis, all the members `"start"`, `"stop"`, and `"num"`.
- The value of `"values"` is a non-empty array of axis values.
- The values of `"start"` and `"stop"` must be numbers, and the value of `"num"` an integer greater than zero. If the value of `"num"` is `1`, then `"start"` and `"stop"` must have identical values. For `num > 1`, the array elements of `"values"` may be reconstructed with the formula `start + i * step` where `i` is the ith element and in the interval `[0, num-1]` and `step = (stop - start) / (num - 1)`. If `num = 1` then `"values"` is `[start]`. 
- The value of `"dataType"` determines the structure of an axis value and its components that are made available for referencing. The value of `"dataType"` must be either `"Primitive"`, `"Tuple"`, `"Polygon"`, or a full custom URI (although custom data types are not recommended for interoperability reasons). For `"Primitive"`, there is a single component and each axis value must be a number or string. For `"Tuple"`, each axis value must be an array of fixed size of primitive values in a defined order, where the tuple size corresponds to the number of components. For `"Polygon"`, each axis value must be a GeoJSON Polygon coordinate array, where each of the coordinate components (e.g. the x coordinates) form a component in the order they appear.
- If missing, the member `"dataType"` defaults to `"Primitive"` and must not be included for that default case.
- If `"dataType"` is `"Primitive"` and the associated reference system (see 6.1.2) defines a natural ordering of values then the array values must be ordered monotonically, that is, increasing or decreasing.
- The value of `"components"` is a non-empty array of component identifiers corresponding to the order of the components defined by `"dataType"`.
- If missing, the member `"components"` defaults to a one-element array of the axis identifier and must not be included for that default case.
- A component identifier shall not be defined more than once in all axis objects of a domain object.
- An axis object may have axis value bounds defined in the member `"bounds"` where the value is an array of values of length `len*2` with `len` being the length of the `"values"` array. For each axis value at array index `i` in the `"values"` array, a lower and upper bounding value at positions `2*i` and `2*i+1`, respectively, are given in the bounds array.
- If a domain axis object has no `"bounds"` member then a bounds array may be derived automatically.

Example of an axis object with bounds:
```js
{
  "values": [20,21],
  "bounds": [19.5,20.5,
             20.5,21.5]
}
```

Example of an axis object with regular axis encoding:
```js
{
  "start": 0,
  "stop": 5,
  "num": 6
  // equal to: "values": [0,1,2,3,4,5]
}
```

Example of an axis object with tuple values:
```js
{
  "dataType": "Tuple",
  "components": ["t","x","y"],  
  "values": [
    ["2008-01-01T04:00:00Z",1,20],
    ["2008-01-01T04:30:00Z",2,21]
  ]
}
```

Example of an axis object with Polygon values:
```js
{
  "dataType": "Polygon",
  "components": ["x","y"],
  "values": [
    [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]  ]
  ]
}
```

#### 6.1.2. Reference System Connection Objects

A reference system connection object creates a link between values within domain axes and a reference system to be able to interpret those values, e.g. as coordinates in a certain coordinate reference system.

- A reference system connection object must have a member `"components"` which has as value an array of component identifiers that are referenced in this object. Depending on the type of referencing, the ordering of the identifiers may be relevant, e.g. for 2D/3D coordinate reference systems.
- A reference system connection object must have a member `"system"` which has as value a reference system object. Section 5 defines common types of  reference system objects.

Example of a reference system connection object:
```js
{
  "components": ["y","x","z"],
  "system": {
    "type": "GeodeticCRS",
    "id": "http://www.opengis.net/def/crs/EPSG/0/4979"
  }
}
```

#### 6.1.3. Coordinate Space

- A coordinate space is defined by an array `[C1, C2, ..., Cn]` where each of `C1` to `Cn` is an array of coordinates. The number of elements in a coordinate space are `|C1| * |C2| * ... * |Cn|`, where a composite coordinate counts as a single coordinate. Each element in the space can be referenced by a unique number. A coordinate space assigns a unique number to `[c1, c2, ..., cn]` by assuming an `n`-dimensional array of shape `[|C1|, |C2|, ..., |Cn|]` stored in row-major order.
- The coordinate space of a domain object is defined by the array `[C1, C2, ..., Cn]` where `C1` is the `"values"` member corresponding to the first axis identifier in the `"rangeAxisOrder"` array, or any member if no `"rangeAxisOrder"` exists. `C2` corresponds to the second axis identifier in `"rangeAxisOrder"`, continuing until the last axis identifier `Cn`.

#### 6.1.4. Examples

Example of a domain object with [`"Grid"`](profiles.md) profile:
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
  "referencing": [{
    "components": ["t"],
    "system": {
      "type": "TemporalRS",
      "calendar": "Gregorian"
    }
  }, {
    "components": ["y","x","z"],
    "system": {
      "type": "GeodeticCRS",
      "id": "http://www.opengis.net/def/crs/EPSG/0/4979"
    }
  }]
}
```

Example of a domain object with [`"Trajectory"`](profiles.md) profile:
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
  "referencing": [{
    "components": ["t"],
    "system": {
      "type": "TemporalRS",
      "calendar": "Gregorian"
    }
  }, {
    "components": ["x","y"],
    "system": {
      "type": "GeodeticCRS",
      "id": "http://www.opengis.net/def/crs/OGC/1.3/CRS84"
    }
  }]
}
```


### 6.2. Range Objects

A CoverageJSON object with the type `"Range"` is a range object.

- A range object must have a member with the name `"values"` where the value is an array of numbers and nulls, or strings and nulls, where nulls represent missing data.
- A range object must have a member with the name `"dataType"` where the value is either `"float"`, `"integer"`, or `"string"` and must correspond to the data type of the non-null values in the `"values"` array. 
- Note that common JSON implementations may use 64-bit floating point numbers as data type for `"values"`, therefore precision has to be taken into account. For example, only integers within the extent [-2^32, 2^32] can be accurately represented with 64-bit floating point numbers.

Example:
```js
{
  "type": "Range",
  "values": [12.3, 12.5, 11.5, 23.1, null, null, 10.1],
  "dataType": "float"
}
```

### 6.3. Coverage Objects

A CoverageJSON object with the type `"Coverage"` is a coverage object.

- A coverage object may have the member `"profile"` with a string value to indicate that the coverage follows a certain structure (e.g. has a certain domain profile or restrictions on parameters). See the ["Common CoverageJSON Profiles Specification"](profiles.md), which forms part of this specification, for details. Custom profiles not part of this specification may be given by full URIs only.
- If a coverage has a commonly used identifier, that identifier should be included as a member of the coverage object with the name `"id"`.
- A coverage object must have a member with the name `"domain"` where the value is either a domain object or a URL.
- If the value of `"domain"` is a URL and the referenced domain has a `"profile"` member, then the coverage object must have the member `"domainProfile"` where the value must equal the `"profile"` value of the referenced domain.
- A coverage object may have a member with the name `"parameters"` where the value is an object where each member has as name a short identifier and as value a parameter object. The identifier corresponds to the commonly known concept of "variable name" and is merely used in clients for conveniently accessing the corresponding range object.
- A coverage object must have a `"parameters"` member if the coverage object is not part of a coverage collection or if the coverage collection does not have a `"parameters"` member.
- A coverage object must have a member with the name `"ranges"` where the value is a range set object. Any member of a range set object has as name any of the names in a `"parameters"` object in scope and as value either a range object or a URL. A `"parameters"` member in scope is either within the enclosing coverage object or, if part of a coverage collection, in the parent coverage collection object. The array elements of the `"values"` member of each range object must correspond to the coordinate space defined by `"domain"` in terms of element order and count. If the referenced parameter object has a `"categoryEncoding"` member, then each array element of the `"values"` member must be equal to one of the values defined in the `"categoryEncoding"` object and be interpreted as the matching category.

### 6.4. Coverage Collection Objects

A CoverageJSON object with the type `"CoverageCollection"` is a coverage collection object.

- A coverage collection object may have the member `"profile"` with a string value to indicate that the coverage collection follows a certain structure (e.g. only has coverages with a specific domain or coverage profile). See the ["Common CoverageJSON Profiles Specification"](profiles.md), which forms part of this specification, for details. Custom profiles not part of this specification may be given by full URIs only.
- A coverage collection object must have a member with the name `"coverages"`. The value corresponding to `"coverages"` is an array. Each element in the array is a coverage object as defined above.
- A coverage collection object may have a member with the name `"parameters"` where the value is an object where each member has as name a short identifier and as value a parameter object.
- A coverage collection object may have a member with the name `"referencing"` where the value is an array of reference system connection objects.

## 7. JSON-LD

A JSON-LD context may be established by including a `"@context"` element in the root of a CoverageJSON object which should refer to the base CoverageJSON context `"https://rawgit.com/reading-escience-centre/coveragejson/master/contexts/coveragejson-base.jsonld"` and any other necessary contexts. Domain axis values and range values should *not* be exposed as linked data since they are currently not suitable for such representation.

Example:
```js
{
  "@context": [
     "https://rawgit.com/reading-escience-centre/coveragejson/master/contexts/coveragejson-base.jsonld",
     {
      ...
     }
   ],
   "type": "Coverage",
   ...
}
```

All identifiers referred to in a CoverageJSON object should be URIs. These URIs should be taken from established vocabularies if available.

TODO expand

## 8. Resolving domain and range URLs

If a domain or range is referenced by a URL in a CoverageJSON document, then the client should, whenever is appropriate, load the data from the given URL and treat the loaded data as if it was directly embedded in place of the URL. When sending HTTP requests, the `Accept` header should be set appropriately to the CoverageJSON media type.

## 9. Media Type and File Extension

The CoverageJSON media type shall be `application/prs.coverage+json` with an optional parameter `profile` which is a non-empty list of space-separated URIs identifying specific constraints or conventions that apply to a CoverageJSON document according to [RFC6906](http://www.ietf.org/rfc/rfc6906.txt). The only profile URI defined in this document is `http://coveragejson.org/profiles/standalone` which asserts that all domain and range objects are directly embedded in a CoverageJSON document and not referenced by URLs.

The file extension shall be `covjson`.

## Appendix A. Coverage Examples

TODO, see http://reading-escience-centre.github.io/leaflet-coverage-demo/

## Attribution

This specification uses ideas and sentence structures from the [GeoJSON specification](http://geojson.org/geojson-spec.html) which is licensed under a [Creative Commons Attribution 3.0 United States License](http://creativecommons.org/licenses/by/3.0/us/).

## Acknowledgements

This work was inspired by a demonstration of the concept by Joan Masó of CREAF.
