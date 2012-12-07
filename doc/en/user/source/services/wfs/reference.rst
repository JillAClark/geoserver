.. _wfs_reference: 


.. warning:: Document Status: ** Requires Tech review**

WFS reference
============= 

Introduction
------------ 

The `Web Feature Service <http://www.opengeospatial.org/standards/wfs>`_ (WFS) is a standard created by the Open Geospatial Consortium (OGC) for creating, modifying and exchanging vector format geographic information on the Internet using HTTP. A WFS encodes and transfers information in Geography Markup Language(GML), a subset of XML. 

The current version of WFS is 2.0.0. GeoServer supports versions 2.0.0, 1.1.0, and version 1.0.0. Although there are some important differences between the versions, the syntax often remains the same.

A related OGC specification, the Web Map Service (:ref:`wms`), defines the standard for exchanging geographic information in digital image format. 

Benefits of WFS
---------------

The WFS standard defines the framework for providing access to, and supporting transactions on, discrete geographic features in a manner that is independent of the underlying data source. Through a combination of discovery, query, locking and transaction operations, users have access to the source spatial and attribute data in a manner that allows them to query, style, edit (create, update, and delete), and download individual features. The transactional capabilities of WFS also support the development and deployment of collaborative mapping applications. 

Operations
----------

WFS supports the following 12 operations: 

.. list-table::
   :widths: 20 80

   * - **Operation**
     - **Description**
   * - ``GetCapabilities``
     - Generates a metadata document describing a WFS service provided by server  as well as valid WFS operations and parameters
   * - ``DescribeFeatureType``
     - Returns a description of feature types supported by a WFS service 
   * - ``GetFeature``
     - Returns a selection of features from a data source including geometry and attribute values
   * - ``LockFeature``
     - Prevents a feature from being edited through a persistent feature lock
   * - ``Transaction`` 
     - Edits existing feature types by creating, updating, and deleting 
   * - ``GetGMLObject`` 
     - *(Version 1.1.0 only)*—Retrieves features and elements by ID from a WFS 
   * - ``GetPropertyValue``
     - *(Version 2.0.0 only)*—Retrieves the value of a feature property or part of the value of a complex feature property from the data store for a set of features identified using a query expression
   * - ``GetFeatureWithLock``
     - *(Version 2.0.0 only)*—Returns a selection of features and also applies a lock on those features
   * - ``CreateStoredQuery``
     - *(Version 2.0.0 only)*—Create a stored query on the WFS server
   * - ``DropStoredQuery``
     - *(Version 2.0.0 only)*—Deletes a stored query from the WFS server
   * - ``ListStoredQueries``
     - *(Version 2.0.0 only)*—Returns a list of the stored queries on a WFS server
   * - ``DescribeStoredQueries``
     - *(Version 2.0.0 only)*—Returns a metadata document describing the stored queries on a WFS server

.. note:: In the some of the examples that follow, the fictional URL http://www.example.com/wfs? is used for illustration. Substitute the address of a valid WFS server as required.

.. _wfs_getcap:

GetCapabilities
---------------

The **GetCapabilities** operation is a request to a WFS server for a list of the operations and services, or *capabilities*, supported by that server. A list of WFS services and sample GetCapabilities requests are maintained on the `GeoServer site <http://geoserver.org/display/GEOS/Available+WMS+and+WFS+servers>`_.

To issue a GET request using HTTP:

.. code-block:: xml
 
   http://www.example.com/wfs?
   service=wfs&
   version=1.1.0&
   request=GetCapabilities
	  
The equivalent request using POST:
	
.. code-block:: xml 

   <GetCapabilities
   service="WFS"
   xmlns="http://www.opengis.net/wfs"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://www.opengis.net/wfs 			
   http://schemas.opengis.net/wfs/1.1.0/wfs.xsd"/>
	
GET requests are simplest to decode, but the POST requests are comparable. 

.. note:: Although the request would normally be defined on one line with no breaks, breaks are added in the various examples provided here for clarity. 

All requests to a WFS server must include three mandatory parameters:

