#Appendix A: Keywords and Properties Parsing

The resource enumeration Xml return by the resource enumeration service contains keywords and properties elements. For resources derived from XenApp, XenDesktop or VDI-in-a-Box, these values are generated from mark-up in the description field of the corresponding resource. The description field is then 'tidied up' to remove this mark-up.

The keyword/property parser looks for the string KEYWORDS: (case-insensitive). If found, all text after that string is treated as keyword/property mark-up. The mark-up is made up of the following tokens separated by one or more spaces:

* &lt;property>=&lt;value>
* &lt;property>="&lt;quoted-value>"
* &lt;keyword>

Where:

* &lt;property>, &lt;value> and &lt;keyword> are strings made up of any characters other than space, = or "
* &lt;quoted-value> is a string made up of any character other than "

The keywords are collected into a single list.

All property entries with the same property name are collected togther to give a multiple-valued property.

Some concrete examples:
|---|---|---|---|
|"My description KEYWORDS: kw1 kw2 (Description with keyword mark-up)|"My description"|&lt;keywords&gt; <br>&lt;keyword&gt;kw1&lt;/keyword&gt; <br>&lt;keyword&gt;kw2&lt;/keyword&gt;<br> &lt;/keywords&gt;|&lt;properties/&gt;|
|"My description keywords: name1=a name2=b" (Description with misformatted mark-up - a misspelled KEYWORDS: token)|"My description keywrds: name1=a name2=b "|&lt;keywords/&gt;|&lt;properties/&gt;|
|"My description KEYWORDS: name1=a Name1=b "(Property names are case- sensitive.)|"My description"|&lt;keywords/&gt;|&lt;property propertyId="name1"&gt;<br>&lt;value&gt;a&lt;/value&gt;<br>&lt;/property&gt;<br>&lt;property propertyId="Name1"&gt;<br>&lt;value&gt;b&lt;/value&gt;<br>&lt;/property&gt;<br>&lt;/properties&gt;|