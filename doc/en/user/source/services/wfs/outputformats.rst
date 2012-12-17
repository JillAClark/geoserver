.. _wfs_output_formats:


WFS output formats
==================

.. warning:: Document Status: **Requires copyedit review**

WFS returns features and feature information in a number of formats. The syntax for specifying an output format is::

   outputFormat=<format>

where ``<format>`` is one of the following options:

.. list-table::
   :widths: 30 30 40
   
   * - **Format**
     - **Syntax**
     - **Notes**
   * - GML2
     - ``outputFormat=GML2``
     - Default option for WFS 1.0.0
   * - GML3
     - ``outputFormat=GML3``
     - Default option for WFS 1.1.0 and 2.0.0
   * - Shapefile
     - ``outputFormat=shape-zip``
     - Created in a ZIP archive
   * - JSON
     - ``outputFormat=application/json``
     - Returns a GeoJson or a Json
   * - JSONP
     - ``outputFormat=text/javascript``
     - Returns a JsonP in the form: ``parseResponse(...json...)``. See :ref:`wms_vendor_parameters` to change the callback name. Note that this format is disabled by default (See :ref:`wms_global_variables`).
   * - CSV
     - ``outputFormat=csv``
     - 

.. note:: This list applies to the basic GeoServer installation. Some additional output formats (such as Excel XLS) are available with the use of an extension. The full list of output formats supported by a GeoServer instance can be found by requesting the WFS :ref:`wfs_getcap`.
     
     
Zipped shapefile customization
------------------------------

With GeoServer 2.0.3 and above, the zipped shapefile output format output can be customized by preparing a Freemarker template which will configure the file names of the zip file and the shapefiles it contains. The default template is:

::

  zip=${typename}
  shp=${typename}${geometryType}
  txt=wfsrequest

The ``zip`` property is the name of the zip file, the ``shp`` property is the name of the shapefile for a given feature type, and ``txt`` is the dump of the WFS request (the request dump is also available with version 2.0.3 and above).

The properties available in the template are:
  
  * ``typename``—Feature type name (for the zip property this will be the first feature type if the request contains many feature types)
  * ``geometryType``—Type of geometry contained in the shapefile. This is only used if the output geometry type is generic and the various geometries are stored in one shapefile per type.
  * ``workspace``—Workspace of the feature type
  * ``timestamp``—Date object with the request timestamp
  * ``iso_timestamp``—String (ISO timestamp of the request at GMT) in yyyyMMdd_HHmmss format
  
Format options as parameter in WFS requests
-------------------------------------------

GeoServer provides the ``format_options`` vendor-specific parameter to specify parameters that are specific to each format. The syntax is:

::

    format-options=param1:value1;param2:value2;...
	
The currently supported format option is:

  * ``filename``—Only applies to the SHAPE-ZIP output format. If a file name is provided, the name is used as the output file name. For example, ``format_options=filename:roads.zip``. If a file name is not specified, an output file name is inferred from the requested feature type(s) name.