* *service*—Instructs the WFS server that a WFS request has been sent  
* *version*—Refers to the version of WFS being requested. Supplying a value like "1" or "1.1" will likely return an error. 
* *request*—Specifies of the GetCapabilities operation 

Although these parameters are mandatory, if any are omitted from a request, GeoServer will provide default values.

The GetCapabilities response is a lengthy XML document. Note that a 1.0.0 Capabilities document is different from the 1.1.0 document which in turn is different to the 2.0.0 version discussed here.

There are five main components in a GetCapabilities document:

.. list-table::
   :widths: 20 80
   
   * - **ServiceIdentification**
     - Contains basic header information for the request such as the Title and ServiceType. The ServiceType indicates which version(s) of WFS are supported.
   * - **ServiceProvider**
     - Provides contact information about the company publishing the WFS service, including telephone, website, and email.
   * - **OperationsMetadata**
     - Describes the operations that the WFS server supports and the parameters for each operation. A WFS server may be configured not to respond to all operations listed above.
   * - **FeatureTypeList**
     - Lists the feature types published by a WFS server. Feature types are listed in the form *namespace:featuretype*. The default projection of the feature type is also listed, along with the  bounding box for the data in the stated projection.
   * - **Filter_Capabilities**
     - Lists the filters or expressions that are available to form query predicates. For example, SpatialOperators (Equals, Touches) and ComparisonOperators (LessThan, GreaterThan). The filters are not defined in the Capabilities document but are for the most part self explanatory.

DescribeFeatureType
-------------------

**DescribeFeatureType** requests information about an individual feature type before requesting the actual data. Specifically, **DescribeFeatureType** will request a list of features and attributes for the given feature type, or list the feature types available.

The parameters for **DescribeFeatureType** are:

.. list-table::
   :widths: 20 20 60
   
   * - **Parameter**
     - **Required?**
     - **Description**
   * - ``service``
     - Yes
     - Service name—Value is ``WFS``
   * - ``version``
     - Yes
     - Service version—Value is the current version number
   * - ``request``
     - Yes
     - Operation name—Value is ``DescribeFeatureType``
   * - ``typeNames``
     - Yes
     - Name of the feature type to describe
   * - ``exceptions``
     - No
     - Format for reporting exceptions—default value is ``application/vnd.ogc.se_xml``
   * - ``outputFormat``
     - No
     - Defines the scheme description language used to describe feature types

Note again the four required parameters—``service``, ``version``, ``request`` and '``typeNames``.

To return a list of feature types, the GET request would be as follows. This request will return the list of feature types, sorted by namespace.

.. code-block:: xml 

   http://www.example.com/wfs?
      service=wfs&
      version=1.1.0&
      request=DescribeFeatureType

To list information about a specific feature type, the GET request would be:

.. code-block:: xml 

   http://www.example.com/wfs?
      service=wfs&
      version=1.1.0&
      request=DescribeFeatureType&
      typeName=namespace:featuretype

The only difference between these two requests is the addition of ``typeName=namespace:featuretype``, where ``featuretype`` is the name of the feature type and ``namespace`` is the name of the namespace that feature type is contained in.

.. code-block:: xml 

   http://localhost:8080/geoserver/wfs?
      REQUEST=DescribeFeatureType&SERVICE=WFS&VERSION=1.1.0
      &EXCEPTIONS=application/json
      &outputFormat=text/javascript
      &typeName=sf:roads

The result in JsonP format (if enabled) would be:

.. code-block:: xml 

   parseResponse(
   {
      elementFormDefault: "qualified",
      targetNamespace: "http://www.openplans.org/spearfish",
      targetPrefix: "sf",
      featureTypes: [
         {
         typeName: "roads",
         properties: [
            {
            name: "the_geom",
            maxOccurs: 1,
            minOccurs: 0,
            nillable: true,
            type: "gml:MultiLineString",
            localType: "MultiLineString"
            },
            {
            name: "cat",
            maxOccurs: 1,
            minOccurs: 0,
            nillable: true,
            type: "xsd:int",
            localType: "int"
            },
            {
            name: "label",
            maxOccurs: 1,
            minOccurs: 0,
            nillable: true,
            type: "xsd:string",
            localType: "string"
            }
         ]
         }
      ]
   }
   )

