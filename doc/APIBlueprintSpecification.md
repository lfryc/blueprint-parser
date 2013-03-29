Author: z@apiary.io

# API Blueprint Language Specification

## Revision 1A

---

## Contents
1. [Introduction][Introduction]
2. [API Blueprint Language][Language]
3. [API Blueprint Document][Document]
	1. [Sections][Sections]
	2. [Reserved Section Names][ReservedSectionNames]
	3. [Nested sections][NestedSections]
	4. [Other Markdown headers][OtherMarkdownHeaders]
	5. [Special Sections][SpecialSections]
4. [API Blueprint Document Structure][DocumentStructure]
	1. [Metadata Section][MetadataSection]
	2. [API Name & Overview Section][APINameOverviewSection]
	3. [Resource Section][ResourceSection]
		1. [Parameters Subsection][ResourceParametersSection]
		2. [Method Section][ResourceMethodSection]
		3. [Request Subsection][ResourceRequestSection]
		4. [Response Subsection][ResourceResponseSection]
		5. [Headers Subsection][ResourceHeadersSection]
	4. [Grouping resources][ResourceGroups]
5. [Payloads][Payloads]
	1. [Headers Subsection][PayloadHeadersSection]
	2. [Parameters Subsection][PayloadParametersSection]
	3. [Body Subsection][PayloadBodySection]
	4. [Schema Subsection][PayloadSchemaSection]
6. [Assets][DocumentAssets]
	1. [Inline Asset][InlineDocumentAsset]
---

