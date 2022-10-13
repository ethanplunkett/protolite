# protolite

> Highly Optimized Protocol Buffer Serializers

[![CRAN_Status_Badge](http://www.r-pkg.org/badges/version/protolite)](https://cran.r-project.org/package=protolite)
[![CRAN RStudio mirror downloads](http://cranlogs.r-pkg.org/badges/protolite)](https://cran.r-project.org/package=protolite)

Pure C++ implementations for reading and writing several common data formats 
based on Google protocol-buffers. Currently supports 'rexp.proto' for serialized 
R objects, 'geobuf.proto' for binary geojson, and 'mvt.proto' for vector tiles. 
This package uses auto-generated C++ code by protobuf-compiler, hence the 
entire serialization is optimized at compile time. The 'RProtoBuf' package on 
the other hand uses the protobuf runtime library to provide a general-purpose 
toolkit for reading and writing arbitrary protocol-buffer data in R.

## RProtoBuf vs protolite

This small package contains optimized C++ implementations for reading and writing several common data formats based on Google protocol-buffers. Currently it supports [`rexp.proto`](https://github.com/jeroen/protolite/blob/master/src/rexp.proto) for serialized R objects, 
[`geobuf.proto`](https://github.com/jeroen/protolite/blob/master/src/geobuf.proto) for geojson data, and [`mvt.proto`](https://github.com/jeroen/protolite/blob/master/src/mvt.proto) for reading Mapbox vector tiles. 

To extend the package with additional formats, put your `.proto` file in the `src` directory. The package configure script will automatically generate the code and header file to include in your C++ bindings.

The protolite package is much faster than RProtoBuf because it binds directly to [generated C++](https://developers.google.com/protocol-buffers/docs/reference/cpp-generated) code from the `protoc` compiler. RProtoBuf on the other hand uses the more flexible but slower reflection-based interface, which parses the descriptors at runtime. With RProtoBuf you can create new protocol buffers of a schema, read in arbitrary .proto files, manipulate fields, and generate / parse .prototext ascii format protocol buffers. For more details have a look at our paper: [*RProtoBuf: Efficient Cross-Language Data Serialization in R*](https://arxiv.org/abs/1401.7372).

## Serializing R objects

```r
# Serialize and unserialize an object
library(protolite)
buf <- serialize_pb(iris)
out <- unserialize_pb(buf)
stopifnot(identical(iris, out))

# Fully compatible with RProtoBuf
buf <- RProtoBuf::serialize_pb(iris, NULL)
out <- protolite::unserialize_pb(buf)
stopifnot(identical(iris, out))

# Other way around
buf <- protolite::serialize_pb(mtcars, NULL)
out <- RProtoBuf::unserialize_pb(buf)
stopifnot(identical(mtcars, out))

```

## Converting between GeoJSON and GeoBuf

Use the [countries.geo.json](https://github.com/johan/world.geo.json/blob/master/countries.geo.json) example data:

```r
# Example data
download.file("https://github.com/johan/world.geo.json/raw/master/countries.geo.json",
  "countries.geo.json")

# Convert geojson to geobuf
buf <- json2geobuf("countries.geo.json")
writeBin(buf, "countries.buf")

# The other way around
geobuf2json(buf) #either in memory
geobuf2json("countries.buf") #or from disk

# Read directly from geobuf
mydata <- read_geobuf("countries.buf")
```


## Installation

Binary packages for __OS-X__ or __Windows__ can be installed directly from CRAN:

```r
install.packages("protolite")
```

Installation from source on Linux or OSX requires Google's [Protocol Buffers](https://developers.google.com/protocol-buffers/) library. On __Debian or Ubuntu__ install [libprotobuf-dev](https://packages.debian.org/testing/libprotobuf-dev) and [protobuf-compiler](https://packages.debian.org/testing/protobuf-compiler):

```
sudo apt-get install -y libprotobuf-dev protobuf-compiler
```

On __Fedora__ we need [protobuf-devel](https://src.fedoraproject.org/rpms/protobuf):

```
sudo yum install protobuf-devel
````

On __CentOS / RHEL__ we install [protobuf-devel](https://src.fedoraproject.org/rpms/protobuf) via EPEL:

```
sudo yum install epel-release
sudo yum install protobuf-devel
```

On __OS-X__ use [protobuf](https://github.com/Homebrew/homebrew-core/blob/master/Formula/protobuf.rb) from Homebrew:

```
brew install protobuf
```
