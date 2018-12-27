[domain-types]: domain-types.md

# The CoverageJSON Format Specification

WORK-IN-PROGRESS

<!-- Document Info -->
<table class="table">
  <tr>
    <th>Authors</th>
    <td>
      <a href="https://github.com/letmaik">Maik Riechert</a> (<a href="http://www.reading.ac.uk">University of Reading</a>),
      <a href="http://www.met.reading.ac.uk/users/users/1659">Jon Blower</a> (<a href="http://www.reading.ac.uk">University of Reading</a>)
    </td>
  </tr>
  <tr>
    <th>Revision</th>
    <td>0.2-draft</td>
  </tr>
  <tr>
    <th>Date</th>
    <td>xx yy 2016</td>
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

## Contents
{:.no_toc}

* TOC
{:toc}

## 1. Introduction

CoverageJSON is a format for encoding coverage data like grids, time series, and vertical profiles, distinguished by the geometry of their spatiotemporal domain. A CoverageJSON object represents a domain, a range, a coverage, or a collection of coverages. A range in CoverageJSON  represents coverage values. A coverage in CoverageJSON is the combination of a domain, parameters, ranges, and additional metadata. A coverage collection represents a list of coverages.

A complete CoverageJSON data structure is always an object (in JSON terms). In CoverageJSON, an object consists of a collection of name/value pairs -- also called members. For each member, the name is always a string. Member values are either a string, number, object, array or one of the literals: true, false, and null. An array consists of elements where each element is a value as described above.

### 1.1. Example

A CoverageJSON grid coverage of global air temperature:

```json
{
  "type" : "Coverage",
  "domain" : {
    "type": "Domain",
    "domainType": "Grid",
    "axes": {
      "x": { "start": -179.5, "stop": 179.5, "num": 360 },
      "y": { "start": -89.5, "stop": 89.5, "num": 180 },
      "t": { "values": ["2013-01-13T00:00:00Z"] }
    },
    "referencing": [{
      "coordinates": ["x","y"],
      "system": {
        "type": "GeographicCRS",
        "id": "http://www.opengis.net/def/crs/OGC/1.3/CRS84"        
      }
    }, {
      "coordinates": ["t"],
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
          "value": "Cel",
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
    "TEMP" : "http://example.com/coverages/123/TEMP"
  }
}
```
where `"http://example.com/coverages/123/TEMP"` points to the following document:

```json
{
  "type" : "NdArray",
  "dataType": "float",
  "axisNames": ["t","y","x"],
  "shape": [1, 180, 360],
  "values" : [ 27.1, 24.1, null, 25.1, ... ]
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

- CIS enforces exactly one coordinate reference system (CRS) per coverage, CoverageJSON allows CRSs to be associated with a given combination of coordinates.
- CIS has separate domain concepts for grids vs other types, CoverageJSON always uses collections of orthogonal axes for organizing domains, whether gridded or not.
- CIS has no specific model for describing categories of a categorical parameter, CoverageJSON defines such a model.
- CIS has no notion of semantically grouping parameters (e.g. velocity = speed + direction), CoverageJSON allows that.

### 1.3. Definitions

- JavaScript Object Notation (JSON), and the terms object, name, value, array, string, number, and null, are defined in [IETF RFC 4627](http://www.ietf.org/rfc/rfc4627.txt).
- JSON-LD is defined in [http://www.w3.org/TR/json-ld/](http://www.w3.org/TR/json-ld/).
- A compact URI has the form prefix:suffix.
- The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [IETF RFC 2119](http://www.ietf.org/rfc/rfc2119.txt).

## 2. i18n Objects

An i18n object represents a string in multiple languages where each key is a language tag as defined in [BCP 47](http://tools.ietf.org/html/bcp47), and the value is the string in that language.
The special language tag `"und"` can be used to identify a value whose language is unknown or undetermined.

Example:

```json
{
  "en": "Temperature",
  "de": "Temperatur"
}
```

## 3. Parameter Objects

Parameter objects represent metadata about the values of the coverage in terms of the observed property (like water temperature), the units, and others.

- A parameter object MAY have any number of members (name/value pairs).
- A parameter object MUST have a member with the name `"type"` and the value `"Parameter"`.
- A parameter object MAY have a member with the name `"id"` where the value MUST be a string and SHOULD be a common identifier.
- A parameter object MAY have a member with the name `"label"` where the value MUST be an i18n object that is the name of the parameter and which SHOULD be short. Note that this SHOULD be left out if it would be identical to the `"label"` of the `"observedProperty"` member.
- A parameter object MAY have a member with the name `"description"` where the value MUST be an i18n object which is a, perhaps lengthy, textual description of the parameter.
- A parameter object MUST have a member with the name `"observedProperty"` where the value is an object which MUST have the member `"label"` and which MAY have the members `"id"`, `"description"`, and `"categories"`. The value of `"label"` MUST be an i18n object that is the name of the observed property and which SHOULD be short. If given, the value of `"id"` MUST be a string and SHOULD be a common identifier. If given, the value of `"description"` MUST be an i18n object with a textual description of the observed property. If given, the value of `"categories"` MUST be a non-empty array of category objects. A category object MUST an `"id"` and a `"label"` member,  and MAY have a `"description"` member. The value of `"id"` MUST be a string and SHOULD be a common identifier. The value of `"label"` MUST be an i18n object of the name of the category and SHOULD be short. If given, the value of `"description"` MUST be an i18n object with a textual description of the category.
- A parameter object MAY have a member with the name `"categoryEncoding"` where the value is an object where each key is equal to an `"id"` value of the `"categories"` array within the `"observedProperty"` member of the parameter object. There MUST be no duplicate keys. The value is either an integer or an array of integers where each integer MUST be unique within the object.
- A parameter object MAY have a member with the name `"unit"` where the value is an object which MUST have either or both the members `"label"` or/and "`symbol`", and which MAY have the member `"id"`. If given, the value of `"symbol"` MUST either be a string of the symbolic notation of the unit, or an object with the members `"value"` and `"type"` where `"value"` is the symbolic unit notation and `"type"` references the unit serialization scheme that is used. `"type"` MUST HAVE the value `"http://www.opengis.net/def/uom/UCUM/`" if [UCUM](http://unitsofmeasure.org) is used, or a custom value as recommended in section "Extensions". If given, the value of `"label"` MUST be an i18n object of the name of the unit and SHOULD be short. If given, the value of `"id"` MUST be a string and SHOULD be a common identifier. It is RECOMMENDED to reference a unit serialization scheme to allow automatic unit conversion.
- A parameter object MUST NOT have a `"unit"` member if the `"observedProperty"` member has a `"categories"` member.


Example for a continuous-data parameter:

```json
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
      "value": "Cel",
      "type": "http://www.opengis.net/def/uom/UCUM/"
    }
  }
}
```

Example for a categorical-data parameter:

```json
{
  "type" : "Parameter",
  "description" : {
    "en": "The land cover category."
  },
  "observedProperty" : {
    "id" : "http://example.com/land_cover",
    "label" : {
      "en": "Land Cover"
    },
    "description" : {
      "en": "longer description..."
    },
    "categories": [{
      "id": "http://example.com/land_cover/categories/grass",
      "label": {
        "en": "Grass"
      },
      "description": {
        "en": "Very green grass."
      }
    }, {
      "id": "http://example.com/land_cover/categories/forest",
      "label": {
        "en": "Forest"
      }
    }]
  },
  "categoryEncoding": {
    "http://example.com/land_cover/categories/grass": 1,
    "http://example.com/land_cover/categories/forest": [2,3]
  }
}
```

## 4. ParameterGroup Objects

Parameter group objects represent logical groups of parameters, for example vector quantities.

- A parameter group object MAY have any number of members (name/value pairs).
- A parameter group object MUST have a member with the name `"type"` and the value `"ParameterGroup"`.
- A parameter group object MAY have a member with the name `"id"` where the value MUST be a string and SHOULD be a common identifier.
- A parameter group object MAY have a member with the name `"label"` where the value MUST be an i18n object that is the name of the parameter group and which SHOULD be short. Note that this SHOULD be left out if it would be identical to the `"label"` of the `"observedProperty"` member.
- A parameter group object MAY have a member with the name `"description"` where the value MUST be an i18n object which is a, perhaps lengthy, textual description of the parameter group.
- A parameter group object MAY have a member with the name `"observedProperty"` where the value is an object as specified for parameter objects.
- A parameter group object MUST have either or both the members `"label"` or/and `"observedProperty"`.
- A parameter group object MUST have a member with the name `"members"` where the value is a non-empty array of parameter identifiers (see 6.3 Coverage objects).

Example of a group describing a vector quantity:

```json
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

```json
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

```json
{
  "type" : "Parameter",
  "observedProperty" : {
    "label" : {
      "en": "Sea surface temperature daily mean"
    },
    "statisticalMeasure": "http://www.uncertml.org/statistics/mean",
    "statisticalPeriod": "P1D",
    "narrowerThan": ["http://vocab.nerc.ac.uk/standard_name/sea_surface_temperature/"]
  },
  "unit" : {
    "label": {
      "en": "Kelvin"
    },
    "symbol": {
      "value": "K",
      "type": "http://www.opengis.net/def/uom/UCUM/"
    }
  }
}
```

and `"SST_stddev"`:

```json
{
  "type" : "Parameter",
  "observedProperty" : {
    "label" : {
      "en": "Sea surface temperature standard deviation of daily mean"
    },
    "statisticalMeasure": "http://www.uncertml.org/statistics/standard-deviation",
    "narrowerThan": ["http://vocab.nerc.ac.uk/standard_name/sea_surface_temperature/"]
  },
  "unit" : {
    "label": {
      "en": "Kelvin"
    },
    "symbol": {
      "value": "K",
      "type": "http://www.opengis.net/def/uom/UCUM/"
    }
  }
}
```

## 5. Reference system objects
Reference system objects are used to provide information about how to interpret coordinate values within the domain. Coordinates are usually geospatial or temporal in nature, but may also be categorical (based on identifiers). All reference system objects MUST have a member `"type"`, the possible values of which are given in the sections below. Custom values MAY be used as detailed in the "Extensions" section below.

### 5.1. Geospatial Coordinate Reference Systems
Geospatial coordinate reference systems (CRSs) link coordinate values to the Earth.

#### 5.1.1 Geographic Coordinate Reference Systems
Geographic CRSs anchor coordinate values to an ellipsoidal approximation of the Earth. They have coordinate axes of geodetic longitude and geodetic latitude, and perhaps height above the ellipsoid (i.e. they can be two- or three-dimensional). The origin of the CRS is on the surface of the ellipsoid.

 - The value of the `"type"` member MUST be "GeographicCRS"
 - The object MAY have an `"id"` member, whose value MUST be a string and SHOULD be a common identifier for the reference system.

Note that sometimes (e.g. for numerical model data) the exact CRS may not be known or may be undefined. In this case the `"id"` may be omitted, but the `"type"` still indicates that this is a geographic CRS. Therefore clients can still use geodetic longitude, geodetic latitude (and maybe height) axes, even if they can't accurately georeference the information.

If a Coverage conforms to one of the defined [domain types][domain-types] then the coordinate identifier `"x"` is used to denote geodetic longitude, `"y"` is used for geodetic latitude and `z` for ellipsoidal height.

Example of a two-dimensional geographic CRS (longitude-latitude):

```json
{
  "type": "GeographicCRS",
  "id": "http://www.opengis.net/def/crs/OGC/1.3/CRS84"
}
```

Example of a three-dimensional geographic CRS (latitude-longitude-height):

```json
{
  "type": "GeographicCRS",
  "id": "https://www.epsg-registry.org/export.htm?wkt=urn:ogc:def:crs:EPSG::4979"
}
```

#### 5.1.2 Projected Coordinate Reference Systems
Projected CRSs use two coordinates to denote positions on a Cartesian plane, which is derived from projecting the ellipsoid according to some defined transformation.

 - The value of the `"type"` member MUST be "ProjectedCRS"
 - The object MAY have an `"id"` member, whose value MUST be a string and SHOULD be a common identifier for the reference system.

If a Coverage conforms to one of the defined [domain types][domain-types] then the coordinate identifier `"x"` is used to denote easting and `"y"` is used for northing.

Example of a projected CRS (here [British National Grid](https://www.epsg-registry.org/export.htm?wkt=urn:ogc:def:crs:EPSG::27700)):

```json
{
  "type": "ProjectedCRS",
  "id": "https://www.epsg-registry.org/export.htm?wkt=urn:ogc:def:crs:EPSG::27700"
}
```

#### 5.1.3 Vertical Coordinate Reference Systems
Vertical CRSs use a single coordinate to denote some measure of height or depth, usually approximately oriented with gravity.

- The value of the `"type"` member MUST be "VerticalCRS"
- The object MAY have an `"id"` member, whose value MUST be a string and SHOULD be a common identifier for the reference system.

Example of a vertical CRS, here representing height above the NAV88 datum:

```json
{
  "type": "VerticalCRS",
  "id": "https://www.epsg-registry.org/export.htm?wkt=urn:ogc:def:crs:EPSG::5703"
}
```

#### 5.1.4 Providing inline definitions of CRSs
Sometimes there may be no well-known identifier for a geospatial CRS. Or the data provider may wish to make the CoverageJSON file more self-contained by avoiding external lookups. In this case a full inline definition of the CRS in JSON (instead of, or in addition to the `"id"`). This has not yet been fully defined in this specification, but we recommend following the OGC Well-Known Text (WKT) structure, for example:

```json
{
  "type": "VerticalCRS",
  "id": "http://www.opengis.net/def/crs/EPSG/0/5703",
  "datum": {
    "id": "http://www.opengis.net/def/datum/EPSG/0/5103",
    "label": {
      "en": "North American Vertical Datum 1988"
    }
  },
  "cs": {
    "id": "http://www.opengis.net/def/cs/EPSG/0/6499",
    "csAxes": [{
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

In future work, a mapping from OGC WKT2 to JSON may be defined, and may be adopted into the CoverageJSON specification.


### 5.2. Temporal Reference Systems

Time is referenced by a temporal reference system (temporal RS).
In this specification, only a string-based notation for time values is defined.

- A temporal RS object MUST have a member `"type"`. The only currently defined value of it is `"TemporalRS"`.
- A temporal RS object MUST have a member `"calendar"` with value `"Gregorian"` or a URI.
- If the Gregorian calender is used, then `"calendar"` MUST have the value `"Gregorian"` and cannot be a URI.
- A temporal RS object MAY have a member `"timeScale"` with a URI as value.
  If omitted, the time scale defaults to UTC (`"http://www.opengis.net/def/trs/BIPM/0/UTC"`).
  If the time scale is UTC, the `"timeScale"` member MUST be omitted.
- If the calendar is based on years, months, days, then the referenced values SHOULD use one of the following
  ISO8601-based lexical representations:
  - YYYY
  - ±XYYYY (where X stands for extra year digits)
  - YYYY-MM
  - YYYY-MM-DD
  - YYYY-MM-DDTHH:MM:SS[.F]Z where Z is either "Z" or a time scale offset +|-HH:MM
- If calendar dates with reduced precision are used in a lexical representation (e.g. `"2016"`), then
  a client SHOULD interpret those dates in that reduced precision.

Example:
ep
```json
{
  "type": "TemporalRS",
  "calendar": "Gregorian"
}
```

### 5.3. Identifier-based Reference Systems

Identifier-based reference systems (identifier RS) .

- An identifier RS object MUST have a member `"type"` with value `"IdentifierRS"`.
- An identifier RS object MAY have a member `"id"` where the value MUST be a string and SHOULD be a common identifier for the reference system.
- An identifier RS object MAY have a member `"label"` where the value MUST be an i18n object that is the name of the reference system.
- An identifier RS object MAY have a member `"description"` where the value MUST be an i18n object that is the (perhaps lengthy) description of the reference system.
- An identifier RS object MUST have a member `"targetConcept"` where the value is an object that MUST have a member `"label"` and MAY have a member `"description"` where the value of each MUST be an i18n object that is the name or description, respectively, of the concept which is referenced in the system.
- An identifier RS object MAY have a member `"identifiers"` where the value is an object where each key is an identifier referenced by the identifier RS and each value an object describing the referenced concept, equal to `"targetConcept"`.
- Coordinate values associated with an identifier RS MUST be strings.

Example of a geographic identifier reference system:

```json
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

- The CoverageJSON object MAY have any number of members (name/value pairs).
- The CoverageJSON object MUST have a member with the name `"type"` which has as value one of: `"Domain"`, `"NdArray"` (a range encoding), `"TiledNdArray"` (a range encoding), `"Coverage"`, or `"CoverageCollection"`. The case of the type member values MUST be as shown here.

### 6.1. Domain Objects

A domain object is a CoverageJSON object which defines a set of positions and their extent in one or more referencing systems.
Its general structure is:

```json
{
  "type": "Domain",
  "domainType": "...",
  "axes": { ... },
  "referencing": [...]
}
```

- The value of the `"type"` member MUST be `"Domain"`.
- For interoperability reasons it is RECOMMENDED that a domain object has the member `"domainType"` with a string value to indicate that the domain follows a certain structure (e.g. a time series, a vertical profile, a spatio-temporal 4D grid). See the ["Common CoverageJSON Domain Types Specification"][domain-types], which forms part of this specification, for details. Custom domain types may be used as recommended in the section "Extensions".
- A domain object MUST have the member `"axes"` which has as value an object where each key is an axis identifier and each value an axis object as defined below.
- A domain object MAY have the member `"referencing"` where the value is an array of reference system connection objects as defined below.
- A domain object MUST have a `"referencing"` member if the domain object is not part of a coverage collection or if the coverage collection does not have a `"referencing"` member.

#### 6.1.1. Axis Objects

- An axis object MUST have either a `"values"` member or, as a compact notation for a regularly spaced numeric axis, all the members `"start"`, `"stop"`, and `"num"`.
- The value of `"values"` is a non-empty array of axis values.
- The values of `"start"` and `"stop"` MUST be numbers, and the value of `"num"` an integer greater than zero. If the value of `"num"` is `1`, then `"start"` and `"stop"` MUST have identical values. For `num > 1`, the array elements of `"values"` MAY be reconstructed with the formula `start + i * step` where `i` is the ith element and in the interval `[0, num-1]` and `step = (stop - start) / (num - 1)`. If `num = 1` then `"values"` is `[start]`. Note that `"start"` can be greater than `"stop"` in which case the axis values are descending.
- The value of `"dataType"` determines the structure of an axis value and its coordinates that are made available for referencing. The values of `"dataType"` defined in this specification are `"primitive"`, `"tuple"`, and `"polygon"`. Custom values MAY be used as detailed in the "Extensions" section. For `"primitive"`, there is a single coordinate identifier and each axis value MUST be a number or string. For `"tuple"`, each axis value MUST be an array of fixed size of primitive values in a defined order, where the tuple size corresponds to the number of coordinate identifiers. For `"polygon"`, each axis value MUST be a GeoJSON Polygon coordinate array, where the order of coordinates is given by the `"coordinates"` array.
- If missing, the member `"dataType"` defaults to `"primitive"` and MUST not be included for that default case.
- If `"dataType"` is `"primitive"` and the associated reference system (see 6.1.2) defines a natural ordering of values then the array values in `"values"`, if existing, MUST be ordered monotonically, that is, increasing or decreasing.
- The value of `"coordinates"` is a non-empty array of coordinate identifiers corresponding to the order of the coordinates defined by `"dataType"`.
- If missing, the member `"coordinates"` defaults to a one-element array of the axis identifier and MUST NOT be included for that default case.
- A coordinate identifier SHALL NOT be defined more than once in all axis objects of a domain object.
- An axis object MAY have axis value bounds defined in the member `"bounds"` where the value is an array of values of length `len*2` with `len` being the length of the `"values"` array. For each axis value at array index `i` in the `"values"` array, a lower and upper bounding value at positions `2*i` and `2*i+1`, respectively, are given in the bounds array.
- If a domain axis object has no `"bounds"` member then a bounds array MAY be derived automatically.

Example of an axis object with bounds:

```json
{
  "values": [20,21],
  "bounds": [19.5,20.5,
             20.5,21.5]
}
```

Example of an axis object with regular axis encoding:

```json
{
  "start": 0,
  "stop": 5,
  "num": 6
}
```
The axis values in the above example are equal to `"values": [0,1,2,3,4,5]`.

Example of an axis object with tuple values:

```json
{
  "dataType": "tuple",
  "coordinates": ["t","x","y"],  
  "values": [
    ["2008-01-01T04:00:00Z",1,20],
    ["2008-01-01T04:30:00Z",2,21]
  ]
}
```

Example of an axis object with Polygon values:

```json
{
  "dataType": "polygon",
  "coordinates": ["x","y"],
  "values": [
    [ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]  ]
  ]
}
```

#### 6.1.2. Reference System Connection Objects

A reference system connection object creates a link between values within domain axes and a reference system to be able to interpret those values, e.g. as coordinates in a certain coordinate reference system.

- A reference system connection object MUST have a member `"coordinates"` which has as value an array of coordinate identifiers that are referenced in this object. Depending on the type of referencing, the ordering of the identifiers MAY be relevant, e.g. for 2D/3D coordinate reference systems. In this case, the order of the identifiers MUST match the order of axes in the coordinate reference system.
- A reference system connection object MUST have a member `"system"` whose value MUST be a Reference System Object (defined in section 5 above).

Example of a reference system connection object:

```json
{
  "coordinates": ["y","x","z"],
  "system": {
    "type": "GeographicCRS",
    "id": "https://www.epsg-registry.org/export.htm?wkt=urn:ogc:def:crs:EPSG::4979"
  }
}
```

#### 6.1.3. Examples

Example of a domain object with [`"Grid"`][domain-types] domain type:

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
  "referencing": [{
    "coordinates": ["t"],
    "system": {
      "type": "TemporalRS",
      "calendar": "Gregorian"
    }
  }, {
    "coordinates": ["y","x","z"],
    "system": {
      "type": "GeographicCRS",
      "id": "https://www.epsg-registry.org/export.htm?wkt=urn:ogc:def:crs:EPSG::4979"
    }
  }]
}
```

Example of a domain object with [`"Trajectory"`][domain-types] domain type:

```json
{
  "type": "Domain",
  "domainType": "Trajectory",
  "axes": {
    "composite": {
      "dataType": "tuple",
      "coordinates": ["t","x","y"],
      "values": [
        ["2008-01-01T04:00:00Z", 1, 20],
        ["2008-01-01T04:30:00Z", 2, 21]
      ]
    }
  },
  "referencing": [{
    "coordinates": ["t"],
    "system": {
      "type": "TemporalRS",
      "calendar": "Gregorian"
    }
  }, {
    "coordinates": ["x","y"],
    "system": {
      "type": "GeographicCRS",
      "id": "http://www.opengis.net/def/crs/OGC/1.3/CRS84"
    }
  }]
}
```

### 6.2. NdArray Objects

A CoverageJSON object with the type `"NdArray"` is an NdArray object. It represents a multidimensional (>= 0D) array with named axes, encoded as a flat one-dimensional array in row-major order.

- An NdArray object MUST have a member with the name `"values"` where the value is an array of numbers and nulls, or strings and nulls, where nulls represent missing data.
- An NdArray object MUST have a member with the name `"dataType"` where the value is either `"float"`, `"integer"`, or `"string"` and MUST correspond to the data type of the non-null values in the `"values"` array.
- An NdArray object MAY have a member with the name `"shape"` where the value is an array of integers. For 0D arrays, `"shape"` MAY be omitted (defaulting to `[]`), for >= 1D arrays it MUST be included.
- An NdArray object MAY have a member with the name `"axisNames"` where the value is a string array of the same length as `"shape"`. For 0D arrays, `"axisNames"` MAY be omitted (defaulting to `[]`), for >= 1D arrays it MUST be included.
- Note that common JSON implementations use 64-bit floating point numbers as data type for `"values"`, therefore precision has to be taken into account. For example, only integers within the extent [-2^32, 2^32] can be accurately represented with 64-bit floating point numbers.

Example:

```json
{
  "type": "NdArray",
  "dataType": "float",
  "shape": [4, 2],
  "axisNames": ["y", "x"],
  "values": [
    12.3, 12.5, 11.5, 23.1,
    null, null, 10.1, 9.1
  ]  
}
```

### 6.3. TiledNdArray Objects

A CoverageJSON object with the type `"TiledNdArray"` is a TiledNdArray object. It represents a multidimensional (>= 1D) array with named axes that is split up into sets of linked NdArray documents. Each tileset typically covers a specific data access scenario, for example, loading a single time slice of a grid vs. loading a time series of a spatial subset of a grid.

- A TiledNdArray object MUST have a member with the name `"dataType"` where the value is either `"float"`, `"integer"`, or `"string"`.
- A TiledNdArray object MUST have a member with the name `"shape"` where the value is a non-empty array of integers.
- A TiledNdArray object MUST have a member with the name `"axisNames"` where the value is a string array of the same length as `"shape"`.
- A TiledNdArray object MUST have a member with the name `"tileSets"` where the value is a non-empty array of TileSet objects.
- A TileSet object MUST have a member with the name `"tileShape"` where the value is an array of the same length as `"shape"` and where each array element is either null or an integer lower or equal than the corresponding element in `"shape"`. A null value denotes that the axis is not tiled.
- A TileSet object MUST have a member with the name `"urlTemplate"` where the value is a Level 1 URI template as defined in [RFC 6570](https://tools.ietf.org/html/rfc6570). The URI template MUST contain a variable for each axis name whose corresponding element in `"tileShape"` is not null. A variable for an axis of total size `totalSize` (from `"shape"`) and tile size `tileSize` (from `"tileShape"`) has as value one of the integers `0, 1, ..., q + r - 1` where `q` and `r` are the quotient and remainder obtained by dividing `totalSize` by `tileSize`. Each URI that can be generated from the URI template MUST resolve to an NdArray CoverageJSON document where the members `"dataType"` and `"axisNames`" are identical to the ones of the TiledNdArray object, and where each value of `"shape"` is an integer equal, or lower if an edge tile, to the corresponding element in `"tileShape"` while replacing null with the corresponding element of `"shape"` of the TiledNdArray.

Example:

```json
{
  "type" : "TiledNdArray",
  "dataType": "integer",
  "axisNames": ["t", "y", "x"],
  "shape": [2, 5, 10],
  "tileSets": [{
    "tileShape": [null, null, null],
    "urlTemplate": "http://example.com/a/all.covjson"
  }, {
    "tileShape": [1, null, null],
    "urlTemplate": "http://example.com/b/{t}.covjson"
  }, {
    "tileShape": [null, 2, 3],
    "urlTemplate": "http://example.com/c/{y}-{x}.covjson"
  }]
}
```

`http://example.com/a/all.covjson`:

```json
{
  "type": "NdArray",
  "dataType": "integer",
  "axisNames": ["t", "y", "x"],
  "shape": [2, 5, 10],
  "values": [
     1,  2,  3,  4,  5,  6,  7,  8,  9, 10,
    11, 12, 13, 14, 15, 16, 17, 18, 19, 20,
    21, 22, 23, 24, 25, 26, 27, 28, 29, 30,
    31, 32, 33, 34, 35, 36, 37, 38, 39, 40,
    41, 42, 43, 44, 45, 46, 47, 48, 49, 50,

    51, 52, 53, 54, 55, 56, 57, 58, 59, 60,
    61, 62, 63, 64, 65, 66, 67, 68, 69, 70,
    71, 72, 73, 74, 75, 76, 77, 78, 79, 80,
    81, 82, 83, 84, 85, 86, 87, 88, 89, 90,
    91, 92, 93, 94, 95, 96, 97, 98, 99, 100
  ]
}
```

`http://example.com/b/0.covjson`:

```json
{
  "type": "NdArray",
  "dataType": "integer",
  "axisNames": ["t", "y", "x"],
  "shape": [1, 5, 10],
  "values": [
     1,  2,  3,  4,  5,  6,  7,  8,  9, 10,
    11, 12, 13, 14, 15, 16, 17, 18, 19, 20,
    21, 22, 23, 24, 25, 26, 27, 28, 29, 30,
    31, 32, 33, 34, 35, 36, 37, 38, 39, 40,
    41, 42, 43, 44, 45, 46, 47, 48, 49, 50
  ]
}
```

`http://example.com/c/0-0.covjson`:

```json
{
  "type": "NdArray",
  "dataType": "integer",
  "axisNames": ["t", "y", "x"],
  "shape": [2, 2, 3],
  "values": [
     1,  2,  3,
    11, 12, 13,

    51, 52, 53,
    61, 62, 63
  ]
}
```

`http://example.com/c/0-3.covjson`:

```json
{
  "type": "NdArray",
  "dataType": "integer",
  "axisNames": ["t", "y", "x"],
  "shape": [2, 2, 1],
  "values": [
    10,
    20,

    60,
    70
  ]
}
```

### 6.4. Coverage Objects

A CoverageJSON object with the type `"Coverage"` is a coverage object.

- If a coverage has a commonly used identifier, that identifier SHOULD be included as a member of the coverage object with the name `"id"`.
- A coverage object MUST have a member with the name `"domain"` where the value is either a domain object or a URL.
- If the value of `"domain"` is a URL and the referenced domain has a `"domainType"` member, then the coverage object SHOULD have the member `"domainType"` where the value MUST equal that of the referenced domain.
- If the coverage object is part of a coverage collection which has a `"domainType"` member then that member SHOULD be omitted in the coverage object.
- A coverage object MAY have a member with the name `"parameters"` where the value is an object where each member has as name a short identifier and as value a parameter object. The identifier corresponds to the commonly known concept of "variable name" and is merely used in clients for conveniently accessing the corresponding range object.
- A coverage object MUST have a `"parameters"` member if the coverage object is not part of a coverage collection or if the coverage collection does not have a `"parameters"` member.
- A coverage object MAY have a member with the name `"parameterGroups"` where the value is an array of ParameterGroup objects.
- A coverage object MUST have a member with the name `"ranges"` where the value is a range set object. Any member of a range set object has as name any of the names in a `"parameters"` object in scope and as value either an NdArray or TiledNdArray object or a URL resolving to a CoverageJSON document of such object. A `"parameters"` member in scope is either within the enclosing coverage object or, if part of a coverage collection, in the parent coverage collection object. The shape and axis names of each NdArray or TiledNdArray object MUST correspond to the domain axes defined by `"domain"`, while single-valued axes MAY be omitted. If the referenced parameter object has a `"categoryEncoding"` member, then each non-null array element of the `"values"` member of the NdArray object, or the linked NdArray objects within a TiledNdArray object, MUST be equal to one of the values defined in the `"categoryEncoding"` object and be interpreted as the matching category.
- A coverage object MAY have a member with the name `"rangeAlternates"` where the value is a range alternates object. Any member of a range alternates object has as name a format identifier and a value that is defined by the format definition.
- Note that it is allowed to have an empty `"ranges"` member object. This can make sense if `"rangeAlternates"` is given and clients are expected to have support for the alternative range format(s).

Example:

See the [Vertcal Profile Coverage Example](https://covjson.org/spec/#vertical-profile-coverage)

### 6.5. Coverage Collection Objects

A CoverageJSON object with the type `"CoverageCollection"` is a coverage collection object.

- A coverage collection object MAY have the member `"domainType"` with a string value to indicate that the coverage collection only contains coverages of the given domain type. See the ["Common CoverageJSON Domain Types Specification"][domain-types], which forms part of this specification, for details. Custom domain types may be used as recommended in the section "Extensions".
- If a coverage collection object has the member `"domainType"`, then this member is inherited to all included coverages.
- A coverage collection object MUST have a member with the name `"coverages"`. The value corresponding to `"coverages"` is an array. Each element in the array is a coverage object as defined above.
- A coverage collection object MAY have a member with the name `"parameters"` where the value is an object where each member has as name a short identifier and as value a parameter object.
- A coverage collection object MAY have a member with the name `"parameterGroups"` where the value is an array of ParameterGroup objects.
- A coverage collection object MAY have a member with the name `"referencing"` where the value is an array of reference system connection objects.

Example:

See the [Coverage Collection Example](https://covjson.org/spec/#coverage-collection)

## 7. Extensions

A CoverageJSON document can be extended with custom members and types in a robust and interoperable way. For that, it makes use of absolute URIs and compact URIs (prefix:suffix) in order to avoid conflicts with other extensions and future versions of the format. A central registry of compact URI prefixes is provided which anyone can extend and which is a simple mapping from compact URI prefix to namespace URI in order to avoid collisions with other extensions that are based on compact URIs as well. Extensions that do not follow this approach MAY use simple names instead of absolute or compact URIs but have to accept the consequence of the document being less interoperable and future-proof. In certain use cases this is not an issue and may be a preferred solution for simplicity reasons, for example, if such CoverageJSON documents are only used internally and are not meant to be shared to a wider audience.

### 7.1. Custom members

If a custom member is added to a CoverageJSON document, its name SHOULD be a compact URIs of the form `"prefix:suffix"`.

Example:

```json
{
  "type" : "Coverage",
  "dct:license": "https://creativecommons.org/licenses/by/4.0/",
  ...
}
```

The prefix SHOULD be registered at <https://covjson.org/prefixes/> which in the example above would be `dct = http://purl.org/dc/terms/`.

If the value of a custom member can have multiple structures, for example a string or an object, then a client should ignore the member if it does not understand the structure that is used.

Example of a different value structure:

```json
{
  "type" : "Coverage",
  "dct:license": {
    "id": "https://creativecommons.org/licenses/by/4.0/",
    "label": {
      "en": "Creative Commons Attribution 4.0 International License"
    }
  },
  ...
}
```

### 7.2. Custom types

Custom types MAY be used with the following members:

- `"domainType"` in domain objects
- `"dataType"` in axis objects
- `"type"` in reference system objects
- `"type"` in unit symbol objects
- `"type"` within custom members that have an object as value

The custom value of those members SHOULD be either an absolute URI or a compact URI. If a compact URI is used, then the prefix SHOULD be registered at <https://covjson.org/prefixes/>.

Example of a custom unit symbol type using an absolute URI:

```json
{
  "type" : "Parameter",
  "unit" : {
    "symbol": {
      "value": "degreeC",
      "type": "http://www.opengis.net/def/uom/UDUNITS/"
    }
  },
  "observedProperty" : {
    "label" : {
      "en": "Air temperature"
    }
  }
}
```

Example of a custom reference system type using a compact URI:

```json
{
  "type": "uor:HEALPixRS",
  "uor:h": 3,
  "uor:k": 3,
  "uor:ordering": "nested"
}
```


## 8. JSON-LD

If no JSON-LD context is given, then the default context `https://covjson.org/context.jsonld` SHALL be assumed. Note that this context includes [registered namespace prefixes](https://covjson.org/prefixes/) and MAY be updated in a backwards-compatible way as the format evolves.

Additional semantics not provided by the default context MAY be provided by specifying an explicit `"@context"` member in the root of a CoverageJSON document. The value of that member MUST be an array where the first element is the default context URL. Any additional context definitions SHALL NOT override definitions of the default context, except when the definition is identical.

Providing an explicit context is especially useful for extensions. A recommended practice is to include any used namespace prefixes, even if registered, in the explicit context. This provides additional clarity and helps humans understand the document more quickly.

It is NOT RECOMMENDED to use the explicit JSON-LD context to map simple names, for example, `"license": "dct:license"`. On one side, this would hinder interoperability for generic non-JSON-LD clients, as they generally rely on absolute URIs or [registered prefixes](https://covjson.org/prefixes/) of compact URIs. On the other side, it would make documents less future-proof as there may be name collisions with future versions of the format where semantics of that name may be defined differently. It is therefore RECOMMENDED to use compact or absolute URIs if an explicit JSON-LD context is included.

Note that domain axis values and range values SHOULD NOT be exposed as linked data via the JSON-LD context since they are not suitable for such representation.

Example:

```json
{
  "@context": [
    "https://covjson.org/context.jsonld",
    {
      "dct": "http://purl.org/dc/terms/",
      "dct:license": { "@type": "@id" }
    }
  ],
  "type" : "Coverage",
  "dct:license": "https://creativecommons.org/licenses/by/4.0/",
   ...
}
```

In this example, additional semantics for the registered `dct` prefix are provided by stating that the `"dct:license"` member value in this document is an identifier and not just an unstructured string.

## 9. Resolving domain and range URLs

If a domain or range is referenced by a URL in a CoverageJSON document, then the client should, whenever is appropriate, load the data from the given URL and treat the loaded data as if it was directly embedded in place of the URL. When sending HTTP requests, the `Accept` header SHOULD be set appropriately to the CoverageJSON media type.

## 10. Media Type and File Extension

The CoverageJSON media type SHALL be `application/prs.coverage+json` with an optional parameter `profile` which is a non-empty list of space-separated URIs identifying specific constraints or conventions that apply to a CoverageJSON document according to [RFC6906](http://www.ietf.org/rfc/rfc6906.txt). The only profile URI defined in this document is `https://covjson.org/def/core#standalone` which asserts that all domain and range objects are directly embedded in a CoverageJSON document and not referenced by URLs. There is no `charset` parameter and CoverageJSON documents MUST be serialized using the UTF-8 character encoding.

The file extension SHALL be `covjson`.

## Appendix A. Coverage Examples

### Vertical Profile Coverage

```json
{
  "type" : "Coverage",
  "domain" : {
    "type" : "Domain",
    "domainType" : "VerticalProfile",
    "axes": {
      "x" : { "values": [-10.1] },
      "y" : { "values": [ -40.2] },
      "z" : { "values": [
              5.4562, 8.9282, 14.8802, 20.8320, 26.7836, 32.7350,
              38.6863, 44.6374, 50.5883, 56.5391, 62.4897, 68.4401,
              74.3903, 80.3404, 86.2902, 92.2400, 98.1895, 104.1389,
              110.0881, 116.0371, 121.9859 ] },
      "t" : { "values": ["2013-01-13T11:12:20Z"] }
    },
    "referencing": [{
      "coordinates": ["x","y"],
      "system": {
        "type": "GeographicCRS",
        "id": "http://www.opengis.net/def/crs/OGC/1.3/CRS84"
      }
    }, {
      "coordinates": ["z"],
      "system": {
        "type": "VerticalCRS",
        "cs": {
          "csAxes": [{
            "name": {
              "en": "Pressure"
            },
            "direction": "down",
            "unit": {
              "symbol": "Pa"
            }
          }]
        }
      }
    }, {
      "coordinates": ["t"],
      "system": {
        "type": "TemporalRS",
        "calendar": "Gregorian"
      }
    }]
  },
  "parameters" : {
    "PSAL": {
      "type" : "Parameter",
      "description" : {
        "en": "The measured salinity, in practical salinity units (psu) of the sea water "
      },
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
      "description" : {
        "en": "The potential temperature, in degrees celcius, of the sea water"
      },
      "unit" : {
        "symbol" : "°C"
      },
      "observedProperty" : {
        "id" : "http://vocab.nerc.ac.uk/standard_name/sea_water_potential_temperature/",
        "label" : {
          "en": "Sea Water Potential Temperature"
        }
      }
    }
  },
  "ranges" : {
    "PSAL" : {
      "type" : "NdArray",
      "dataType": "float",
      "shape": [21],
      "axisNames": ["z"],
      "values" : [ 43.9599, 43.9599, 43.9640, 43.9640, 43.9679, 43.9879, 44.0040,
                   44.0120, 44.0120, 44.0159, 44.0320, 44.0320, 44.0480, 44.0559,
                   44.0559, 44.0579, 44.0680, 44.0740, 44.0779, 44.0880, 44.0940 ]
    },
    "POTM" : {
      "type" : "NdArray",
      "dataType": "float",
      "shape": [21],
      "axisNames": ["z"],
      "values" : [ 23.8, 23.7, 23.5, 23.4, 23.2, 22.4, 21.8,
                   21.7, 21.5, 21.3, 21.0, 20.6, 20.1, 19.7,
                   19.4, 19.1, 18.9, 18.8, 18.7, 18.6, 18.5 ]
    }
  }
}
```

### Coverage Collection

```json
{
  "type" : "CoverageCollection",
  "domainType" : "VerticalProfile",
  "parameters" : {
    "PSAL": {
      "type" : "Parameter",
      "description" : {
        "en": "The measured salinity, in practical salinity units (psu) of the sea water"
      },
      "unit" : {
        "symbol" : "psu"
      },
      "observedProperty" : {
        "id": "http://vocab.nerc.ac.uk/standard_name/sea_water_salinity/",
        "label" : {
          "en": "Sea Water Salinity"
        }
      }
    }
  },
  "referencing": [{
    "coordinates": ["x","y"],
    "system": {
      "type": "GeographicCRS",
      "id": "http://www.opengis.net/def/crs/OGC/1.3/CRS84"
    }
  }, {
    "coordinates": ["z"],
    "system": {
      "type": "VerticalCRS",
      "cs": {
        "csAxes": [{
          "name": {
            "en": "Pressure"
          },
          "direction": "down",
          "unit": {
            "symbol": "Pa"
          }
        }]
      }
    }
  }, {
    "coordinates": ["t"],
    "system": {
      "type": "TemporalRS",
      "calendar": "Gregorian"
    }
  }],
  "coverages": [
    {
      "type" : "Coverage",
      "domain" : {
        "type": "Domain",
        "axes": {
          "x": { "values": [-10.1] },
          "y": { "values": [-40.2] },
          "z": { "values": [ 5, 8, 14 ] },
          "t": { "values": ["2013-01-13T11:12:20Z"] }
        }
      },
      "ranges" : {
        "PSAL" : {
          "type" : "NdArray",
          "dataType": "float",
          "shape": [3],
          "axisNames": ["z"],
          "values" : [ 43.7, 43.8, 43.9 ]
        }
      }
    }, {
      "type" : "Coverage",
      "domain" : {
        "type": "Domain",
        "axes": {
          "x": { "values": [-11.1] },
          "y": { "values": [-45.2] },
          "z": { "values": [ 4, 7, 9 ] },
          "t": { "values": ["2013-01-13T12:12:20Z"] }
        }
      },
      "ranges" : {
        "PSAL" : {
          "type" : "NdArray",
          "dataType": "float",
          "shape": [3],
          "axisNames": ["z"],
          "values" : [ 42.7, 41.8, 40.9 ]
        }
      }
    }]
}
```

## Attribution

This specification uses ideas and sentence structures from the [GeoJSON specification](http://geojson.org/geojson-spec.html) which is licensed under a [Creative Commons Attribution 3.0 United States License](http://creativecommons.org/licenses/by/3.0/us/).

## Acknowledgements

This work was inspired by a demonstration of the concept by Joan Masó of CREAF.
