.. _wfs_basics:

WFS basics
==========

GeoServer provides support for Open Geospatial Consortium (OGC) Web Feature Service (WFS) versions 1.0, 1.1, and 2.0. WFS defines a standard for exchanging vector data over the Internet. With a compliant WFS, clients can query both the data structure and the source data. Advanced WFS operations also support feature locking and edit operations.  

GeoServer is the reference implementation of all three versions of the standard, completely implementing every part of the protocol. This includes the Basic operations of GetCapabilities, DescribeFeatureType and GetFeature, as well as the more advanced Transaction, LockFeature and GetGmlObject operations. GeoServer's WFS also is integrated with GeoServer's :ref:`security` system, to limit access to data and transactions. It also supports a wide variety of :ref:`wfs_output_formats`, to make the raw data more widely available.  

GeoServer additionally supports a special *versioning* protocol in an extension: :ref:`wfsv_extension`. This is not yet a part of the WFS specification, but has been written to be compatible, extending the specification to provide a history of edits, the differences between edits, and a rollback operation to restore features to a previous state.  


:ref:`wfs_reference`

Differences between WFS versions
--------------------------------

The major differences between the WFS versions are: 

* WFS 1.1.0 and 2.0.0 return GML3 as the default GML. In WFS 1.0.0 the default was GML2. (GeoServer still supports requests in both GML3 and GML2 formats.) GML3 has slightly different ways of specifying a geometry. 

* In WFS 1.1.0 and 2.0.0, the SRS (Spatial Reference System or projection) is specified with ``urn:x-ogc:def:crs:EPSG:XXXX``, whereas in version 1.0.0 the specification was ``http://www.opengis.net/gml/srs/epsg.xml#XXXX``. This change has implications for the axis order of the returned data. 

* WFS 1.1.0 and 2.0.0 support on-the-fly reprojection of data, which allows for data to be returned in a SRS other than the native SRS. 

* WFS 2.0.0 introduces a new version of the Filter encoding specification, adding support for temporal filters.  

* WFS 2.0.0 also add support for joins via a GetFeature request.

* WFS 2.0.0 adds the ability to page results of a GetFeature request via the startIndex and maxFeatures parameters. This capability is also now supported in versions 1.0.0 and 1.1.0. 

* WFS 2.0.0 introduces support for store queries, which are in effect regular WFS queries stored on the server that may be invoked by passing the appropriate identifier with the WFS request.

* WFS 2.0.0 adds support for SOAP (Simple Object Access Protocol) as an alternative to the OGC interface.


Axis ordering
-------------

WFS 1.0.0 servers return geographic coordinates in longitude/latitude (x/y) order. This is the most common way to distribute data as well (for example, most shapefiles adopt this order by default). 

However, the traditional axis order for geographic and cartographic systems is the opposite— latitude/longitude (y/x)—and the WFS 1.1.0 specification respects this. This may cause difficulties when switching between servers with different WFS versions, or when upgrading your WFS. 

The default axis ordering support is therefore: 

* WFS 1.1.0 and WFS 2.0.0 request = latitude/longitude
* WMS 1.0.0 request = longitude/latitude 

GeoServer, however, in an attempt to minimize confusion and increase interoperability, has adopted the following conventions when specifying projections in the follow formats: 

* ``EPSG:xxxx``—longitude/latitude
* ``http://www.opengis.net/gml/srs/epsg.xml#xxxx``—longitude/latitude
* ``urn:x-ogc:def:crs:EPSG:xxxx``—latitude/longitude 