## 1. Introduction [Introduction]
This documents is full specification of [Apiary.io](http://apiary.io)'s API Blueprint Language. API Blueprint Language is a domain-specific language in which you can describe any modern [REST](http://www.restapitutorial.com) or [Hypermedia]() API. It was built to **design** and **create** **API wireframes** as well as **documentation**.

While the language is simple enough to be used by anybody with the elementary programming skills it is recommended to go through [Tutorial](http://apiary.io/blueprint) first before continuing with the full specification.

Note that it is not in scope of this document to discuss any of the Apiary.io tools such as [Mock Server](http://apiary.io), [Proxy](http://apiary.io) or others. However some of them may be mentioned by this document.

---

## 2. API Blueprint Language [Language]
API Blueprint Language is essentially a superset of John Gruber's [Markdown](http://daringfireball.net/projects/markdown), where some of its constructs have **strictly defined meaning**. It inherits some major [MultiMarkdown](http://fletcherpenney.net/multimarkdown) and [GitHub Flavored Markdown](https://help.github.com/articles/github-flavored-markdown) features.

Before you will proceed with this document please make yourself familiar with the basic [Markdown Syntax](http://daringfireball.net/projects/markdown/syntax) as well as with the Metadata and Automatic Cross-References sections of [MultiMarkdown Syntax](https://github.com/fletcher/MultiMarkdown/blob/master/Documentation/MultiMarkdown%20User%27s%20Guide.md#multimarkdown-syntax-guide) and GitHub Flavored Markdown's [newlines & fenced code blocks](https://help.github.com/articles/github-flavored-markdown).

The **API Blueprint Language** uses several **Markdown constructs**, such as headers, lists or pre-formatted blocks, to **define** and **describe** your **API**. These constructs are usually recognized by reserved names, expected formatting and / or by their specific locations.

In addition to usual Markdown elements you can use HTML-style **comments** (`<!--` & `-->`) to comment out a block of blueprint or add additional notes.

---

## 3. API Blueprint Document [Document]
API Blueprint Document is where you describe, explain and define the interface. 

The document itself is divided into several logical sections forming **API Blueprint Document Structure**.

### 3.1. Sections [Sections]
A section represents logical units of your API Blueprint. For example an API overview, group of resources or a resource definition. Some sections might include subsections, for example a resource definition might contain resource URI parameters description as its subsection.

Sections are recognized by a **reserved section name** or an **URI template** in **Markdown header**

Subsections are recognized by a **reserved (sub)section name** in a Markdown list item followed be newline.

Each section and subsection has strictly defined name, meaning and content. Anything between a Section Markdown header and another such a header at the same level is considered to be a section. This implies you **must avoid** using reserved section names – **keywords** in other Markdown headers. The same applies to subsections.

### 3.2. Reserved Section Names [ReservedSectionNames]
Reserved keywords are:

* **Request**
* **Response**
* **Headers**
* **Parameters**
* **Body**
* **Schema**
* \+ HTTP method names
* \+ URIs

Reserved sections and their meanings in [API Blueprint Document Structure](DocumentStructure). Note that some section names can contain variable components such as identifiers or other modifiers. See relevant section's entry to find out more about how section name is built.

### 3.3. Nested sections [NestedSections]
Some sections can be nested. To nest a section in another section simply **increase** its **header level**.

Example:

	# Section A
	... Section A content ...

	## Nested Section of Section A
	... Nested Section content ...

	# Section B
	... Section B content...

What section can be nested and where depends on the actual section as described in relevant [API Blueprint Document Structure](DocumentStructure) section's entry.

### 3.4. Other Markdown headers [OtherMarkdownHeaders]
You are free to use any Markdown header of your liking anywhere as long as it does not clash with [Reserved Section Names](ReservedSectionNames). It is considered a good practice to keep your own header level nested to your actual section.

### 3.5. Special Sections [SpecialSections]
There are **two additional** sections of a Blueprint Document to sections represented by a [Reserved Name Sections][Sections]: A [Metadata Section][MetadataSection] and the [API Name & Overview][APINameOverviewSection]. These are discussed in the [API Blueprint Document Structure][DocumentStructure]

---

## 4. API Blueprint Document Structure [DocumentStructure]
Bellow you will find description of every section of the API Blueprint Document. Note that all sections are optional. However the document should contain one or more [Resource][ResourceSection] Section.

An example of a possible API Blueprint Document layout:

	Metadata: ...

	# API Name
	...
	# Group 1
	...
	## Resource 1.1
	...
	+ Parameters
	...
	+ Request
	...
	+ Response
	...
	## Resource 1.2
	...
	+ Response
	...
	# Group 2
	...
	+ Resource 2.1
	...

### 4.1. Metadata Section [MetadataSection]
**Optional**. API metadata.

This section is **recognized** as [MultiMarkdown' Metadata](https://github.com/fletcher/MultiMarkdown/blob/master/Documentation/MultiMarkdown%20User%27s%20Guide.md#metadata). It starts from the beginning of the document and ends with a first Markdown header.

This section **does not include** any **other sections**.

#### Supported Metadata
* **`Host` (or `HOST`)** *(optional)* ... API server hostname.
* **`Format`** *(optional)* ... API Blueprint version. Leave blank for legacy format. Use `1A` for actual version.

Example:

	Host: http://blog.acme.com
	Format: 1A

### 4.2. API Name & Overview Section [APINameOverviewSection]
**Optional**. Name of the API in the form of a Markdown header.

This section is **recognized** as the **first** Markdown header in your document its name is considered to be your **API name**.

This section can contain **further Markdown-formatted content**. If a content is provided it is considered to represent the API Overview.

This section **does not include** any **other sections**.

Example:

	# My API Name

	-- or --

	# Basic ACME Blog API
	Welcome to the **ACME Blog** API. This API provides access to the **ACME Blog** service.

### 4.3. Resource Section [ResourceSection]
**Optional**. Definition of exactly *one* API [Resource](http://www.w3.org/TR/di-gloss/#def-resource). Your blueprint document can contain multiple sections for the same resource - URI, as long as their HTTP methods differs.

This section is **recognized** by an [RFC 6570 URI template](http://tools.ietf.org/html/rfc6570) written an Markdown header. Optionally the header can contain one leading [HTTP Request Method](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods).

This section can contain **further Markdown-formatted content**. If a content is provided it is considered to represent Resource description.

This section **must include** at least one [Response Subsection][ResourceResponseSection] **or**, if no **HTTP Request Method** is specified, this section **must include** at least one nested [Method Section][ResourceMethodSection].

In addition to any mandatory nested sections this section **may include** following additional subsections:
 
* [Parameters Subsection][ResourceParametersSection]
* Nested [Method Section][ResourceMethodSection],  if **no** HTTP Request Method is specified
* [Request Subsection][ResourceRequestSection], if a HTTP Request Method is specified
* [Response Subsection][ResourceResponseSection], if a HTTP Request Method is specified
* [Headers Subsection][ResourceHeadersSection]

Example:

	# PUT /posts

	-- or --

	# GET /posts{/id}

	-- or --

	# /posts

#### 4.3.1. Parameters Subsection [ResourceParametersSection]
**Optional**. Description of [Resource Section][ResourceSection]'s URI parameters. Content of this section is subject to additional formatting.

This subsection is **recognized** by the **"Parameters"** reserved **keyword** written as a Markdown list item. If present, it must be subsection of a [Resource Section][ResourceSection].

This subsection can contain **further Markdown-formatted content**. If a content is provided it is considered to represent general Resource URI parameter description. The rest of this subsection is formatted as follows:

	+ <parameter name> [= <default value>] [<type> [,(required | optional)]] ... Markdown-formatted content

Where:

* `<parameter name>` is a parameter name as written in [Resource Section][ResourceSection]'s URI (e.g. "id").
* `<default value>` is **optional** parameter default or example value (e.g. 1234).
* `<type>` is **optional** parameter type as expected by your API (e.g. "number").
* `required` is **optional** specifier of a required parameter
* `optional` is **optional** specifier of a optional parameter

This subsection does not have to list every URI parameter. It **should not** however contain parameter that is not specified in URI.

This subsection **does not include** any **other subsection**.

Example:

	# GET /posts{/id}
	+ Parameters
		+ id ... Id of a post.

	-- or --

		+ id = 1234 ... Id of a post.

	-- or --

		+ id (number) ... Id of a post.

	-- or --
Response Subsection
		+ id = 1234 (number, required) ... Id of a post.

Note that parameters description is used for documentation purposes only.

#### 4.3.2. Method Section [ResourceMethodSection]
**Required** if there is no HTTP method specified in [Resource Section][ResourceSection]'s header. **Illegal** otherwise. [HTTP Request Method](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods) available for this Resource.

This section is **recognized** by one of the [HTTP Request Methods](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods) written in capitals as an Markdown header.

This section can contain **further Markdown-formatted content**. If a content is provided it is considered to represent this Resource method description.

This section **must include** at least one [Response Subsection][ResourceResponseSection]. In addition to mandatory nested section this section **may include** following additional subsections:
 
* [Parameters Subsection][ResourceParametersSection]
* [Request Subsection][ResourceRequestSection], if a HTTP Request Method is specified
* [Response Subsection][ResourceResponseSection], if a HTTP Request Method is specified
* [Headers Subsection][ResourceHeadersSection]

One [Resource Section][ResourceSection] can contain **one or more different** Method Sections.

Example:

	# /posts{/id}
	+ Parameters
	...

	## GET
	Retrieves a **ACME Blog** posts.
	...

	## PUT
	...

#### 4.3.3. Request Subsection [ResourceRequestSection]
**Optional**. Description of exactly *one* [HTTP request](http://www.w3.org/TR/di-gloss/#def-http-request).

This subsection is **recognized** by the **"Request"** reserved **keyword** written in a Markdown header. The **"Request"** can be followed by an arbitrary string representing user identifier of this request. This identifier **must not** be enclosed in brackets. In case HTTP body is specified the **"Request"** keyword (and possible identifier) should be followed by HTTP Body [Media Type (MIME type)](http://en.wikipedia.org/wiki/Internet_media_type).

Full Request Subsection list syntax is as follows:

	+ Request [<identifier>] [(<Media Type>)]

This subsection is a specific type of [Payload][Payloads] carried by this request. See [Payloads Documentation][Payloads] for details on how to specify the content of this section.

One [Resource Section][ResourceSection] or [Method Section][ResourceMethodSection] can contain **one or more different** (that is with different identifier) Request Subsections.

Example:

	+ Request (text/plain)
		
			Hello World

	-- or --

	+ Request Create Blog Post (application/json)
	    
	    	{ "message" : "Hello World." }

#### 4.3.4. Response Subsection [ResourceResponseSection]
**Required**. Description of exactly *one* [HTTP response](http://www.w3.org/TR/di-gloss/#def-http-response).

This subsection is **recognized** by the **"Response"** reserved **keyword** written in a Markdown list followed by a [HTTP Status code](http://www.restapitutorial.com/httpstatuscodes.html). In case HTTP body is specified the **"Response"** keyword should be followed by HTTP Body [Media Type (MIME type)](http://en.wikipedia.org/wiki/Internet_media_type).

Full Response Subsection list syntax is as follows:

	+ Response <Status Code> [(<Media Type>)]

This subsection **must** be listed under a [Method Section][ResourceMethodSection] unless a HTTP method is specified in the [Resource Section][ResourceSection]'s header. In that case this subsection must be listed under the [Resource Headers Subsection][ResourceSection].

This section is a specific type of [Payload][Payloads] carried by this response. See [Payload Documentation][Payloads] for details on how to specify the content of this section.

One [Resource Section][ResourceSection] or [Method Section][ResourceMethodSection] can contain **one or more different** (that is with different HTTP Status code) Response Subsections.

Example:

	+ Response 201 (application/json)
		
				{ "message" : "created" }

#### 4.3.5. Headers Subsection [ResourceHeadersSection]
**Optional**. Description HTTP Headers parameters. Content of this subsection is subject to additional formatting.

This section is **recognized** by the **"Headers"** reserved **keyword** written in a Markdown list. Optionally the **"Headers"** keyword can be preceded by either `Response` or `Request` header keyword.

Full Header section list syntax is as follows:

	+ [Request [<identifier>] | Response <status code>] Headers

This subsection must be listed under one of the following sections:

* [Resource Section][ResourceSection]
* [Method Section][ResourceMethodSection]

Based on where is this sections is listed the headers are expected or send as follows:

* **Resource Section**: Headers are expected and/or send with **every** request and/or response on this **Resource's URI**.
* **Method Section**: Headers are expected and/or send with **every** request and/or response with specific **method and Resource's URI**.

Based on keywords preceding the **"Headers"** keyword the headers are expected or send as follows:

* **Request** keyword: Headers are expected only with requests.
* **Response** keyword: Headers are send only with responses.

The subsection is formatted as an Markdown's [Pre-formatted code blocks](http://daringfireball.net/projects/markdown/syntax#precode) with following syntax:

	<HTTP header name>: <value>

One HTTP header per line.

This subsection **does not include** any **other sections**.

Example:

	+ Headers
	    
	    	Accept-Charset: utf-8
		    Connection: keep-alive

	+ Request Headers

		    Accept-Charset: utf-8
		    Connection: keep-alive

### 4.4. Grouping resources [ResourceGroups]
Resource sections can be grouped together. For example by a common task such as handling payments, shopping cart manipulation, blog post management or user management.

To group resources simply [nest][NestedSections] your resource section(s) under a Markdown header of a group's name.

Group section can contain **further Markdown-formatted content**. If a content is provided it is considered to represent group description.

Group **must include** at least one [Resource Section][ResourceSection].

Example:

	# Blog Posts
	Resources in this groups are related to **ACME Blog** posts.

	## GET /posts{/id}
		...

	## PUT /posts
		...

	# Authors
	## GET /authors
		...

	# Comments
		...

---

## 5. Payloads [Payloads]
An payload is essentially data expected in a HTTP request or send in a HTTP response. Payload consists of meta information in form of HTTP headers and content received or send in a HTTP body. Furthermore API Blueprint Payload can include its description as well as discussion of its parameters.

Note that the term "payload" (excluding its description) as used in this document is technically a subset of [HTTP Payload](http://www.w3.org/TR/di-gloss/). There might be additional metadata (HTTP headers) specified outside of the scope of the payload that can form up the final HTTP Payload.

A Payload has **always** its Media Type associated. Payload's Media type represents a metadata that is always received or send in form of an HTTP `Content-Type` header.

Payload section header syntax is follows:

	# <Payload Identifier> (<Media Type>)
	
Payload subsection header syntax is follows:	

	+ <Payload Identifier> (<Media Type>)

Payload is formed by following **optional** subsections: 

* [Headers Subsection][PayloadHeadersSection]
* [Parameters Subsection][PayloadParametersSection]
* [Body Subsection][PayloadBodySection]
* [Schema Subsection][PayloadSchemaSection]

If **no subsection** is specified content of the payload section is considered as a [Body Subsection][PayloadBodySection].

Example:

	# MyPayload (application/json)
	+ Headers
		
		X-My-Payload-Size: 42

	+ Parameters
		+ message ... A message.

	+ Body
		
			{ ... }

	+ Schema
	
			{ ... }


### 5.1. Headers Subsection [PayloadHeadersSection]
**Optional**. Specifies the metadata in form of HTTP headers to be received or send with the payload. Content of this section is subject to additional formatting.

This subsection is **recognized** by the **"Headers"** reserved **keyword** written as a Markdown list item. No further keywords or modifiers are expected.

Refer to [Resource Headers Subsection][ResourceHeadersSection] for this section's syntax definition.

Example:

	# MyPayload (application/json)
	+ Headers
	
			X-My-Payload-Size: 42

### 5.2. Parameters Subsection [PayloadParametersSection]
**Optional** for **application/json** media type. Description of [Payload][Payloads]'s parameters. Content of this subsection is subject to additional formatting.

This subsection is **recognized** by the **"Parameters"** reserved **keyword** written as a Markdown list item.

This subsection can contain **further Markdown-formatted content**. If a content is provided it is considered to represent general payload parameters description. The rest of this section is formatted as follows:

	+ <parameter name> [= <default value>] [<type> [,(required | optional)]] ... Markdown-formatted content


Where `<parameter name>` is name of a [body][PayloadBodySection] top-level field. To access the elements of an array and to access the fields of a subdocument use [MongoDB Dot Notation](http://docs.mongodb.org/manual/core/document/#dot-notation).

See Resource [Parameters Subsection][ResourceParametersSection] for further details.

Example:

	# MyPayload (application/json)
	+ Headers
	
			X-My-Payload-Size: 42

	+ Parameters
	
			+ message (string) ... A message from **ACME Blog** API.

### 5.3. Body Subsection [PayloadBodySection]
**Optional**. Specifies content of the payload received or send in the form of HTTP body.

This subsection is **recognized** by the **"Body"** reserved **keyword** written as a Markdown list item.

This subsection represents an API Blueprint Document [Asset][DocumentAssets].

This subsection **does not include** any **other subsections**.

Example:

	# MyPayload (application/json)
	+ Headers
	
			X-My-Payload-Size: 42

	+ Parameters
		+ message (string) ... A message from **ACME Blog** API.

	+ Body
	
			{ "message" : "Hello World." }

### 5.4. Schema Subsection [PayloadSchemaSection]
**Optional**. Where applicable specifies a schema used to validate this payload's body content.

This subsection is **recognized** by the **"Schema"** reserved **keyword** written as a Markdown list item.

This subsection represents an API Blueprint Document [Asset][DocumentAssets].

This subsection **does not include** any **other sections**.

---

## 6. Assets [DocumentAssets]
An API Blueprint Document Asset is simply a resource (not to be confused with API Resource) – a piece data used in [payloads][Payloads].

### 6.1. Inline Asset [InlineDocumentAsset]
In its simplest form an asset is essentially a Markdown's [Pre-formatted code blocks](http://daringfireball.net/projects/markdown/syntax#precode). The sole content of this block is considered to represent the Asset's data.

Example:

	# Asset Name
		{ "message" : "Hello World." }
		
	-- or --
	
	+ Asset Name

		{ "message" : "Hello World." }

---