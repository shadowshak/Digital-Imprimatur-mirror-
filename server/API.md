# Digital Imprimatur API

## Table of Contents

1. [Data Types](#data-types)
   1. [Bytes](#bytes)
   2. [User ID](#user-id)
   3. [Access Token](#access-token)
   4. [Submission ID](#submission-id)
   5. [Document ID](#document-id)
   6. [Feedback ID](#feedback-id)
   7. [Role](#role)
2. [API Endpoints](#api-endpoints)
   1. [User Management](#user-management)
      1. [Create User](#create-user)
      2. [Login User](#login-user)
      3. [Invalidate User Login](#invalidate-login)
   2. [Submission API](#submission-api)
      1. [Create Submission](#create-submission)
      2. [Read Submission](#read-submission)
      3. [Update Submission](#update-submission)
      4. [Delete Submission](#delete-submission)
      5. [List Submission Documents](#list-documents)
      6. [List Submission Feedback](#list-feedback)
   3. [Documents API](#document-api)
      1. [Upload Document](#upload-document)
      2. [Read Document](#read-document)
      3. [Download Document](#download-document)
      4. [Delete Document](#delete-document)
   4. [Feedback API](#feedback-api)
      1. [Upload Feedback](#upload-feedback)
      2. [Read Feedback](#read-feedback)
      3. [Download Feedback](#download-feedback)
      4. [Delete Feedback](#delete-feedback)
3. [Work-In-Progress](#wip)

## Data Types

All responses and requests should be JSON unless otherwise specified. In addition to the usual string, number, boolean, object and array types, there are a few other data types used in this API.

### Bytes

A binary file encoded as a string using Base64 encoding.

### User ID

Represents a user account. Internally this data type is represented as a v4 UUID.

### Access Token

Represents a login session. Internally this data type is represented as a v4 UUID.

### Submission ID

Represents a unique submission. Internally this data type is represented as a v4 UUID.

### Document ID

Represents a unique document. Internally this data type is represented as a v4 UUID.

### Feedback ID

Represents a unique feedback document. Internally this data type is represented as a v4 UUID.

### Role

Represents the role of either a publisher, reviewer, or an admin. This can be represented either as a string, number, or enum (IDK if they have these in javascript). As a string, "publisher" refers to the publisher role, "reviewer" refers to the reviewer role, and "admin" refers to the admin role. Any other string is not a valid role. As a number, 0 refers to a publisher, 1 refers to a reviewer, and 2 refers to an admin. As an enum, Role.Publisher refers to the publisher, Role.Reviewer refers to the reviewer, and Role.Admin refers to the admin role.

### DocumentMetadata

```
{

}
```

[Work-In-Progress](#wip)

### FeedbackMetadata

```
{

}
```

[Work-In-Progress](#wip)

## API Endpoints

### User Management

#### Create User

**POST** `/user/create`

##### Description

Creates a new user account. Takes user information as a parameter, and returns the user id for the new account.

##### Request

```
{
    user_name:  string,
    email:      string,
    first_name: string,
    last_name:  string,
    password:   string,

    role:       Role
}
```

##### Contraints

- `user_name.length` <= `32`
- `email.length` <= `64`
- `first_name.length` <= `32`
- `last_name.length` <= `32`
- `password.length` <= `32`

##### Error Conditions

- User already exists
- Not a valid email
- Not valid JSON
- Invalid role

##### Response

```
{
    user_id: UserId
}
```

#### Login User

**POST** `/user/login`

##### Description

Logs a user in with a user id, password, and role, returning the user id and creating an access token for the new session created.

##### Request

```
{
    user_name:  String,
    password:   String,
    role:       Role
}
```

##### Error Conditions

- The user id doesn't exist
- The password isn't right
- The role specified isn't the same as the user's role

##### Response

```
{
    user_id:    UserId,
    token:      AccessToken
}
```

#### Invalidate Login

**POST** `/user/invalidate`

##### Description

Invalidates a logged in session. The access token will be rendered invalid.

##### Request

```
{
    user_id: UserId,
    token:   AccessToken
}
```

##### Error Conditions

- `user_id` does not exist
- `user_id` is not logged in
- `token` is not a valid session
- `token` is not associated with `user_id`

##### Response

```
{

}
```


### Submission API

#### Create Submission

**POST** `/sub/create`

##### Description

Creates a new submission

##### Request

```
{
    token:          AccessToken,
    name:           String,
    author:         String,
    description:    String
}
```

##### Constraints

- `name.length` <= `100`
- `author.length` <= `100`
- `description.length` <= `1000`

##### Error Conditions

- The user session doesn't have permissions to create submissions
- `token` is a reviewer

##### Response

```
{
    submission_id: SubmissionId
}
```

#### Read Submission

**POST** `/sub/read`

##### Description

Reads the metadata for a submission.

##### Request

```
{
    token:          AccessToken,
    submission_id:  SubmissionId
}
```

##### Error Conditions

- `token` doesn't exist
- `submission_id` doesn't exist
- `token` doesn't have access to `submission_id`

##### Response

```
{
    metadata: {
        name:        String,
        author:      String,
        description: String,
    }
}
```

#### Update Submission

**POST** `/sub/update`

##### Description

Updates the metadata for a subscription

##### Request

```
{
    token:              AccessToken,
    submission_id:      SubmissionId,
    delta: {
        name?:          String,
        author?:        String,
        description?:   String,
    }
}
```

##### Constraints

- `name.length` <= `100`
- `author.length` <= `100`
- `description.length` <= `1000`

##### Error Conditions

- `token` doesn't exist
- `token` is a reviewer
- `submission_id` doesn't exist
- `token` doesn't have access to `submission_id`

##### Response

```
{

}
```

#### Delete Submission

**POST** `/sub/delete`

##### Description

Deletes a submission.

##### Request

```
{
    token:          AccessToken,
    submission_id:  SubmissionId
}
```

##### Error Conditions

- `token` doesn't exist
- `token` is a reviewer
- `submission_id` doesn't exist
- `token` doesn't have access to `submission_id`
- `token` doesn't have delete permissions for `submission_id`

##### Response

```
{

}
```

#### List Documents

**POST** `/sub/read/documentument`

##### Description

Lists the Documents associated with a submission.

##### Request

```
{
    token:          AccessToken,
    submission_id:  SubmissionId,
}
```

##### Error Conditions

- `token` doesn't exist
- `submission_id` doesn't exist
- `token` doesn't have access to `submission_id`

##### Response

```
{
    document_ids: [ DocumentId ]
}
```

#### List Feedback

**POST** `/sub/read/feedback`

##### Description

Lists the Feedback associated with a submission.

##### Request

```
{
    token:          AccessToken,
    submission_id:  SubmissionId,
}
```

##### Error Conditions

- `token` doesn't exist
- `submission_id` doesn't exist
- `token` doesn't have access to `submission_id`

##### Response

```
{
    feedback_ids: [ FeedbackId ]
}
```

### Document API

#### Upload Document

**POST** `/document/upload`

##### Description

Uploads a new document, which will be associated with a submission.

##### Request

```
{
    token:          AccessToken,
    submission_id:  SubmissionId,
    document:       Bytes
}
```

##### Constraints

- There should be some constraint on `document`'s length

##### Error Conditions

- `token` doesn't exist
- `token` is a reviewer
- `submission_id` doesn't exist
- `token` doesn't have access to `submission_id`
- `token` doesn't have write permission for `submission_id`

##### Response

```
{
    document_id: DocumentId
}
```

#### Read Document

**POST** `/document/read`

##### Description

Reads the metadata for a submitted document

##### Request

```
{
    token:          AccessToken,
    document_id:    DocumentId
}
```

##### Error Conditions

- `token` doesn't exist
- `token` is a reviewer
- `document_id` doesn't exist
- `token` doesn't have access to `document_id`

##### Response

```
{
    metadata: DocumentMetadata
}
```

#### Download Document

**POST** `/document/download`

##### Description

Downloads a file.

##### Request

```
{
    token:          AccessToken,
    document_id:    DocumentId
}
```

##### Error Conditions

- `token` doesn't exist
- `token` is a reviewer
- `document_id` doesn't exist
- `token` doesn't have access to `document_id`

##### Response

Responds with the contents of the file as binary.

#### Delete Document

**POST** `/document/delete`

##### Description

Deletes a document? should we be able to do this

##### Request

```
{
    token:          AccessToken,
    document_id:    DocumentId
}
```

##### Error Conditions

- `token` doesn't exist
- `token` is a reviewer
- `document_id` doesn't exist
- `token` doesn't have access to `document_id`
- `token` doesn't have delete access to `document_id`


##### Response

```
{

}
```

### Feedback API

#### Upload Feedback

**POST** `/feedback/upload`

##### Description

Uploads a new feedback document, which will be associated with a submission.

##### Request

```
{
    token:          AccessToken,
    submission_id:  SubmissionId,
    document:       Bytes
}
```

##### Constraints

- There should be some constraint on `document`'s length

##### Error Conditions

- `token` doesn't exist
- `submission_id` doesn't exist
- `token` is a publisher
- `token` doesn't have access to `submission_id`
- `token` doesn't have write permission for `submission_id`

##### Response

```
{
    feedback_id: FeedbackId
}
```

#### Read Feedback

**POST** `/feedback/read`

##### Description

Reads the metadata for a submitted feedback document

##### Request

```
{
    token:          AccessToken,
    feedback_id:    FeedbackId
}
```

##### Error Conditions

- `token` doesn't exist
- `feedback_id` doesn't exist
- `token` doesn't have access to `feedback_id`

##### Response

```
{
    metadata: FeedbackMetadata
}
```

#### Download Feedback

**POST** `/feedback/download`

##### Description

Downloads a file.

##### Request

```
{
    token:          AccessToken,
    feedback_id:    FeedbackId
}
```

##### Error Conditions

- `token` doesn't exist
- `feedback_id` doesn't exist
- `token` doesn't have access to `feedback_id`

##### Response

Responds with the contents of the file as binary.

#### Delete Feedback

**POST** `/feedback/delete`

##### Description

Deletes a feedback? should we be able to do this

##### Request

```
{
    token:          AccessToken,
    feedback_id:    FeedbackId
}
```

##### Error Conditions

- `token` doesn't exist
- `token` is a publisher
- `feedback_id` doesn't exist
- `token` doesn't have access to `feedback_id`
- `token` doesn't have delete access to `feedback_id`


##### Response

```
{

}
```

## WIP

- ChangePassword
- GetUserInfo
- Permission Managment