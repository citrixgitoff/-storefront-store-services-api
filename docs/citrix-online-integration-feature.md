#Citrix-Online Integration Feature (Generic Applications)

The optional Citrix-Online Integration feature, if added to an installation, will add support for 'Generic Applications'. These are resources additonal to, or modified versions of, the resources returned by XenApp, XenDesktop or VDI-in-a-Box. The Generic Applications functionality is driven from configuration.

The Generic Applications configuration appears in the citrix.deliveryservices/genericApplications section of the web.config configuration file in the Store web application. It has the form:
```
<genericApplications>
<application name="name" clientType="clientType">
<source enabled="true|false" resourceId="resourceId" title="title" summary="summary" imageLocation="imagePath">
      <keywords></keywords>
<properties></properties>
</source>
<transform enabled="true|false" markerKeyword="keyword"/>
  </application>
</genericApplications>
```

|---|---|
|application/source/@enabled|Indicates whether a resource for this generic application should be generated for all users.|

If the source enabled attribute is set to true, then for all users, resource enumeration will contain an extra resource of type 'Citrix.MPS.Application' with the specified id, title, summary, client-types, keywords, properties and icon.

##Transform
If the transform enabled attribute is set to true then, any resource that has the specified marker-keyword, will be replaced with a resource with exactly the same id, type and other data, but with the client-types changed to contain the single, specified client- type.

**Notes**:

* It is not recommended to specify enabled="true" for both source and tranform elements for a single application entry, since that can lead to some users having two copies of the same generic application.
* If more than one resource contains the specified marker keyword, then it is possible that some users will have more than one copy of the same generic application.