.. _wfs_getfeature:

GetFeature
----------

The **GetFeature** operation returns a selection of features from the data source. Not all of its abilities will be discussed here.

The simplest way to run a **GetFeature** command is without any arguments.

.. code-block:: xml 

   http://www.example.com/wfs?
      service=wfs&
      version=1.1.0&
      request=GetFeature&
      typeName=namespace:featuretype

This syntax should be familiar from previous examples. The only difference is the ``request=GetFeature.``

Executing this command will return the geometries for all features in given a feature type, potentially a large amount of data, so using GetFeature is this manner is not recommended. However, to limit the output you can restrict the GetFeature request to a single feature by including an additional parameter, ``featureID`` and providing the ID of a specific feature.  In this case, the GET request would be:

.. code-block:: xml 

   http://www.example.com/wfs?
      service=wfs&
      version=1.1.0&
      request=GetFeature&
      typeName=namespace:featuretype&
      featureID=feature

If the ID of the feature is unknown, or if you wish to limit the amount of features returned, use the ``maxFeatures`` parameter. In the example below, ``N`` represents the number of features to return.

.. code-block:: xml 

   http://www.example.com/wfs?
      service=wfs&
      version=1.1.0&
      request=GetFeature&
      typeName=namespace:featuretype&
      maxFeatures=N

Exactly which N features will be returned depends in the internal structure of the data. However, you can sort the returned selection based on an attribute value. In the following example, the attribute is included in the request using the ``sortBy=property`` parameter (replace ``property`` with the attribute you wish to sort by):

.. code-block:: xml

   http://www.example.com/wfs?
      service=wfs&
      version=1.1.0&
      request=GetFeature&
      typeName=namespace:featuretype&
      maxFeatures=N&
      sortBy=<attribute>property

The default sort operation is to sort in ascending order. Some WFS servers require the sort order to be specified, even if an ascending order sort if required.  In this case, append a ``+A`` to the request. Conversely, to sort in descending order, add a ``+D`` to the request, like so:

.. code-block:: xml

   http://www.example.com/wfs?
      service=wfs&
      version=1.1.0&
      request=GetFeature&
      typeName=namespace:featuretype&
      maxFeatures=N&
      sortBy=property+D

There is no obligation to use ``sortBy`` with ``maxFeatures`` in a GetFEature request, but they can work effectively together to help manage the returned selection of features. 

To restrict a GetFeature request by attribute rather than feature, use the ``propertyName`` key in the form ``propertyName=property``. You can specify a single property, or multiple properties separated by commas. To search for a single property in all features, use the following:


.. code-block:: xml

   http://www.example.com/wfs?
      service=wfs&
      version=1.1.0&
      request=GetFeature&
      typeName=namespace:featuretype&
      propertyName=property

For a single property from just one feature, execute the following:

.. code-block:: xml

   http://www.example.com/wfs?
      service=wfs&
      version=1.1.0&
      request=GetFeature&
      typeName=namespace:featuretype&
      featureID=feature&
      propertyName=property

For more than one property from a single feature:

.. code-block:: xml

   http://www.example.com/wfs?
      service=wfs&
      version=1.1.0&
      request=GetFeature&
      typeName=namespace:featuretype&
      featureID=feature&
      propertyName=property1,property2


All of these permutations for a GetFeature request have so far centered around non-spatial parameters. It is also possible to query for features based on geometry. While there are limited options available in a GET request for spatial queries (more are available in POST requests using filters), one of the most important options, "bounding box" or BBOX, is supported.

The BBOX allows you to search for features that are contained (or partially contained) inside a box of user-defined coordinates. The format of a BBOX query is ``bbox=a1,b1,a2,b2``where ``a``, ``b``, ``c``, and ``d`` represent the coordinate values.

