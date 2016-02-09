
- Concise Binary Object Representation (CBOR) is defined in [IETF RFC 7049](http://tools.ietf.org/rfc/rfc7049.txt).

### 6.2

...
Note: When the offset/factor encoding (see section below) is used, then this type corresponds to the desired value type *after* applying the transformation, which currently can only be `"float"` in that case. 

- If the `"values"` array of a range object does not contain nulls, then for CBOR serializations typed arrays (as in RDFxxxx) should be used for increased space and parsing efficiency.

#### 6.2.1. Offset/Factor Encoding (CBOR-only)

A simple compression scheme typically used for storing low-resolution floating point data as small integers in binary formats is the offset/factor encoding. When using CBOR as serialization format, this encoding scheme may be used for the `"values"` array as described below.

- A range object may have both or none of the `"offset"` and `"factor"` members where the value of each is a number.
- If both `"offset"` and `"factor"` are present in a range object, then `"dataType"` must be `"float"`.
- If both `"offset"` and `"factor"` are present in a range object, each non-null value `v` in `"values"` must be converted to `v * factor + offset` when accessing it and all values in the `"values"` array must be integers or nulls. The converted value is always a floating point number and therefore this mechanism shall not be used for values that shall result in integers.
- If both `"offset"` and `"factor"` are present in a range object, then `"validMin"` and `"validMax"`, if existing, must be integers and be converted equally to the numbers in `"values"` when accessing them.

Example (JSON notation used for convenience only):
```js
{
  "type": "Range",
  "values": [1230, 1250, 1150, 2310, null, null, 1010],
  "factor": 100,
  "offset": 0,
  "validMin": 0,
  "validMax": 5000,
  "dataType": "float"
}

```

#### 6.2.2. Missing Value Encoding (CBOR-only)

If only a small amount of values in `"values"` are missing and the value type is numeric, then it is more space efficient to encode these missing values using a number outside the valid value extent (instead of null) so that CBOR's typed array representation for `"values"` can be applied.

- If a range object contains the `"validMin"` and `"validMax"` members and the value of `"dataType"` is `"integer"` or `"float"`, then the range object may have a member `"missing"` with value `"nonvalid"`.
- If a range object has the member `"missing"` with value `"nonvalid"`, then all missing values in `"values"` must be encoded as a number outside the `"validMin"`/`"validMax"` extent and interpreted as missing values.

Example (JSON notation used for convenience only):
```js
{
  "type": "Range",
  "values": [12.3, 12.5, 11.5, 23.1, 555.0, 555.0, 10.1],
  "validMin": 0.0,
  "validMax": 50.0,
  "missing": "nonvalid",
  "dataType": "float"
}
```

Example combining it with offset/factor encoding:
```js
{
  "type": "Range",
  "values": [1230, 1250, 1150, 2310, 55555, 55555, 1010],
  "factor": 100,
  "offset": 0,
  "validMin": 0,
  "validMax": 5000,
  "missing": "nonvalid",
  "dataType": "float"
}
```
In this case, the values array can be stored as a compact uint16 typed array in CBOR. 


## media types

`application/prs.coverage+cbor`

A software may only claim support for reading a CoverageJSON document encoded in CBOR if it can interpret the [typed array tags of CBOR](https://tools.ietf.org/html/draft-jroatch-cbor-tags-03).