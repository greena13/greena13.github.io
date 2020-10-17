---
layout: post
title: "JSON API Cheatsheet"
date: 2020-10-17 08:20:08 +0100
comments: true
categories: 
---

The JSON API specification the following design goals:

* Standardising the JSON schema (irrespective of the data you're transmitting)
* Minimising the number of client requests
* Minimising the amount of data transmitted 

Understanding how each is achieved is essential in effectively using its features.

<!--more-->

The following is a reorganisation and summary of the full specification: https://jsonapi.org/

## General requirements

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 130px;"/>
        <col style="width: 678px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td style="width: 678px; padding: 8px; border: 1px solid;">
            <div>Rquirement</div>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Headers</span></div>
        </td>
        <td style="width: 678px; padding: 8px; border: 1px solid;">
            <div><span
                    style="font-family: &quot;Helvetica Neue&quot;;">Client and server both use </span><span
                    style="font-family: &quot;Courier New&quot;;">Content-Type: application/vnd.api+json</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;"> without any </span>media type parameters</div>
            <ul>
                <li>
                    <span style="font-family: &quot;Helvetica Neue&quot;;">if the server receives a request with them, it returns 415 Unsupported Media Type</span>

                </li>
            </ul>
            <br/>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Client must also include </span><span
                    style="font-family: &quot;Courier New&quot;;">application/vnd.api+json</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;"> in  </span><span
                    style="font-family: &quot;Courier New&quot;;">Accept</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;"> header, if it uses that header</span>
            </div>
            <ul>
                <li>
                    <span style="font-family: &quot;Helvetica Neue&quot;;">server responds with 406 Not Acceptable if all instances of the Mime Type in the Accept header have media type parameters</span>
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Query parameters</span></div>
        </td>
        <td style="width: 678px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Query string must be </span>URL encoded</div>
            <ul>
                <li>
                    <span style="font-family: &quot;Helvetica Neue&quot;;">e.g. </span><span
                            style="font-family: &quot;Courier New&quot;;">[</span><span
                            style="font-family: &quot;Helvetica Neue&quot;;"> and</span><span
                            style="font-family: &quot;Courier New&quot;;"> ]</span><span
                            style="font-family: &quot;Helvetica Neue&quot;;"> characters must be percent-encoded, per the requirements in RFC 3986.</span>

                </li>
            </ul>
            <br/>
            <div>Query parameters must adhere to the same constraints as member names below, with the
                additional requirement that they MUST contain at least one non a-z character
            </div>
            <ul>
                <li>
                    To preserve the ability of JSON:API to make additive additions to standard query
                        parameters without conflicting with existing implementations
                </li>
                <li>
                    RECOMMENDED that a U+002D HYPHEN-MINUS, “-“, U+005F LOW LINE, “_”, or capital letter
                        is used (e.g. camelCasing).

                </li>
            </ul>
            <br/>
            <div>400 Bad Request:</div>
            <ul>
                <li>
                    Query parameter that does not follow the naming conventions and the server does not
                        know how to process it as a query parameter from this specification
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>Request body</div>
        </td>
        <td style="width: 678px; padding: 8px; border: 1px solid;">
            <div>Can’t include any additional attributes beyond those specified - and if they appear the
                client and server must ignore them
            </div>
            <div><br/></div>
            <div>A JSON object must be at the root of every request and response with data</div>
            <div><br/></div>
            <div>Member (field) names:</div>
            <ul>
                <li>
                    Case-sensitive
                </li>
                <li>
                    Must have at least one character and start and end with globally allowed character
                        (Can be used anywhere): 

                </li>
                <ul>
                    <li>
                        U+0061 to U+007A, “a-z”
                    </li>
                    <li>
                        U+0041 to U+005A, “A-Z”
                    </li>
                    <li>
                        U+0030 to U+0039, “0-9”
                    </li>
                    <li>
                        U+0080 and above: non-ASCII Unicode and non URL safe characters (according to RFC
                            3986) are not recommended

                    </li>
                </ul>
                <li>
                    Not allowed at the start or end:
                </li>
                <ul>
                    <li>
                        * U+002D HYPHEN-MINUS, “-“
                    </li>
                    <li>
                        * U+005F LOW LINE, “_”
                    </li>
                    <li>
                        * U+0020 SPACE, “ “ (not recommended, not URL safe)
                    </li>
                    <li>
                        <a href="https://jsonapi.org/format/#document-member-names-reserved-characters">Full
                            reference</a> 

                    </li>
                </ul>
            </ul>
        </td>
    </tr>
    </tbody>
</table>

## Design Goal: Standardised schema

Object shapes:

* Top-level schema
* Resource Object
* Resource Identifier object
* Linkage Object
* Relationship Object

### Consistent top level schema

The top-level schema of responses is the same, regardless of whether a collection, singular item or (possibly) no data is being returned

**Client / Request**

Nothing explicit; client needs to parse or deserialize the response to get it in a format that is easy to extract information from (JSON API is optimised for data *transmission*, not *retrieval* of information).

**Server / Response**

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 130px;"/>
        <col style="width: 130px;"/>
        <col style="width: 118px;"/>
        <col style="width: 102px;"/>
        <col style="width: 87px;"/>
        <col style="width: 246px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>Required</div>
        </td>
        <td style="width: 118px; padding: 8px; border: 1px solid;">
            <div>Forbidden</div>
        </td>
        <td colspan="3" style="width: 435px; padding: 8px; border: 1px solid;">
            <div>Description</div>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">data</span></div>
        </td>
        <td rowspan="3" style="height: 92px; width: 130px; padding: 8px; border: 1px solid;">
            <div>At least 1 of these must be defined</div>
        </td>
        <td style="height: 56px; width: 118px; padding: 8px; border: 1px solid;" rowspan="2">
            <div>Can’t have <span style="font-family: &quot;Courier New&quot;;">data</span> and <span
                    style="font-family: &quot;Courier New&quot;;">errors</span> at the same time
            </div>
        </td>
        <td colspan="3" style="width: 435px; padding: 8px; border: 1px solid;">
            <div>Document’s “primary data”: resource or collection of resources targeted by a request</div>
            <div><br/></div>
            <div>Requests that target single resources:</div>
            <ul>
                <li>
                    Single resource object
                </li>
                <li>
                    Single resource identifier object, or
                </li>
                <li>
                     null
                </li>
            </ul>
            <div><br/></div>
            <div>Requests that target resource collections:</div>
            <ul>
                <li>
                    Array of resource objects,
                </li>
                <li>
                    Array of resource identifier objects, or
                </li>
                <li>
                    Empty array
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <span style="font-family: &quot;Courier New&quot;;">errors</span>
        </td>
        <td colspan="3" style="width: 435px; padding: 8px; border: 1px solid;">
            <div>Array of error objects</div>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">meta</span></div>
        </td>
        <td style="width: 118px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td colspan="3" style="width: 435px; padding: 8px; border: 1px solid;">
            <div>meta object that contains non-standard meta-information.</div>
        </td>
    </tr>
    <tr>
        <td style="height: 164px; width: 130px; padding: 8px; border: 1px solid;" rowspan="5">
            <div><span style="font-family: &quot;Courier New&quot;;">links</span></div>
        </td>
        <td style="height: 164px; width: 130px; padding: 8px; border: 1px solid;" rowspan="5">
            <div>No</div>
        </td>
        <td style="height: 164px; width: 118px; padding: 8px; border: 1px solid;" rowspan="5">
            <div><br/></div>
        </td>
        <td colspan="3" style="width: 435px; padding: 8px; border: 1px solid;">
            <div>Links object related to the primary data</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>{</div>
                <div><span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">  "links": {</span>
                </div>
                <div><span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">    "self": "<a
                        href="http://example.com/articles">http://example.com/articles</a>",</span></div>
                <div><span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">    "next": "<a
                        href="http://example.com/articles?page">http://example.com/articles?page</a>[offset]=2",</span>
                </div>
                <div><span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">    "last": "<a
                        href="http://example.com/articles?page">http://example.com/articles?page</a>[offset]=10"</span>
                </div>
                <div><span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">  },</span></div>
                <div>  "data": [{</div>
            </div>
            <div> </div>
            <div>Each member of the links object must represent either:</div>
            <ul>
                <li>
                    String containing the link’s URL
                </li>
                <li>
                    Link object: can have members:
                </li>
                <ul>
                    <li>
                        <span style="font-family: &quot;Courier New&quot;;">href</span>: a string
                            containing the link’s URL.

                    </li>
                    <li>
                        <span style="font-family: &quot;Courier New&quot;;">meta</span>: a meta object
                            containing non-standard meta-information about the link.

                    </li>
                </ul>
            </ul>
            <br/>
            <div>Indicating how many of an association is available:</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>"links": {</div>
                <div>  "related": {</div>
                <div>    "href": "<a href="http://example.com/articles/1/comments">http://example.com/articles/1/comments</a>",
                </div>
                <div>    <span
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">"meta": {</span></div>
                <div><span
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">      "count": 10</span>
                </div>
                <div><span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">    }</span></div>
                <div>  }</div>
                <div>}</div>
            </div>
            <div><br/></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid; width: 102px; padding: 8px;">
            <div><br/></div>
        </td>
        <td style="width: 87px; padding: 8px; border: 1px solid;">
            <div>Required</div>
        </td>
        <td style="width: 246px; padding: 8px; border: 1px solid;">
            <div>Description</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid; width: 102px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">self</span></div>
        </td>
        <td style="width: 87px; padding: 8px; border: 1px solid;">
            <div>No</div>
        </td>
        <td style="width: 246px; padding: 8px; border: 1px solid;">
            <div>Link that generated the current response document.</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid; width: 102px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">related</span></div>
        </td>
        <td style="width: 87px; padding: 8px; border: 1px solid;">
            <div>No</div>
        </td>
        <td style="width: 246px; padding: 8px; border: 1px solid;">
            <div>Related resource link when the primary data represents a resource relationship.</div>
            <ul>
                <li>
                    Access to resource objects linked in a relationship
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid; width: 102px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">pagination links</span></div>
        </td>
        <td style="width: 87px; padding: 8px; border: 1px solid;">
            <div>No</div>
        </td>
        <td style="width: 246px; padding: 8px; border: 1px solid;">
            <div>Links for the primary data.</div>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">included</span></div>
        </td>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>No.</div>
        </td>
        <td style="width: 118px; padding: 8px; border: 1px solid;">
            <div>When <span style="font-family: &quot;Courier New&quot;;">data</span> is not present</div>
        </td>
        <td colspan="3" style="width: 435px; padding: 8px; border: 1px solid;">
            <div><span style="--inversion-type-color: simple; color: rgb(255, 38, 0);">Array of resource objects</span>
                that are either:
            </div>
            <ul>
                <li>
                    Related to the primary data and/or
                </li>
                <li>
                    Related to each other
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">jsonapi</span></div>
        </td>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">No</span></div>
        </td>
        <td style="width: 118px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td colspan="3" style="width: 435px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Object describing the server’s implementation</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">If the</span> version member is not
                present, clients should assume the server implements at least version 1.0 of the
                specification.
            </div>
        </td>
    </tr>
    </tbody>
</table>

Example:

```json
{
  "data": [{
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "JSON:API paints my bikeshed!"
    },
    "links": {
      "self": "http://example.com/articles/1"
    },
    "relationships": {
      "author": {
        "links": {
          "self": "http://example.com/articles/1/relationships/author",
          "related": "http://example.com/articles/1/author"
        },
        "data": { "type": "people", "id": "9" }
      },
      "comments": {
        "links": {
          "self": "http://example.com/articles/1/relationships/comments",
          "related": "http://example.com/articles/1/comments"
        },
        "data": [
          { "type": "comments", "id": "5" },
          { "type": "comments", "id": "12" }
        ]
      }
    }
  }],
  "included": [{
    "type": "people",
    "id": "9",
    "attributes": {
      "first-name": "Dan",
      "last-name": "Gebhardt",
      "twitter": "dgeb"
    },
    "links": {
      "self": "http://example.com/people/9"
    }
  }, {
    "type": "comments",
    "id": "5",
    "attributes": {
      "body": "First!"
    },
    "relationships": {
      "author": {
        "data": { "type": "people", "id": "2" }
      }
    },
    "links": {
      "self": "http://example.com/comments/5"
    }
  }, {
    "type": "comments",
    "id": "12",
    "attributes": {
      "body": "I like XML better"
    },
    "relationships": {
      "author": {
        "data": { "type": "people", "id": "9" }
      }
    },
    "links": {
      "self": "http://example.com/comments/12"
    }
  }]
}
```

### Consistent data objects

**Client / Request**

Nothing explicit; client needs to parse or deserialize the standard object shapes.

**Server / Response**

The schema comprises of two object types:

* Resource objects: Contain the full list of attributes to represent a resource
* Resource identifier objects: Contain only the necessary information to identify a resource (for linking or requesting the resource object later on)

Resource object vs resource identifier object:

```javascript
// Resource object:
{
  "data": {
    "type": "articles",
    "id": "1",
    "attributes": {
      // ... this article's attributes
    },
    "relationships": {
      // ... this article's relationships
    }
  }
}

// Resource identifier object:
{
  "data": {
    "type": "articles",
    "id": "1"
  }
} 
```

Resource object

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 130px;"/>
        <col style="width: 130px;"/>
        <col style="width: 541px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div>Required</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 541px; padding: 8px;">
            <div>Description</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">id</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div>Yes - unless new object coming from client</div>
        </td>
        <td style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 541px; padding: 8px;"
            rowspan="2">
            <div>Must be strings that together uniquely resolve to a single resource</div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">type</span> </div>
            <ul>
                <li>
                    used to describe resource objects that share <span style="font-weight: bold;">common attributes and relationships</span>

                </li>
                <li>
                    Can be either plural or singular (but must be used consistently)
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">type</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div>Yes</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">attributes</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div>No</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 541px; padding: 8px;">
            <ul>
                <li>
                    Information about the resource object
                </li>
                <li>
                    Known as the resource’s fields
                </li>
                <li>
                    Any object that constitutes or is contained in an attribute MUST NOT contain a <span
                            style="font-family: &quot;Courier New&quot;;">relationships</span> or <span
                            style="font-family: &quot;Courier New&quot;;">links</span> member

                </li>
                <li>
                    has-one foreign keys SHOULD NOT appear as attributes
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">relationships</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div>No</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 541px; padding: 8px;">
            <div>Relationships (to-one or to-many) between the resource and other JSON:API resources.</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">links</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div>No</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 541px; padding: 8px;">
            <div>Links related to the resource</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">meta</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div>No</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 541px; padding: 8px;">
            <div>meta object containing non-standard meta-information about a resource that can not be
                represented as an attribute or relationship
            </div>
        </td>
    </tr>
    </tbody>
</table>

### Hyperlinked media: Standard way of navigating between data

**Client / Request**

Nothing explicit; client needs to find and use the most appropriate link

**Server / Response**

`links`: Can include links to navigate pages, to reload self or to fetch associated data

* Provides easy navigation by including URLs for each request the client may wish to make next, in the current response

### Abstract relationships: Standard way of representing all relationships, regardless of implementation

**Client / Request**

Nothing explicit; client needs deserialize the response and match up included data with its parent using the relationships

**Server / Response**

`data.relationships`: Abstracts the details of how items are associated (join tables, foreign keys, etc) and just returns the associated data
* Only contains primary keys and data types - for the other attributes, you need to (request and) match up with the data in the included

Relationships are treated as their own entity, allowing endpoints for interacting on the relationship rather than the related resource

Example: author relationship includes

* Link for the relationship itself (allows the client to change the related author directly)
* Related resource link to fetch the resource objects
*  Linkage information.

```javascript
// ...
{
  "type": "articles",
  "id": "1",
  "attributes": {
    "title": "Rails is Omakase"
  },
  "relationships": {
    "author": {
      "links": {
        "self": "http://example.com/articles/1/relationships/author",
        "related": "http://example.com/articles/1/author"
      },
      "data": { "type": "people", "id": "9" }
    }
  },
  "links": {
    "self": "http://example.com/articles/1"
  }
}
// ...
```

Relationships object schema

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 104px;"/>
        <col style="width: 85px;"/>
        <col style="width: 87px;"/>
        <col style="width: 115px;"/>
        <col style="width: 409px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="width: 104px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td style="width: 85px; padding: 8px; border: 1px solid;">
            <div>Required</div>
        </td>
        <td colspan="3" style="width: 611px; padding: 8px; border: 1px solid;">
            <div>Description</div>
        </td>
    </tr>
    <tr>
        <td rowspan="5" style="height: 164px; width: 104px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">links</span></div>
        </td>
        <td rowspan="7" style="height: 236px; width: 85px; padding: 8px; border: 1px solid;">
            <div>At least one of these must be defined</div>
            <div><br/></div>
        </td>
        <td colspan="3" style="width: 611px; padding: 8px; border: 1px solid;">
            <div>Links object</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid; width: 87px; padding: 8px;">
            <div>Attribute</div>
        </td>
        <td style="width: 115px; padding: 8px; border: 1px solid;">
            <div>Required</div>
        </td>
        <td style="width: 409px; padding: 8px; border: 1px solid;">
            <div>Description</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid; width: 87px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">self</span></div>
        </td>
        <td style="height: 56px; width: 115px; padding: 8px; border: 1px solid;" rowspan="2">
            <div>At least one of these is required</div>
        </td>
        <td style="width: 409px; padding: 8px; border: 1px solid;">
            <div>Link for the <span style="--inversion-type-color: simple; color: rgb(255, 38, 0);">relationship</span> (join
                table) - allows the client to directly manipulate the relationship.
            </div>
            <ul>
                <li>
                    For example, removing an author through an article’s relationship URL would
                        disconnect the person from the article without deleting the people resource itself. 
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid; width: 87px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">related</span></div>
        </td>
        <td style="width: 409px; padding: 8px; border: 1px solid;">
            <div>Link to related <span style="--inversion-type-color: simple; color: rgb(255, 38, 0);">resource</span> 
            </div>
            <ul>
                <li>
                    When fetched, the related resource object(s) are returned as the response’s primary
                        data.

                </li>
                <li>
                    E.g: An article’s comments relationship could specify a link that returns a
                        collection of comment resource objects when retrieved through a GET request

                </li>
            </ul>
            <div><br/></div>
            <div>If present link MUST reference a valid URL, even if the relationship isn’t currently
                associated with any target resources
            </div>
            <ul>
                <li>
                    MUST NOT change because its relationship’s content changes
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid; width: 87px; padding: 8px;">
            <span style="font-family: &quot;Helvetica Neue&quot;;">pagination links</span>
        </td>
        <td style="width: 115px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td style="width: 409px; padding: 8px; border: 1px solid;">
            <div>To-many relationship MAY also contain pagination links</div>
            <div>Paginate the relationship data, not the related resources.</div>
        </td>
    </tr>
    <tr>
        <td style="width: 104px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">data</span></div>
        </td>
        <td colspan="3" style="width: 611px; padding: 8px; border: 1px solid;">
            <div>Resource linkage</div>
            <ul>
                <li>
                    Allows a client to link together all of the included resource objects without having
                        to GET any URLs via links.
                </li>
                <li>
                    to-one relationships: <span style="font-family: &quot;Courier New&quot;;">null</span> or a
                        single <span style="font-weight: bold;">resource identifier object</span>
                </li>
                <li>
                    to-many relationships: empty array or array of <span style="font-weight: bold;">resource identifier objects</span>

                </li>
            </ul>
            <div><br/></div>
            <div>Spec doesn’t impart meaning about order of array, although implementations may</div>
            <ul>
                <li>
                    May represent ordered or unordered relationships, and both types can be mixed in one
                        response object.

                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="width: 104px; padding: 8px; border: 1px solid;">
            <span style="font-family: &quot;Courier New&quot;;">meta</span>
        </td>
        <td colspan="3" style="width: 611px; padding: 8px; border: 1px solid;">
            <div>Non-standard meta-information about the relationship</div>
        </td>
    </tr>
    </tbody>
</table>

### Standard error reporting

**Client / Request**

Client needs to deserialize the response and handle the errors

**Server / Response**

errors: Generalised error reporting that can account for one or more errors occurring

Error schema:

* `id` Unique identifier for this particular occurrence of the problem
* `links` Links object containing the following members
* `about`: a link that leads to further details about this particular occurrence of the problem.
* `status` HTTP status code applicable to this problem, expressed as a string value.
* `code` application-specific error code, expressed as a string value
* `title` short, human-readable summary of the problem that SHOULD NOT change from occurrence to occurrence of the problem, except for purposes of localization.
* `detail` human-readable explanation specific to this occurrence of the problem. Like title, this field’s value can be localized.
* `source` Object containing references to the source of the error, optionally including any of the following members:
    * `pointer`: a JSON Pointer [RFC6901] to the associated entity in the request document [e.g. "/data" for a primary data object, or "/data/attributes/title" for a specific attribute].
    * `parameter`: a string indicating which URI query parameter caused the error.
* `meta` meta object containing non-standard meta-information about the error

Server can either stop processing as soon as a problem is encountered, or continue and encounter multiple problems

* The most generally applicable HTTP error code should be used in the response

## Design Goal: Minimise number of requests

### Compound Documents: Including related data client needs

**Client / Request**

`include` query parameter: Comma-separated list of relationship paths (dot-separated list of relationship names)

```
GET /articles/1?include=comments.author HTTP/1.1
Accept: application/vnd.api+json
```

Client is required to re-assemble the connections using the primary fields and types in `relationships` and plucking them out of the `included` array
* Just a flat list - have to match up where they should be nested by finding the matching resource identifier object

**Server / Response**

Adds a flat, normalised (duplicates removed) array of associated resources in the `included` attribute 

* Response is called a “compound document” (consists of more than just the primary resource requested)
* Note: query param is named `include` while attribute is named `included`

Server may return resources related to the primary data by default and may also support an `include` request parameter to allow the client to customize which related resources should be returned

* Server **must** remove all default `included` and only use those specified, if the client is using the query parameter

Because compound documents require full linkage (every included (full) resource object **must** be referenced by a resource identifier object somewhere else in the document), intermediate resources in a multi-part path **must** be returned along with the leaf nodes.

* Exception: when relationship fields that would otherwise contain linkage data are excluded via sparse fieldsets (see below).
* Server may choose to expose a deeply nested relationship as a direct relationship with an alias such as `comment-authors` for `comments.author`
* would allow a client to request `/articles/1?include=comment-authors` instead of `/articles/1?include=comments.author`
* Server can still provide full linkage in compound documents without including potentially unwanted intermediate resources.

Applies to all request types: a server could support the inclusion of related resources along with a POST request to create a resource or relationship

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 131px;"/>
        <col style="width: 681px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="width: 131px; padding: 8px; border: 1px solid;">
            <div>400 Bad Request</div>
        </td>
        <td style="width: 681px; padding: 8px; border: 1px solid;">
            <ul>
                <li>
                    Request uses the <span
                            style="font-family: &quot;Courier New&quot;;">include</span> parameter for
                        an endpoint that does not support it

                </li>
                <li>
                    Server is unable to identify a relationship path
                </li>
                <li>
                    Server does not support inclusion of resources from a path
                </li>
            </ul>
        </td>
    </tr>
    </tbody>
</table>

### Metadata: Including summary and other metadata

**Client / Request**

Included by default - up to the server to implement.
 
**Server / Response**

`meta` attribute for things like counts and modified dates, so don’t have to request all results to generate aggregate or summary information 

Example: 

```javascript
{
  "meta": {
    "copyright": "Copyright 2015 Example Corp.",
    "authors": [
      "Yehuda Katz",
      "Steve Klabnik",
      "Dan Gebhardt",
      "Tyler Kellen"
    ]
  },
  "data": {
    // ...
  }
}
```

## Design Goal: Minimise amount of data transmitted

### Query parameters quick reference

<table style="border-collapse: collapse; min-width: 100%;">
    <tbody>
    <tr>
        <td style="width: 123px; padding: 8px; border: 1px solid;">
            <div>Sorting</div>
        </td>
        <td style="width: 346px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">?sort=age,-name</span></div>
            <ul>
                <li>
                    <span style="font-family: &quot;Helvetica Neue&quot;;">Sort precedence must match order fields are specified in</span>
                </li>
                <li>
                    Default order is ascending, - makes it descending
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="width: 123px; padding: 8px; border: 1px solid;">
            Filtering
        </td>
        <td style="width: 346px; padding: 8px; border: 1px solid;">
            <div><span
                    style="font-family: &quot;Courier New&quot;;">?filter[foo]=bar&amp;filter[baz]=bla</span>
            </div>
            <ul>
                <li>
                    <span style="font-family: &quot;Helvetica Neue&quot;;">Agnostic to filter algorithm used (exact matches, contains, etc)</span>
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="width: 123px; padding: 8px; border: 1px solid;">
            <div>Pagination</div>
        </td>
        <td style="width: 346px; padding: 8px; border: 1px solid;">
            <span style="font-family: &quot;Courier New&quot;;">?page[number]=</span>
            <ul>
                <li>
                    <span style="font-family: &quot;Helvetica Neue&quot;;">Agnostic to paging algorithm used (pages, offsets, etc)</span>
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="width: 123px; padding: 8px; border: 1px solid;">
            <div>Including related resources</div>
        </td>
        <td style="width: 346px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">?include=a.b,a.c</span></div>
        </td>
    </tr>
    <tr>
        <td style="width: 123px; padding: 8px; border: 1px solid;">
            <div>Sparse fieldsets</div>
        </td>
        <td style="width: 346px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">?fields[a]=b,c&amp;fields[d]=e</span>
            </div>
        </td>
    </tr>
    </tbody>
</table>

### Filtering: Restricting results based on criteria

**Client / Request**

`filter` query parameter is reserved for filtering data, but doesn't tell you how to use it: (implementation agnostic, so can use any filter implementation/algorithm)

* Usually involves specifying multiple filters using the square brackets syntax: `filter[name]=foo&filter[age]=21`

**Server / Response**

`data`:

* Collections: Filters items in the primary document
* Items: Filters the scope for looking up the item (may result in `404 Not Found`)

### Sorting: Ordering results

**Client / Request**

`sort` query parameter is used for filtering 

* Recommended dot-separated sort fields be used: E.g. `author.name` could be used to request that the primary data be sorted based upon the name attribute of the `author` relationship
* However, `fields` do not necessarily need to correspond to resource attribute and association names

Sort fields are applied in the order specified

```
GET /people?sort=age,name HTTP/1.1
Accept: application/vnd.api+json
```

Sort fields are ascending unless it is prefixed with a minus

```
GET /articles?sort=-created,title HTTP/1.1
Accept: application/vnd.api+json
```

**Server / Response**

`data`:

* Collections: Changes the order of items in the primary document
* Items: No effect

Server may apply default sorting rules to top-level data if request parameter `sort` is not specified.

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 130px;"/>
        <col style="width: 680px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>400 Bad Request</div>
        </td>
        <td style="width: 680px; padding: 8px; border: 1px solid;">
            <div>Server does not support sorting as specified in the query parameter sort</div>
        </td>
    </tr>
    </tbody>
</table>

### Pagination: Truncating data into pages

**Client / Request**

`page` query parameter is reserved for pagination, but doesn’t tell you how to use it: (implementation agnostic, so can use any pagination strategy: page-based, offset-based, cursor-based)

* Eg. `page[number]` and `page[size]`, an offset-based strategy might use `page[offset]` and `page[limit]`, while a cursor-based strategy might use `page[cursor]`

**Server / Response**

`data`:       

* Collections: Truncates the full set of results in the primary document into pages
* Items: No effect

`meta`: Not dictated, but there are examples of it being used to store the total number of results

`included`: Server may also paginate included resources

`links`: If they server does provide pagination, need to include pagination links: `first`, `last`, `prev`, `next`

* Can be omitted or be `null` if they’re unavailable
* Placed at the top level or in the `included` resource they relate to

### Sparse fieldsets: Selectively including data client needs

**Client / Request**

`fields` query parameter used for specifying a whitelist of attributes to include by specifying a `fields[TYPE]` parameter.

* Comma separated list
* Empty value indicates that no fields should be returned.

Example:

```
GET /articles?include=author&fields[articles]=title,body&fields[people]=name HTTP/1.1
Accept: application/vnd.api+json
```

**Server / Response**

If a client uses sparse fieldsets, server **must** not send back any others

* If not used, server may send all fields, a subset of fields, or no fields for that resource type.

`included` / `relationships`: Lifts the requirement of full linkage if the relationship fields that would otherwise contain linkage data are excluded via sparse fieldsets.

## Working with primary resources (CRUD)

### Viewing a resource

**Server / Response**

Server **must** actually implement any routes mentioned in `self` and `related`

The `data` attribute of a response **must** be a (possible empty) array of resource objects or resource identifier objects for collections, and a resource object or null for a singular item.

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 130px;"/>
        <col style="width: 678px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>Response</div>
        </td>
        <td style="width: 678px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>200 OK</div>
        </td>
        <td style="width: 678px; padding: 8px; border: 1px solid;">
            <div>Successful request to fetch</div>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>404 Not Found</div>
        </td>
        <td style="width: 678px; padding: 8px; border: 1px solid;">
            <div>Request to fetch a single resource that does not exist</div>
            <ul>
                <li>
                    Use a 200 OK response with null as the primary data if the requested URL is
                        one that might correspond to a single resource, but doesn’t currently

                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>Other responses</div>
        </td>
        <td style="width: 678px; padding: 8px; border: 1px solid;">
            <div>Can be used, including setting the <span
                    style="font-family: &quot;Courier New&quot;;">errors</span> attribute
            </div>
        </td>
    </tr>
    </tbody>
</table>

### General (For Creating, Updating and Destroying)

Request **must** completely succeed or fail (in a single “transaction”). No partial updates are allowed.

If the client somehow does not have the most up-to-date and complete set of values, the server **must** respond with the full document again

Examples:

* If using client-generated ids which are ignored and replaced with server generated ones (client needs to be told actual ids used)
* If not using client-generated ids (client needs to be told ids used)
* If updating or creating some attributes, which cause others to be updated or have defaults set (client needs to be told full set of new values)

If the server responds with `204 No Content`, client should assume it already has the complete and most up-to-date values for a resource

### Creating a resource

**Client / Request**

Server may accept a client-generated ID along with a request to create a resource, but they **must** be a universally unique identifier (ideally a properly generated and formatted UUID as described in RFC 4122)

**Server / Response**

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 131px;"/>
        <col style="width: 673px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 131px; padding: 8px;">
            <div>Response</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 673px; padding: 8px;">
            <div><br/></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 131px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">201 Created</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 673px; padding: 8px;">
            <div>Request <span style="font-weight: bold;">did not</span> include a Client-Generated ID
                then it must use <span style="font-family: &quot;Courier New&quot;;">201 Created</span>
            </div>
            <ul>
                <li>
                    Also should include a <span style="font-family: &quot;Courier New&quot;;">Location</span>
                        header identifying the location of the newly created resource. (must match <span
                                style="font-family: &quot;Courier New&quot;;">self</span> link if they’re
                        both provided)

                </li>
                <li>
                    Must respond with a document that contains the primary resource created
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 131px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">204 No Content</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 673px; padding: 8px;">
            <div>Request <span style="font-weight: bold;">did</span> include a Client-Generated ID (and
                the server used it unaltered): Can use <span
                        style="font-family: &quot;Courier New&quot;;">201 Created</span> with document or
                <span style="font-family: &quot;Courier New&quot;;">204 No Content</span> with no document
                (below)
            </div>
            <ul>
                <li>
                    Client should consider the resource object sent in the request to be accepted by
                        the server

                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 131px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">202 Accepted</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 673px; padding: 8px;">
            <div>Request to create a resource has been accepted for processing, but the processing has not
                been completed by the time the server responds
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 131px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">403 Forbidden</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 673px; padding: 8px;">
            <div>Unsupported request to create a resource:</div>
            <ul>
                <li>
                    Including if the server doesn’t support client-generated IDs
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 131px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">404 Not Found</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 673px; padding: 8px;">
            <div>Processing a request that references a related resource that does not exist.</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 131px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">409 Conflict</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 673px; padding: 8px;">
            <div>Either:</div>
            <ul>
                <li>
                    Request to create a resource with a client-generated ID that already exists
                </li>
                <li>
                    When <span style="font-family: &quot;Courier New&quot;;">type</span> is <span
                            style="font-weight: bold;">not</span> among the type(s) that constitute the
                        collection represented by the endpoint

                </li>
            </ul>
            <div><br/></div>
            <div>server SHOULD include error details and provide enough information to recognize the
                source of the conflict.
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 131px; padding: 8px;">
            <div>Other responses</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 673px; padding: 8px;">
            <div>Can be used, including setting the <span style="font-family: &quot;Courier New&quot;;">errors</span>
                attribute
            </div>
        </td>
    </tr>
    </tbody>
</table>

### Updating a resource

**Client / Request**

Can include all or none of the resource’s attributes in an update request
Can include some or all of a resource’s `relationships`

**Server / Response**

Server interprets any missing `attributes` or `relationships` as if they were included with their current values (and NOT `null` or `undefined`)

* If a relationship is provided, the value will be **replaced** with the value specified in this member.

Example: Completely replace tags for an article

```javascript
{
  "data": {
    "type": "articles",
    "id": "1",
    "relationships": {
      "tags": {
        "data": [
          { "type": "tags", "id": "2" },
          { "type": "tags", "id": "3" }
        ]
      }
    }
  }
}
```  

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 130px;"/>
        <col style="width: 688px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">200 OK</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 688px; padding: 8px;">
            <div>Server accepts an update but also changes the resource(s) in ways other than those
                specified by the request
            </div>
            <ul>
                <li>
                    E.g., updating the <span
                            style="font-family: &quot;Courier New&quot;;">updatetAt</span> attribute or a
                        computed <span style="font-family: &quot;Courier New&quot;;">sha</span>
                </li>
                <li>
                    Response document must include a representation of the updated resource(s) as if
                        a GET request was made to the request URL (i.e. include all the attributes - not
                        just those changed)

                </li>
            </ul>
            <div><br/></div>
            <div>If an update is successful, the client’s current fields remain up to date, and the server
                responds only with top-level meta data. 
            </div>
            <ul>
                <li>
                    If this is the case, must not include a representation of the updated
                        resource(s).

                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">202 Accepted</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 688px; padding: 8px;">
            <div>Request has been accepted for processing, but the processing has not been completed by
                the time the server responds
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">204 No Content</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 688px; padding: 8px;">
            <div>Update is successful and the server doesn’t update any fields besides those provided
            </div>
            <ul>
                <li>
                    Server responds with no response document
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">403 Forbidden</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 688px; padding: 8px;">
            <div>Unsupported request to update a resource or relationship.</div>
            <div><br/></div>
            <div>Particularly, since full replacement may be a dangerous operation, a server may choose to
                disallow it
            </div>
            <ul>
                <li>
                    May reject an attempt to do a full replacement of a to-many relationship. (Server
                        must reject the entire update)

                </li>
                <li>
                    May reject full replacement if it has not provided the client with the full list
                        of associated objects, and does not want to allow deletion of records the client
                        has not seen.

                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">404 Not Found</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 688px; padding: 8px;">
            <div>Either:</div>
            <ul>
                <li>
                    Request to modify a resource that does not exist.
                </li>
                <li>
                    Request that references a related resource that does not exist.
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">409 Conflict</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 688px; padding: 8px;">
            <div>Either:</div>
            <ul>
                <li>
                    Request to update a resource if that update would violate other server-enforced
                        constraints (such as a uniqueness constraint on a property other than id).

                </li>
                <li>
                    Request in which the resource object’s <span
                            style="font-family: &quot;Courier New&quot;;">type</span> and <span
                            style="font-family: &quot;Courier New&quot;;">id</span> do not match the
                        server’s endpoint.

                </li>
            </ul>
            <div><br/></div>
            <div>Server should include error details and provide enough information to recognise the
                source of the conflict.
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div>Other responses</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 688px; padding: 8px;">
            <div>Can be used, including setting the <span style="font-family: &quot;Courier New&quot;;">errors</span>
                attribute
            </div>
        </td>
    </tr>
    </tbody>
</table>

### Destroying a resource

```
DELETE /photos/1 HTTP/1.1
Accept: application/vnd.api+json
```

Doesn't appear to need the resource `id` or `type` in the body of the request.

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 130px;"/>
        <col style="width: 673px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">202 Accepted</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 673px; padding: 8px;">
            <div>Request has been accepted for processing, but the processing has not been completed by
                the time the server responds,
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">204 No Content</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 673px; padding: 8px;">
            <div>Deletion request is successful and no content is returned.</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">200 OK</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 673px; padding: 8px;">
            <div>Deletion request is successful and the server responds with only top-level meta data.
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">404 Not Found</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 673px; padding: 8px;">
            <div>Deletion request fails due to the resource not existing.</div>
        </td>
    </tr>
    </tbody>
</table>

## Working with relationships directly

JSON API treats relationships as their own singular resource, providing APIs for interacting with them directly (regardless of their implementation):

* Each relationship object can contain a link for interacting with the relationship directly
* The behaviour of requests to that URL depend on the HTTP method you use

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 130px;"/>
        <col style="width: 659px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>Operation</div>
        </td>
        <td style="width: 659px; padding: 8px; border: 1px solid;">
            <div>HTTP Request type</div>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>View</div>
        </td>
        <td style="width: 659px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">GET</span></div>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>Add to</div>
        </td>
        <td style="width: 659px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">POST</span></div>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>Replace</div>
        </td>
        <td style="width: 659px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">PATCH</span></div>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>Remove from</div>
        </td>
        <td style="width: 659px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">DELETE</span></div>
        </td>
    </tr>
    </tbody>
</table>

### Viewing Relationships

Returns the *relationship* object with a data value that is collection or singular resource, depending on whether it’s a to-one or to-many relationship

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 130px;"/>
        <col style="width: 674px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>200 OK</div>
        </td>
        <td style="width: 674px; padding: 8px; border: 1px solid;">
            <div>Successful request to fetch</div>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>404 Not Found</div>
        </td>
        <td style="width: 674px; padding: 8px; border: 1px solid;">
            <div>Fetch a relationship link URL that does not exist.</div>
            <ul>
                <li>
                    Can happen when the parent resource of the relationship does not exist
                </li>
                <li>
                    If a relationship link URL exists but the relationship is empty, then 200 OK MUST
                        be returned

                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>Other responses</div>
        </td>
        <td style="width: 674px; padding: 8px; border: 1px solid;">
            <div>Can be used, including setting the <span style="font-family: &quot;Courier New&quot;;">errors</span>
                attribute
            </div>
        </td>
    </tr>
    </tbody>
</table>

### Updating Relationships

Has the same endpoint shape, regardless of implementation semantics (foreign keys, join tables etc)

Server can choose to delete the underlying resource if a relationship is deleted (as a garbage collection measure).

#### Update a To-One relationship: Only PATCH is supported - i.e. you’re updating it whether the relation currently exists or not

**Client / Request**

Client include a top-level member named `data` containing one of:

* A resource identifier object corresponding to the new related resource.
* `null`, to remove the relationship.

##### Swap a relationship

```
PATCH /articles/1/relationships/author HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json 

{ "data": { "type": "people", "id": "12" }}
```

##### Removing a relationship

```
PATCH /articles/1/relationships/author HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json 

{ "data": null}
```

#### Update a To-Many relationship: PATCH, POST and DELETE are supported

**Client / Request**

Body **must** contain a `data` member whose value is an empty array or an array of resource identifier objects

##### POST request: Adding items to a relation

**Server / Response**

Adds the specified members to the relationship unless they are already present

* If a given `type` and `id` is already in the relationship, the server does not add it again

If all of the specified resources can be added to, or are already present in, the relationship then the server returns a successful response.

* Ensures a request is successful if the server’s state matches the requested state
* Helps avoid pointless race conditions caused by multiple clients making the same changes to a relationship.

```
{ "data": [ { "type": "comments", "id": "123" } ]}
```

##### PATCH Request: Replaces all existing items in the relation

**Server / Response**

Either:

* Completely replace every member of the relationship
* Return an appropriate error response if some resources can not be found or accessed
* Return `403 Forbidden`

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 130px;"/>
        <col style="width: 680px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div>403 Forbidden</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 680px; padding: 8px;">
            <div>If full replacement of the association is not permitted</div>
        </td>
    </tr>
    </tbody>
</table>

##### DELETE Request: Remove items from a relation

**Server / Response**

Removes the specified members from the relationship or return a `403 Forbidden` response

If all of the specified resources are able to be removed from, or are already missing from, the relationship then server returns a successful response

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 153px;"/>
        <col style="width: 658px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 153px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">202 Accepted</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 658px; padding: 8px;">
            <div>Relationship update request has been accepted for processing, but the processing has not been
                completed by the time the server responds
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 153px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">204 No Content</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 658px; padding: 8px;">
            <div>update is successful and the representation of the resource in the request matches the
                result
            </div>
            <ul>
                <li>
                    POST request sent to a URL from a to-many relationship link when that relationship
                        already exists.

                </li>
                <li>
                    DELETE request sent to a URL from a to-many relationship link when that relationship
                        does not exist.

                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 153px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">200 OK</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 658px; padding: 8px;">
            <div>Server accepts an update but also changes the targeted relationship(s) in other ways than
                those specified by the request
            </div>
            <ul>
                <li>
                    Response document MUST include a representation of the updated relationship(s).
                </li>
            </ul>
            <div><br/></div>
            <div>Server MUST return a 200 OK status code if an update is successful, the client’s current data
                remain up to date, and the server responds only with top-level meta data. 
            </div>
            <ul>
                <li>
                    In this case the server MUST NOT include a representation of the updated
                        relationship(s

                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 153px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">403 Forbidden</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 658px; padding: 8px;">
            <div> Unsupported request to update a relationship.</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 153px; padding: 8px;">
            <div>Other responses</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 658px; padding: 8px;">
            <div>Can be used, including setting the <span
                    style="font-family: &quot;Courier New&quot;;">errors</span> attribute
            </div>
        </td>
    </tr>
    </tbody>
</table>