The order of coordinates passed to the BBOX parameter depends on the coordinate system used. This also explains why the coordinate syntax isn't ``bbox=x1,y1,x2,y2`` or ``bbox=y1,x1,y2,x1``. To specify the coordinate system, append ``srsName=CRS`` to the WFS request, where ``CRS`` is the coordinate reference system.  

As for which corners of the bounding box to specify, the only requirement is for the bottom corner (left or right) to be provided first. For example, bottom left / top right or bottom right / top left. A sample request for returning features based on bounding box would be similar to the following:  

.. code-block:: xml

   http://www.example.com/wfs?
      service=wfs&
      version=1.1.0&
      request=GetFeature&
      typeName=namespace:featuretype&
      bbox=a1,b1,a2,b2


LockFeature
-----------

A **LockFeature** operation provides a long-term feature locking mechanism to ensure consistency in edit transactions. If one client fetches a feature and makes some changes before submitting it back to the WFS, locks prevent other clients from making any changes to the same feature, ensuring a serializable transaction. If a WFS server supports the **LockFeature** operation, this will be reported in the server's GetCapabilities response.

In practice, few clients support the LockFeature operation. 


Transaction
-----------

The **Transaction** operation can create, modify, and remove features published by a WFS. Each transaction will consists of zero or more Insert, Update, and Delete elements, with each transaction element performed in order. In GeoServer every transaction is *atomic*, meaning that if any of the elements fail, the transaction is abandoned and the data is unaltered. A WFS server that supports **transactions** is sometimes known as a WFS-T server.  **GeoServer fully supports transactions.** 

More information on the syntax of transactions can be found in the `WFS specification <http://www.opengeospatial.org/standards/wfs>`_ and in the GeoServer sample requests.  

 
GetGMLObject (WFS 1.1.0 only)
-----------------------------

A **GetGMLObject** operation accepts the identifier of a GML object (feature or geometry) and returns that object. Not widely used by client applications, it only makes sense in situations that require :ref:`app-schema.complex-features` by allowing clients to extract just a portion of the nested properties of a complex feature.  


GetPropertyValue (WFS 2.0.0 only)
---------------------------------

A **GetPropertyValue** operation retrieves the value of a feature property, or part of the value of a complex feature property, from a data source for a given set of features identified by a query.


GetFeatureWithLock (WFS 2.0.0 only)
-----------------------------------

A **GetFeatureWithLock** operation is very similar to a **GetFeature** operation, only this time when the set of features are returned from the WFS server, the features are also locked in anticipation of a subsequent transaction operation.
 

CreateStoredQuery (WFS 2.0.0 only) 
----------------------------------

A **CreateStoredQuery** operation creates a stored query on the WFS server. The definition of the stored query is encoded in the ``StoredQueryDefinition`` parameter. 


DropStoredQuery (WFS 2.0.0 only)
--------------------------------

A **DropStoredQuery** operation drops previously created stored queries. The request accepts the ID of the query to drop. 


ListStoredQueries (WFS 2.0.0 only)
----------------------------------

A **DropStoredQuery** operation returns a list of the stored queries currently maintained by the WFS server.


DescribeStoredQueries (WFS 2.0.0 only)
--------------------------------------

A **DropStoredQuery** operation returns detailed metadata about each stored query maintain by the WFS server. A description of an individual query may be requested by providing the ID of the specific query. If no ID is provided, all queries are described.

Exceptions
----------

WFS also supports a number of formats for reporting exceptions. The supported values for exceptions are:

.. list-table::
   :widths: 15 35 50
   
   * - **Format**
     - **Syntax**
     - **Notes**
   * - XML
     - ``EXCEPTIONS=text/xml``
     - Xml output. (The default format)
   * - JSON
     - ``EXCEPTIONS=application/json``
     - Simple Json representation.
   * - JSONP
     - ``EXCEPTIONS=text/javascript``
     - Return a JsonP in the form: paddingOutput(...jsonp...). See :ref:`wms_vendor_parameters` to change the callback name.


