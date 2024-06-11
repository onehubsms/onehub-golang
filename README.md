# Onehub Golang Sending SMS Library
```golang
package main

import (
    "fmt"
    "io"
    "log"
    "net/http"
    "strings"
)

func main() {
    client := &http.Client{}
    var data = strings.NewReader(`{"phoneNumbers": "+2547XXXXXXXX,+2547XXXXXXXX", "message": "Hello Api!","senderId":"Onehub"}`)
    req, err := http.NewRequest("POST", "https://api.onehub.co.ke/v1/sms/send", data)
    if err != nil {
        log.Fatal(err)
    }
    req.Header.Set("Content-Type", "application/json")
    req.Header.Set("x-api-user", "API_USERNAME_HERE")
    req.Header.Set("x-api-key", "API_KEY_HERE")
    resp, err := client.Do(req)
    if err != nil {
        log.Fatal(err)
    }
    defer resp.Body.Close()
    bodyText, err := io.ReadAll(resp.Body)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("%s\n", bodyText)
}
```
# Username and API Key
You can get your username and API Key [here](https://dashboard.onehub.co.ke/account/0/user/signup).
# Request Body Parameters
`phoneNumbers` - `[Type: String]` `[Required]` - Phone numbers of the recipients in international format with the plus (+) sign. Multiple numbers should be comma-separated.

`Message` - `[Type: String]` `[Required]` - The message you want to send. Each message is counted as 160 characters long. Messages longer than 160 characters will be billed accordingly.

`Sender ID` - `[Type: String]` `[Required]` - A sender ID serves as a branding for outgoing messages, typically representing your company name. Your users will receive messages with your specified sender ID. You can apply for your sender ID on our [website](https://onehub.co.ke/).
# Response Body Parameters
## Response in case of successful sending of a message:
```json
{
    "0":{
        "country":"kenya",
        "cost":"KES 2.00",
        "success":[
            "+2547XXXYXXXX",
            "+2547XXXXXXXX"
        ]
    },
    "status":200,
    "message":"Message sent"
}
```
## Response in case a message fails:
```json
{
   "0":{
      "country":"kenya",
      "cost":"KES 1.00",
      "success":[
         "+2547XXXYXXXX"
      ],
      "failed":[
         "+2547XXXXXXXX"
      ]
   },
   "status":200,
   "message":"Message sent"
}
```
## Response in case of wrong credentials:
```json
{
    "status":401,
    "message":"Unauthorized"
}
```
# Onehub Golang Add Contacts Library
```golang
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "strconv"
)

func main() {
    // endpoint
    var addContactURL string = "https://api.braceafrica.com/v1/contacts/add"

    // authentication
    var x_username string    = ""
    var x_apikey string      = ""

    // data
    createContact := map[string]string{
        "name":   "John Migo",
        "phone":  "+254711xxxxxx",
        "tags":   "developer,nairobi",
        "groups": "Nairobi Coding, Group 2",
    }

    params, _ := json.Marshal(createContact)

    // request
    request, err := http.NewRequest("POST", addContactURL, bytes.NewBuffer(params))

    if err != nil {
        log.Fatal(err)
    }

    request.Header.Add("Content-Type", "application/json")
    request.Header.Set("x-api-user", x_username)
    request.Header.Set("x-api-key", x_apikey)
    request.Header.Set("Content-Length", strconv.Itoa(len(params)))

    // response
    response, err := http.DefaultClient.Do(request)

    if err != nil {
        panic(err.Error())
    }

    body, err := ioutil.ReadAll(response.Body)

    if err != nil {
        panic(err.Error())
    }

    defer response.Body.Close()

    fmt.Println(string(body))
}
```
# Request Body Parameters
`name` - `[Type: String]` `[Required]` 

`phone` -	`[Type: String]` `[Required]` - Must be in international format.

`tags` - `[Type: String]`	`[Optional]` - A unique field to help group contacts e.g football,team,family.

`groups` - `[Type: String]`	`[Optional]` - This is a group name that the contacts will be added to. It must be an existing group.
# Response Body Parameters
## Response in case of successful adding of a contact:
```json
{
    "status": 200,
    "message": "Contact added"
}
```
# Onehub Golang Edit Contacts Library
```golang
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    // id of contact to edit: this can be gotten from the fetch contacts api
    var contactId string      = ""

    // endpoint
    var editContactURL string = "https://api.braceafrica.com/v1/contacts/edit/" + contactId

    // authentication
    var x_username string     = ""
    var x_apikey string       = ""

    // data
    newContactDetails := map[string]string{
        "name": "",  // new contact name
        "phone": "", // new phonenumber - international format
        "tags": "",  // new tags - comma separated tags
        "groups":""  // new groups - optional groups to link contact
    }

    params, _ := json.Marshal(newContactDetails)

    // request
    request, err := http.NewRequest("POST", editContactURL, bytes.NewBuffer(params))

    if err != nil {
        panic(err.Error())
    }

    // headers
    request.Header.Set("Content-Type", "application/json")
    request.Header.Set("x-api-user", x_username)
    request.Header.Set("x-api-key", x_apikey)

    response, err := http.DefaultClient.Do(request)

    if err != nil {
        panic(err.Error())
    }

    body, err := ioutil.ReadAll(response.Body)

    if err != nil {
        panic(err.Error())
    }

    defer response.Body.Close()

    fmt.Println(string(body))
}
```
# Request Body Parameters
`name` - `[Type: String]` `[Required]` 

`phone` -	`[Type: String]` `[Required]` - Must be in international format.

`tags` - `[Type: String]`	`[Optional]` - A unique field to help group contacts e.g football,team,family.

`groups` - `[Type: String]`	`[Optional]` - This is a group name that the contacts will be added to. It must be an existing group.
# Response Body Parameters
## Response in case of successful adding of a contact:
```json
{
    "status": 200,
    "message": "Contact has been updated"
}
```
# Onehub Golang Fetch Contacts Library
```golang
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    var fetchContactsURL string = "https://api.braceafrica.com/v1/contacts/fetch"

    // authentication
    var x_username string     = ""
    var x_apikey string       = ""

    // request
    request, err := http.NewRequest("GET", fetchContactsURL, nil)

    if err != nil {
        panic(err.Error())
    }

    // headers
    request.Header.Set("Content-Type", "application/json")
    request.Header.Set("x-api-user", x_username)
    request.Header.Set("x-api-key", x_apikey)
    // response
    response, err := http.DefaultClient.Do(request)

    if err != nil {
        panic(err.Error())
    }

    body, _ := ioutil.ReadAll(response.Body)

    fmt.Println(string(body))

}
```
# Request Body Parameters
`name` - `[Type: String]` `[Required]` 

`phone` -	`[Type: String]` `[Required]` - Must be in international format.

`tags` - `[Type: String]`	`[Optional]` - A unique field to help group contacts e.g football,team,family.

`groups` - `[Type: String]`	`[Optional]` - This is a group name that the contacts will be added to. It must be an existing group.
# Response Body Parameters
## Response in case of successful fetching of a contact:
```json
{
    "status": 200,
    "contacts": [
        {
            "id": 2,
            "name": "Jane Mwaura",
            "phoneNumber": "+254712345678",
            "tags": "kenya,nairobi",
            "groups": [],
            "createdOn": "2019-12-02T00:00:00.000Z",
            "lastEditedOn": null
        }
    ]
}
```
# Onehub Golang Delete Contacts Library
```golang
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    // endpoint
    var deleteContactURL string = "https://api.braceafrica.com/v1/contacts/delete

    // authentication
    var x_username string       = ""
    var x_apikey string         = ""

    data := map[string][]int{
        "contactIds":[]int{1,2,3,4}
    }

    params := json.Marshal(data)

    // request
    request, err := http.NewRequest("POST", deleteContactURL, bytes.NewBuffer(params))

    if err != nil {
        panic(err.Error())
    }

    // headers
    request.Header.Set("Content-Type", "application/json")
    request.Header.Set("x-api-user", x_username)
    request.Header.Set("x-api-key", x_apikey)

    response, err := http.DefaultClient.Do(request)

    if err != nil {
        panic(err.Error())
    }

    body, _ := ioutil.ReadAll(response.Body)

    defer response.Body.Close()

    fmt.Println(string(body))
}
```
## ```!``` Please note that this will also remove the contact from all linked groups.
```json
{
    "contactIds": [1,2,3,4]
}
```
# Request Body Parameters
`name` - `[Type: String]` `[Required]` 

`phone` -	`[Type: String]` `[Required]` - Must be in international format.

`tags` - `[Type: String]`	`[Optional]` - A unique field to help group contacts e.g football,team,family.

`groups` - `[Type: String]`	`[Optional]` - This is a group name that the contacts will be added to. It must be an existing group.
# Onehub Golang Create Group Library
```golang
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
)

func main() {
    // endpoint
    var addGroupURL string = "https://api.braceafrica.com/v1/contacts/groups/add"

    // authentication
    var x_username string = ""
    var x_apikey string = ""

    // data
    groupData := map[string]string{
        "name": "",
        "tags": "",
    }

    params, _ := json.Marshal(groupData)

    request, err := http.NewRequest("POST", addGroupURL, bytes.NewBuffer(params))

    request.Header.Add("Content-Type", "application/json")
    request.Header.Set("x-api-user", x_username)
    request.Header.Set("x-api-key", x_apikey)
    request.Header.Set("Content-Length", strconv.Itoa(len(params)))

    response, err := http.DefaultClient.Do(request)

    if err != nil {
        panic(err.Error())
    }

    body, err := ioutil.ReadAll(response.Body)

    if err != nil {
        panic(err.Error())
    }

    defer response.Body.Close()

    fmt.Println(string(body))
}
```
# Request Body Parameters
`name` - `[Type: String]` `[Required]` - The group name.

`tags` - `[Type: String]`	`[Optional]` - These are tags in the contacts you want to link. The group will be formed and the contacts automatically linked to the group.
# Onehub Golang Edit Group Library
```golang
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    var groupId string = ""
    // endpoint
    var editGroupURL string = "https://api.braceafrica.com/v1/contacts/groups/edit/" + groupId

    // authentication
    var x_username string = ""
    var x_apikey string = ""

    // data
    groupData := map[string]string{
        "name": "",
    }

    params, _ := json.Marshal(groupData)

    request, err := http.NewRequest("POST", editGroupURL, bytes.NewBuffer(params))

    if err != nil {
        panic(err.Error())
    }

    // headers
    request.Header.Set("Content-Type", "application/json")
    request.Header.Set("x-api-user", x_username)
    request.Header.Set("x-api-key", x_apikey)

    response, err := http.DefaultClient.Do(request)

    if err != nil {
        panic(err.Error())
    }

    body, err := ioutil.ReadAll(response.Body)

    if err != nil {
        panic(err.Error())
    }

    fmt.Println(string(body))
}
```
# Request Body Parameters
`name` - `[Type: String]` `[Required]` - The group name.

`tags` - `[Type: String]`	`[Optional]` - These are tags in the contacts you want to link. The group will be formed and the contacts automatically linked to the group.
# Response Body Parameters
## Response in case of successful editing of a group:
```json
{
    "status": 200,
    "message": "Group has been updated"
}
```
# Onehub Golang Fetch Group Library
```golang
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    // endpoint
    var fetchGroupsURL string = "https://api.braceafrica.com/v1/contacts/groups/fetch"

    // authentication
    var x_username string = ""
    var x_apikey string = ""

    // request
    request, err := http.NewRequest("GET", fetchGroupsURL, nil)

    if err != nil {
        panic(err.Error())
    }

    // headers

    request.Header.Set("Content-Type", "application/json")
    request.Header.Set("x-api-user", x_username)
    request.Header.Set("x-api-key", x_apikey)

    // response
    response, err := http.DefaultClient.Do(request)

    if err != nil {
        panic(err.Error())
    }

    body, _ := ioutil.ReadAll(response.Body)

    defer response.Body.Close()

    fmt.Println(string(body))
}
```
# Request Body Parameters
`name` - `[Type: String]` `[Required]` - The group name.

`tags` - `[Type: String]`	`[Optional]` - These are tags in the contacts you want to link. The group will be formed and the contacts automatically linked to the group.
# Response Body Parameters
## Response in case of successful fetching of a group:
```json
{
    "status": 200,
    "groups": [
        {
            "groupId": 12,
            "groupName": "Kenzu Safaris",
            "createdOn": "2019-05-17T00:00:00.000Z",
            "contacts": []
        }
    ]
}
```
# Onehub Golang Link Contacts To Group Library
```golang
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    var groupId string = ""
    // endpoint
    var addContactsToGroupURL string = "https://api.braceafrica.com/v1/contacts/add/"+groupId

    // authentication

    var x_username string = ""
    var x_apikey string = ""

    // data

    data:= map[string][]int{
        "contactIds":[]int{1,2,3,4}
    }

    params, _ := json.Marshal(data)

    // request

    request, err := http.NewRequest("POST", addContactsToGroupURL, bytes.NewBuffer(params))

    if err != nil {
        panic(err.Error())
    }

    // headers
    request.Header.Set("Content-Type", "application/json")
    request.Header.Set("x-api-user", x_username)
    request.Header.Set("x-api-key", x_apikey)

    // response
    response, err := http.DefaultClient.Do(request)

    if err != nil {
        panic(err.Error())
    }

    body, err := ioutil.ReadAll(response.Body)

    if err != nil {
        panic(err.Error())
    }

    defer response.Body.Close()

    fmt.Println(string(body))
}
```
# Request Body Parameters
`name` - `[Type: String]` `[Required]` - The group name.

`tags` - `[Type: String]`	`[Optional]` - These are tags in the contacts you want to link. The group will be formed and the contacts automatically linked to the group.
# Response Body Parameters
## Response in case of successful linking contacts to a group:
```json
{
    "status": 200,
    "message": "The contacts linked to group"
}
```
# Onehub Golang Fetch Group Contacts Library
```golang
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    var groupId string = ""
    // endpoint
    var fetchGroupContactsURL string = "https://api.braceafrica.com/v1/contacts/groups/fetch/" + groupId

    // authentication
    var x_username string = ""
    var x_apikey string = ""

    // request
    request, err := http.NewRequest("GET", fetchGroupContactsURL, nil)

    if err != nil {
        panic(err.Error())
    }

    // headers
    request.Header.Set("Content-Type", "application/json")
    request.Header.Set("x-api-user", x_username)
    request.Header.Set("x-api-key", x_apikey)

    // response
    response, err := http.DefaultClient.Do(request)

    if err != nil {
        panic(err.Error())
    }

    body, err := ioutil.ReadAll(response.Body)

    if err != nil {
        panic(err.Error())
    }

    defer response.Body.Close()

    fmt.Println(string(body))

}
```
# Request Body Parameters
`name` - `[Type: String]` `[Required]` - The group name.

`tags` - `[Type: String]`	`[Optional]` - These are tags in the contacts you want to link. The group will be formed and the contacts automatically linked to the group.
# Response Body Parameters
## Response in case of successful fetching group contacts:
```json
{
    "groupId": 2536,
    "groupName": "Chama Ya Mama",
    "contacts": [
        {
            "id": 65,
            "name": "John Doe",
            "phoneNumber": "+2547xxx4578",
            "tags": "chairman"
        },
        {
            "id": 63,
            "name": "Pendo JM",
            "phoneNumber": "+2547xxy4597",
            "tags": "member"
        }
    ]
}
```
# Onehub Golang Remove Group Contacts Library
```golang
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    var groupId string = ""
    var deleteGroupContactsURL string = "https://api.braceafrica.com/v1/contacts/delete/"+groupId

    // authentication
    var x_username string = ""
    var x_apikey string = ""

    // data
    contactIds := map[string][]int{
        "contactIds":[]int{1,2,3,4}
    }

    params, _ := json.Marshal(contactIds)

    // request
    request, err := http.NewRequest("POST", deleteGroupContactsURL, bytes.NewBuffer(params))

    if err != nil {
        panic(err.Error())
    }

    request.Header.Set("Content-Type", "application/json")
    request.Header.Set("x-api-user", x_username)
    request.Header.Set("x-api-key", x_apikey)

    // response
    response, err := http.DefaultClient.Do(request)

    if err != nil {
        panic(err.Error())
    }

    body, err := ioutil.ReadAll(response.Body)

    if err != nil {
        panic(err.Error())
    }

    fmt.Println(string(body))
}
```
# Request Body Parameters
`name` - `[Type: String]` `[Required]` - The group name.

`tags` - `[Type: String]`	`[Optional]` - These are tags in the contacts you want to link. The group will be formed and the contacts automatically linked to the group.
# Response Body Parameters
## Response in case of successful removing group contacts:
```json
{
    "status": 200,
    "message": "Contacts removed form group"
}
```
# Onehub Golang Delete Groups Library
```golang
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    // endpoint
    var deleteGroupURL string = "https://api.braceafrica.com/v1/contacts/groups/delete"

    // authentication

    var x_username string = ""
    var x_apikey string = ""

    groupIds := map[string][]int{
        "groupIds":[]int{1,2,3,4}
    }

    params, _ := json.Marshal(groupIds)

    // request
    request, err := http.NewRequest("POST", deleteGroupURL, bytes.NewBuffer(params))

    if err != nil {
        panic(err.Error())
    }

    // headers
    request.Header.Set("Content-Type", "application/json")
    request.Header.Set("x-api-user", x_username)
    request.Header.Set("x-api-key", x_apikey)

    // response
    response, err := http.DefaultClient.Do(request)

    if err != nil {
        panic(err.Error())
    }

    body, err := ioutil.ReadAll(response.Body)

    if err != nil {
        panic(err.Error())
    }

    fmt.Println(string(body))

}
```
# Request Body Parameters
`name` - `[Type: String]` `[Required]` - The group name.

`tags` - `[Type: String]`	`[Optional]` - These are tags in the contacts you want to link. The group will be formed and the contacts automatically linked to the group.
# Response Body Parameters
## Response in case of successful deleting groups:
```json
{
    "status": 200,
    "message": "4 groups have been deleted"
}
```
# Onehub Golang Sender Ids Library
```golang
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    // endpoint
    var fetchSenderidsURL string = "https://api.braceafrica.com/v1/sms/senderIds/fetch"

    // authentication
    var x_username string = ""
    var x_apikey string = ""

    // request
    request, err := http.NewRequest("GET", fetchSenderidsURL, nil)

    if err != nil {
        panic(err.Error())
    }

    // headers
    request.Header.Set("Content-Type", "application/json")
    request.Header.Set("x-api-user", x_username)
    request.Header.Set("x-api-key", x_apikey)

    // response
    response, err := http.DefaultClient.Do(request)

    if err != nil {
        panic(err.Error())
    }

    body, err := ioutil.ReadAll(response.Body)

    defer response.Body.Close()

    fmt.Println(string(body))
}
```
# Response Body Parameters
## Response in case of successful fetching of Sender Ids:
```json
{
    "status": 200,
    "senderids": [
        {
            "sender_id": "BraceAfrica",
            "country": "Kenya",
            "status": "active"
        },
        {
            "sender_id": "JubaPay",
            "country": "Kenya",
            "status": "active"
        },
        {
            "sender_id": "Foleni",
            "country": "Uganda",
            "status": "active"
        }
    ]
}
```

