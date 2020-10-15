#Subscriptions Service
This service allows resources to be subscribed and unsubscribed, and to have data associated with each subscription.

##Request
|---|---|---|

|---|---|
|{resourceId}|The identifier of the associated resource. This is unique to the instance of the service.|


##Request data
The POSTed requests are Xml, described by the schema: /Schemas/Subscriptions.xsd, with top level element subscriptionUpdate.

##Response
|---|---|
|403 (Forbidden)|The request token did not contain a user security identifier claim (this is required to give a unique identifier for the user for recording subscriptions)|
|503 (Service Unavailable)|An extension is temporarily unable to contact a service it requires to contact as part of the subscription process (see relevant extension specification for details)|
In the case that a successful response (HTTP status code 200 (OK)) is returned, the response body contains an Xml document, described by the schema: /Schemas/Subscriptions.xsd with root node subscriptionResult. The response contains the subscription and resource ids, current status of the subscription, and also properties that contain data relating to the subscription action (this data is empty except in the case of extensions):

|Response Format|Request Accept /Response Content-Type Header|
|---|---|


**Notes**:

* The data Xml element of the subscription result may contain properties that relate to the status change that has occurred as a result of the subscribe/unsubscribe action. This is intended as an extension mechanism.
* No account is made in the subscribe/unsubscribe actions what permissions, if any, the requesting user has on the resource identified by {resourceId}, other than that it is visible to that user.

##Subscription Status
|---|---|


####Request
```
POST http://www.example.com/Citrix/Store/resources/v2/subscriptions/Q2l0cm...Q- HTTP/1.1 Authorization: CitrixAuth H4sIA....
Host: www.example.com
Accepts: application/vnd.citrix.subscriptionresult+xml,
application/vnd.citrix.subscriptionconflict+xml Content-Type: application/vnd.citrix.subscriptionupdate+xml Content-Length: 269
```
```
<?xml version="1.0" encoding="utf-8"?>
<subscriptionUpdate xmlns="http://citrix.com/delivery-services/2-0/subscriptions"
       updateType="unsubscribe"
       updateDataMode="merge">
  <clientName>clientname</clientName>
</subscriptionUpdate>
```
####Response
```
HTTP/1.1 200 OK
Cache-Control: public, no-store, max-age=0
Content-Type: application/vnd.citrix.subscriptionresult+xml Date: Wed, 16 Feb 2011 17:31:43 GMT
Content-Length: xxxx
```
```
<?xml version="1.0" encoding="utf-8"?>
<subscriptionResult xmlns="http://citrix.com/delivery-services/2-0/subscriptions">
<subscriptionId>4C34BB98E543F084BAA33696C5659F63</subscriptionId> <resourceId>Citrix.MPS.App.Farm 1.Notepad</resourceId> <status>notSubscribed</status>
<data />
</subscriptionResult>
```

###Example: Successful Update of Subscription Data
This is an example of a successful update of the data associated with a subscription. The property ids and values are arbitrary from the perspective of the Resources service.

####Request
```
POST http://www.example.com/Citrix/Store/resources/v2/subscriptions/Q2l0cm...Q- HTTP/1.1 Authorization: CitrixAuth H4sIA....
Host: www.example.com
Accepts: application/vnd.citrix.subscriptionresult+xml,
application/vnd.citrix.subscriptionconflict+xml Content-Type: application/vnd.citrix.subscriptionupdate+xml Content-Length: xxxx
Expect: 100-continue
Connection: Keep-Alive
```
```
<?xml version="1.0" encoding="utf-8"?>
<subscriptionUpdate xmlns="http://citrix.com/delivery-services/2-0/subscriptions"
       updateType="update"
       updateDataMode="merge" >
  <data>
    <property key="Data1Key">
      <value>Data1 Value1</value>
      <value>Data1 Value2</value>
    </property>
  </data>
  <clientName>clientname</clientName>
</subscriptionUpdate>
```

####Response
```
HTTP/1.1 200 OK
Cache-Control: public, no-store, max-age=0
Content-Type: application/vnd.citrix.subscriptionresult+xml Content-Length: xxxx
```
```
<?xml version="1.0" encoding="utf-8"?>
<subscriptionResult xmlns="http://citrix.com/delivery-services/2-0/subscriptions">
<subscriptionId>4C34BB98E543F084BAA33696C5659F63</subscriptionId> <resourceId>Citrix.MPS.App.Farm 1.Notepad</resourceId> <status>subscribed</status>
<data />
</subscriptionResult>
```
####Conflict Response Content
In the case that a conflict is returned (HTTP status code 200 (OK)), a response potentially containing data pertaining to the conflict is returned in the response body:

|Response Format|Request Accept /Response Content-Type Header|
|---|---|

The subscription result content is Xml, described by the schema: /Schemas/Subscriptions.xsd with root node **subscriptionConflict**.

**Note**: The subscriptionconflict content type is intended to allow for extension, and will only contain properties in the case of extensions (e.g. workflow integration).

####Conflict Reason
The conflict response contains a reason attribute. The possible reason values are an open set allowing for workflow-integration- specific values in the future. The values defined in the base system are:
|---|---|
 
An example of an attempted subscription that is unsuccessful because the resource is already subscribed.

The subscription, if successful, would have associated a client-specific property named “dazzle:path” with the subscription. The property id and value are arbitrary from the perspective of the Resources service.

####Request
```
POST http://www.example.com/Citrix/Store/resources/v2/subscriptions/Q2l0cm...Q- HTTP/1.1 Authorization: CitrixAuth H4sIA....
Host: www.example.com
Accepts: application/vnd.citrix.subscriptionresult+xml,
application/vnd.citrix.subscriptionconflict+xml Content-Type: application/vnd.citrix.subscriptionupdate+xml Content-Length: xxxx
```
```
<?xml version="1.0" encoding="utf-8"?>
<subscriptionUpdate xmlns="http://citrix.com/delivery-services/2-0/subscriptions"
       updateType="subscribe"
       updateDataMode="merge" />
```
####Response
```
HTTP/1.1 200 OK
Cache-Control: public, no-store, max-age=0
Content-Type: application/vnd.citrix.subscriptionconflict+xml Content-Length: xxxx
```
```
<?xml version="1.0" encoding="utf-8"?>
<subscriptionConflict xmlns="http://citrix.com/delivery-services/2-0/subscriptions"
       reason="ConflictingSubscriptionState" >
 <data />
</subscriptionConflict>
```