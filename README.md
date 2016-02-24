# CoverageJSON

[![Join the chat at https://gitter.im/Reading-eScience-Centre/coveragejson](https://badges.gitter.im/Reading-eScience-Centre/coveragejson.svg)](https://gitter.im/Reading-eScience-Centre/coveragejson?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

This repository hosts the experimental CoverageJSON specification that is currently being developed.

**Feedback is very welcome!** Please use the [issues](https://github.com/Reading-eScience-Centre/coveragejson/issues) for any kind of discussion.

## What is CoverageJSON?

[CoverageJSON](https://github.com/Reading-eScience-Centre/coveragejson/blob/master/spec.md) is a data format for publishing [coverage data](https://en.wikipedia.org/wiki/Coverage_data) to the web in a web-friendly way.

## What is a coverage?

A coverage represents one or more spatio-temporal phenomenon. Some examples are:
- measurements from fixed weather stations over time (time series coverage)
- GPX sensor data like heart rate from a running tour (trajectory coverage)
- satellite images of different wavelengths (grid coverage)
- soil moisture measurements every 10cm from 0cm to 100cm depth at a fixed location (profile coverage)
- single average water salinity for a given area (polygon coverage)
- single water salinity measurement at a fixed location (point coverage)
- ...

## Why develop a new data format?

Consuming coverage data and visualizing it interactively in web clients is still not straight-forward. One reason is that existing formats are either unsuitable for the web (like netCDF files) or hard to interpret independently due to missing standard structures and metadata (e.g. the OPeNDAP protocol).

Increasingly, GeoJSON is used in various ways to attach coverage-like data to simple geometries (mostly points or polygons). However, such attempts often seem to bend the semantics of GeoJSON (using `properties` to attach data to inner geometry points, even though the geometry is meant as a single entity) and face the limits of it quite quickly (no grid geometry, no time coordinates, bad efficiency for big coverage domains).

With the advent of linked data, some measurement data is directly represented as [RDF triples](http://www.w3.org/TR/rdf11-concepts/), typically accessible via a [(Geo)SPARQL](http://geosparql.org) endpoint. While that approach is perfectly fine, there is still the question on how to represent big coverages (like grids) as triples, the current consensus being that it doesn't make much sense to do that. Another challenge is the flexibility of RDF and a missing standard vocabulary for coverage data, making it hard to write generic clients for it.

For all these reasons, the CoverageJSON format was developed which tries to fill these gaps, while being linked-data friendly, space-efficient, and easy to produce and consume.

## How can I use CoverageJSON?

While the format is still in development and changing, a range of libraries are developed for reading and visualizing CoverageJSON documents:
- [JavaScript API for Coverage Data](https://github.com/Reading-eScience-Centre/coverage-jsapi) - an abstraction over CoverageJSON for dealing with arbitrary coverage data in JavaScript
- [covjson-reader](https://github.com/Reading-eScience-Centre/covjson-reader) - JavaScript library that reads CoverageJSON from objects or URLs, exposing the data as JavaScript Coverage and CoverageCollection objects (see API above)
- [leaflet-coverage](https://github.com/Reading-eScience-Centre/leaflet-coverage) - [Leaflet](http://leafletjs.com) plugin for visualizing JavaScript Coverage objects, currently with support for [Common CoverageJSON Profiles](https://github.com/Reading-eScience-Centre/coveragejson/blob/master/profiles.md) only

However, as the format is so simple, it is easy to prototype an application from scratch. If you think the format is *not* simple enough, please open an issue and explain why you feel that way - it's a priority for us to make it simple!

## Alignment to existing standards

We consider a conceptual alignment with the [Coverage Implementation Schema](http://external.opengeospatial.org/twiki_public/CoveragesDWG/CoveragesBigPicture) (successor of "[GMLCOV](https://portal.opengeospatial.org/files/?artifact_id=48553)") important and follow its development closely. 

## Acknowledgments

This specification is developed within the [MELODIES project](http://www.melodiesproject.eu).
The format was inspired by an initial idea by Joan Masó of [CREAF](http://www.creaf.cat/ca) who also coined the name CoverageJSON.
