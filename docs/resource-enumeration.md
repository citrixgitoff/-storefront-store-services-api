#Resource Enumeration
##Request

|URL <span style="font-weight:normal">(indicative only)</span>|Method|Description|
|---|---|---|
|/resources/v2/ /resources/v2/withoutautoprovision (see Endpoints section)| GET | Returns a list of all resources available for the user in resources Xml format, with or without auto-provision.|
|/resources/v2/{id} | GET | Returns the details of specified resource in resource Xml format. This URL is given for illustrative purposes. The actual URL should be obtained from the enumeration result returned by the request to /resources/v2/.|

|URL parameter | Description
|---|---|
|{id} | The identifier of the resource. This is unique to the instance of the service.|

|Query string parameter | Description|
|---|---|
|group | Allows the requester to specify which resource elements should be returned by the enumeration. Rather than specify the elements individually, ‘groups’ are defined which give a group name associated with a collection of elements names (the mechanism by which groups are defined is not described here). <br> The default value (if no group is specified) is ‘**all**’. The ‘**all**’ group corresponds to requesting all available elements.  <br> Group names are matched regardless of case. Unrecognized group names are ignored. If multiple groups are specified then the union of the elements for each group is used. Multiple groups can be requested using multiple group parameters e.g. to request all elements in either the **core** or **sub** groups: .../resources/v2/?group=core&group=sub. <br>  The id element is always returned. If one or more groups are specified, but none of the group names are recognized, then resources containing just the id element will be returned. <br> See below for the default groups defined. This set of groups may be increased by Citrix or 3rd- party extensions in the future (the mechanisms for this extension are not discussed here). Note, however, that groups extensions will not be allowed to change the elements associated with a group.|
|subscriptionStatus | Allows the requester to specify that only resources with a specified set of status values should be returned. Multiple status values can be requested using multiple subscriptionStatus parameters. If no subscriptionStatus is supplied, resources are returned regardless of subscription status. <br> The valid values are those defined in the subscription schema for the subscriptionStatus element (see /Schemas/Subscriptions.xsd).|
|scope | Allows the scope to be specified (a concept in the Nfuse protocol). <br> Only a limited set of allowed values will be permitted. Other values will result in a HTTP 400 (Bad Request) response. The value check will be case-insensitive, although the value passed on to the underlying system will be of the defined case. <br> Allowed values: <pre>$PRELAUNCH$ </pre> and <pre> $ANONYMOUS_PRELAUNCH$ </pre> - As defined by the pre-launch optimizations for PNAgent. <br>The scope value will be passed in the Nfuse RequestAppData made to XenApp/XenDesktop.|
|clientName, clientAddress | See Client Name, Client Address and Device Id section above.|

**Notes**:

* These requests require an Authorisation token
* The URL for a request for a specific resource (i.e. /resources/v2/{id}) is obtained from the Response to resource enumeration (i.e. /resources/v2/).
* In all cases, for each request, the service will perform a fresh enumeration for the user and so will pick up any changes.
* Group and subscriptionStatus query string parameters can be applied in any combination (both, either, neither) and order.

##Response
|Response Code|Description|
|---|---|
|200|Success|
|401|Bad/Missing security token (see CitrixAuth Authentication Scheme document [3] )|
|404|No resource available for the specified {id} parameter (the resource may not exist or access to it may not be authorized for the requesting user).|

|Response Format|Request Accept /Response Content-Type Header|
|---|---|
|Resources Xml format|application/vnd.citrix.resources+xml|
|Resource Xml format|application/vnd.citrix.resource+xml|

