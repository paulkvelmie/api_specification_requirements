# API Specification and Requirements

---

**Version**: 1.0

---

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in [RFC 2119].

[RFC 2119]: http://www.ietf.org/rfc/rfc2119.txt

**[⬆ back to home](../)**

---

## Table of Contents

1. [General](#markdown-header-general)
2. [Responses](#markdown-header-responses)
3. [Errors](#markdown-header-errors)

---

### General

Every document SHOULD contain at least one of the following **top-level members**:

- **data**: the document’s “primary data”

- **errors**: array or errors in the right format

All Attributes must be in **camelCase**.

*Content-Type* MUST be: **application/json**.

**[⬆ back to top](#markdown-header-table-of-contents)**

---

### Responses

#### 200 OK

A server MUST respond to a successful request to fetch an individual resource or resource collection with a 200 OK response.

A server MUST respond to a successful request to fetch a resource collection with an array of resource object or an empty array ([]) as the response document’s primary data.

Example:

```json
{
  "data": [
    {
      "id": 1,
      "userName": "Tom",
      "age": 21
    },
    {
      "id": 2,
      "userName": "Bob",
      "age": 22
    }
  ]
}
```

A server MUST respond to a successful request to fetch an individual resource with a **resource object** or **null** provided as the response document’s primary data.

If a resource object is loaded with relationships, relationships SHOULD be embedded in the resource object. 

Relationships attribute name should be:

 - in a **plural** form (users, books ) for collection
 - in a **singular** form (group, setting) from a single resource.


Example: 

```json
{
  "data": {
    "id": 1,
    "userName": "Tom",
    "age": 22,
    "userGroup": {
      "id": 1,
      "name": "Trol"
    },
    "comments": [
      {
        "id": 1,
        "message": "cool first comment"
      },
      {
        "id": 2,
        "message": "cool second comment"
      }
    ]
  }
}
```

#### 404 Not Found

A server MUST respond with 404 Not Found when processing a request to fetch a single resource that does not exist, except when the request warrants a 200 OK response with null as the primary data (as described above).

**[⬆ back to top](#markdown-header-table-of-contents)**

---

### Errors

Each error from server should be in next format:

- **code**: a unique code of an error. Used to identify error from the dictionary.
- **target**: some sort of error scope
	- **field** - the error related to certain field
	- **common** - the error related to whole request
- *message* (*OPTIONAL*): the error message for developers (use it only for debug purposes)
- *source* (*OPTIONAL*): a container for additional data. Arbitrary structure:
	( **field**: resource object attribute name. Required if target set to field. )

Example:
	 	 	
```json
{
  "errors": [
    {
      "code": "insufficient_funds",
      "target": "common",
      "message": "Hi Nick, it seems that user has empty balance"
    },
    {
      "code": "invalid_punctuation",
      "target": "field",
      "source": {
        "field": "userPassword"
      },
      "message": "Hi Vova, it seems that the password provided is missing a punctuation character"
    },
    {
      "code": "invalid_password_confirmation",
      "target": "field",
      "source": {
        "field": "userPassword",
        "someAdditionalData": "bla bla bla"
      },
      "message": "Hi Lesha, it seems that the password and password confirmation fields do not match"
    }
  ]
}
```


#### Error codes

| Code | Description |
| ----------- | ----------- |
| 400 | If there is an error in request. E.g. wrong sort options, page number and etc |
| 401 | Use it only if a user has wrong credentials (token, login/password) |
| 403 | If a user has not had permission to the requested resource |
| 422 | If there are validation errors |


**[⬆ back to top](#markdown-header-table-of-contents)**

---