The resources Xml format allows for extensions. The default Store service with no 3rd-party extensions will have the following elements which shall exist in the [http://citrix.com/delivery-services/2-0/resources] (‘res:’) and [http://citrix.com/delivery-services/2-0/subscriptions] (‘sub:’) namespaces.

|Extension element|Group(s)|Description|
|---|---|---|
|res:id|n/a|An identifier that is unique across this instance of the resources service. This value is always returned in resources regardless of specified groups.|
|res:images|images|A list of image sizes and color depths held by the StoreFront Services server (may be used to determine which image sizes are likely to give good results when requested by the client).|
|res:imagehash|core|A string giving a hash of the resources full image data. If two resources have the same image hash value, then (barring the insignificant chance of a hash collision) they have the same image data.<br><br>For most purposes clients can use the res:image URL value to determine whether resources share image data.|

**Note**: The image and icon urls represent the image data at the point that the enumeration was performed. If, subsequently, the image data changes for the resource, these urls will not point to the updated images. On the other hand the urls are persistent in the sense that they will always point to the same image (or icon file), although they may return 404 after the image data has been updated on the XenApp/XenDesktop server.

The root <pre>resources </pre> element shall also have the following attributes:

|---|---|---|
|enumeration (http://citrix.com/delivery- services/2-0 resources)|full|All the aggregated Xml Services responded|
 |partial|Some of the aggregated Xml Services responded|

##Endpoints
|URL (indicative only)|Id|Capabilities|
|---|---|---|

This service offers optional auto-provisioning behaviour. With auto-provisioning, the enumeration service automatically subscribes an application if all of the following conditions are true:

* The application is an Auto-provisioned application.
* The application has never been subscribed.
* The application has no associated workflow for subscription.

##Mandatory Apps
All the apps which are marked as mandatory have the following subscriptions behaviour.

* They are always subscribed for all users.
* They cannot be unsubscribed by the Clients. 

##Example: Request all resources 

###Request
```
GET http://www.example.com/Citrix/Store/resources/v2?group=core&group=sub HTTP/1.1 Host: www.example.com
Accept: application/vnd.citrix.resources+xml
Accept-Encoding: gzip, deflate,gzip, deflate
Authorization: CitrixAuth H4sIAA........
Host: www.example.com
```

###Response
```
HTTP/1.1 200 OK
Cache-Control: public, no-store, max-age=0
Content-Type: application/vnd.citrix.resources+xml
Content-Length: xxxxxxx
```
```
<?xml version="1.0" encoding="utf-8"?>
<resources xmlns="http://citrix.com/delivery-services/2-0/resources"
xmlns:a="http://citrix.com/delivery-services/2-0/subscriptions"
           enumeration="full" a:subscriptionsstatus="enabled">
  <resource>
    <id>Controller.Notepad</id>
    <link>
<url>https://www.example.com/Citrix/Store/resources/v2/Q2l0cml4Lk1QUy5BcHAu</url> </link>
<title>Notepad</title>
<summary>Notepad</summary>
<path>\</path> <resourcetype>Citrix.MPS.Application</resourcetype> <clienttypes>
      <clienttype>ica30</clienttype>
      <clienttype>rdp</clienttype>
    </clienttypes>
    <rade>
      <licenseType>online</licenseType>
      <offlineMode>none</offlineMode>
    </rade>
    <images>
      <image size="48" depth="4"  />
      <image size="32" depth="4"  />
      <image size="24" depth="4"  />
      <image size="16" depth="4"  />
      <image size="48" depth="8"  />
      <image size="32" depth="8"  />
      <image size="24" depth="8"  />
      <image size="16" depth="8"  />
      <image size="256" depth="32"  />
      <image size="48" depth="32"  />
      <image size="32" depth="32"  />
      <image size="24" depth="32"  />
      <image size="16" depth="32"  />
    </images>
    <enabled>true</enabled>
    <launchica>
<url>https://www.example.com/Citrix/Store/resources/v2/Q2l0cml4Lk1QUy5BcHAu/launch/ica</url> </launchica>
<image>
<url>https://www.example.com/Citrix/Store/resources/v2/E4wNXorb0I5OHEvZlhsOWFZPQ--/image</url> </image>
<icon> <url>https://www.example.com/Citrix/Store/resources/v2/E4wNXorb0I5OHEvZlhsOWFZPQ--/icon</url>
</icon> <imagehash>E4wNXorb0I5OHEvZlhsOWFZPQ--</imagehash> <aggregatedresource>false</aggregatedresource> <publisherresourceid>Notepad<publisherresourceid> <publishername>Farm1</publishername> <mandatory>true</mandatory> <a:subscriptionactions>
<url>https://www.example.com/Citrix/Store/resources/v2/subscriptions/Q2l0cml4Lk1QUy5BcHAu</url> </a:subscriptionactions>
</resource>
  ...
</resources>
```
##Example: Request a specific resource

**Notes**: When a request is made for a specific resource, then the same Xml is returned as for the corresponding resource element in the resources enumeration using the same schema, as illustrated by the following example:

###Request
```
GET https://www.example.com/Citrix/Store/resources/v2/Q2l0cml4Lk1QUy5BcHAu HTTP/1.1 Accept: application/vnd.citrix.resource+xml
Authorization: CitrixAuth H4sIAA........
Host: www.example.com
Connection: Keep-Alive
```
###Response
```
HTTP/1.1 200 OK
Cache-Control: public, no-store, max-age=0
Content-Type: application/vnd.citrix.resource+xml
Content-Length: xxxxx
```
```
<?xml version="1.0" encoding="utf-8"?>
<resource>
  <id>Controller.Notepad</id>
  <link>
<url>https://www.example.com/Citrix/Store/resources/v2/Q2l0cml4Lk1QUy5BcHAu</url> </link>
<title>Notepad</title>
<summary>Notepad</summary>
<path>\</path> <resourcetype>Citrix.MPS.Application</resourcetype> <clienttypes>
    <clienttype>ica30</clienttype>
    <clienttype>rdp</clienttype>
  </clienttypes>
  <rade>
    <licenseType>online</licenseType>
    <offlineMode>none</offlineMode>
  </rade>
  <images>
    <image size="48" depth="4"  />
     ...
    <image size="16" depth="32"  />
</images>
  <enabled>true</enabled>
  <launchica>
<url>https://www.example.com/Citrix/Store/resources/v2/Q2l0cml4Lk1QUy5BcHAu/launch/ica</url> </launchica>
<image>
<url>https://www.example.com/Citrix/Store/resources/v2/E4wNXorb0I5OHEvZlhsOWFZPQ--/image</url> </image>
<icon>
<url>https://www.example.com/Citrix/Store/resources/v2/E4wNXorb0I5OHEvZlhsOWFZPQ--/icon</url> </icon>
<imagehash>E4wNXorb0I5OHEvZlhsOWFZPQ--</imagehash> <mandatory>true</mandatory> <a:subscriptionactions>
<url>https://www.example.com/Citrix/Store/resources/v2/subscriptions/Q2l0cml4Lk1QUy5BcHAu</url> </a:subscriptionactions>
</resource>
```