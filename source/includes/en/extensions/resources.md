## Resources

The **resources** extension allows the storage of documents in the server in an isolated space for each chatbot, similar to the **bucket** extension. The main difference is that these documents can be mapped as **contents** for messages sent to the chatbot destinations, through the resource **key**. This means that the chatbot developer can choose to **store the content of its messages in the server** instead of keeping them on the chatbot code side.

To manage all resources programmatically, use **resources** extension sending a command with the following properties:

| Name | Description |
|---------------------------------|--------------|
| id    | Unique identifier of the command.   |
| method    | The command verb  |
| resource | The resource document. |
| type | The type of the resource document |
| uri    | **/resources**   |
| to     | **postmaster@msging.net** (not required) |

The **BLiP** portal offers a resource management interface which helps with the edition of content, avoiding the need to update the code on the application side in case of changes in the chatbot.

In order to send a resource message, the developer must use the [**resource** content type](#resource).

### Add a **media link** resource

```http
POST https://msging.net/commands HTTP/1.1
Content-Type: application/json
Authorization: Key {YOUR_TOKEN}

{  
  "id": "1",
  "method": "set",
  "uri": "/resources/xyz1234",
  "type": "application/vnd.lime.media-link+json",
  "resource": {
    "title": "Cat",
    "text": "Here is a cat image for you!",
    "type": "image/jpeg",
    "uri": "http://2.bp.blogspot.com/-pATX0YgNSFs/VP-82AQKcuI/AAAAAAAALSU/Vet9e7Qsjjw/s1600/Cat-hd-wallpapers.jpg",
    "size": 227791,
    "previewUri": "https://encrypted-tbn3.gstatic.com/images?q=tbn:ANd9GcS8qkelB28RstsNxLi7gbrwCLsBVmobPjb5IrwKJSuqSnGX4IzX",
    "previewType": "image/jpeg"
  }
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": "1",
  "from": "postmaster@msging.net/#irismsging1",
  "to": "contact@msging.net/default",
  "method": "set",
  "status": "success"
}
```

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Lime.Protocol;
using Take.Blip.Client;
using Take.Blip.Client.Receivers;
using Take.Blip.Client.Extensions.Resource;
using Lime.Messaging.Contents;

namespace Extensions
{
    public class ResourceMessageReceiver : IMessageReceiver
    {
        private IResourceExtension _resourceExtension;

        public ResourceMessageReceiver(IResourceExtension resourceExtension)
        {
            _resourceExtension = resourceExtension;
        }
        public async Task ReceiveAsync(Message message, CancellationToken cancellationToken = default(CancellationToken))
        {
            var mediaLink = new MediaLink
            {
                Title = "Cat",
                Text = "Here is a cat image for you!",
                Uri = new Uri("http://2.bp.blogspot.com/-pATX0YgNSFs/VP-82AQKcuI/AAAAAAAALSU/Vet9e7Qsjjw/s1600/Cat-hd-wallpapers.jpg"),
                Size = 227791,
                Type = MediaType.Parse("image/jpeg"),
                PreviewType = MediaType.Parse("image/jpeg"),
                PreviewUri = new Uri("https://encrypted-tbn3.gstatic.com/images?q=tbn:ANd9GcS8qkelB28RstsNxLi7gbrwCLsBVmobPjb5IrwKJSuqSnGX4IzX")
            };

            await _resourceExtension.SetAsync("xyz1234", mediaLink);
        }
    }
}
```

Storing a `media link` document with `xyz1234` key.

### Add a **text/plain** resource


```http
POST https://msging.net/commands HTTP/1.1
Content-Type: application/json
Authorization: Key {YOUR_TOKEN}

{  
  "id": "2",
  "method": "set",
  "uri": "/resources/help-message",
  "type": "text/plain",
  "resource": "To use our services, please send a text message."
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": "2",
  "from": "postmaster@msging.net/#irismsging1",
  "to": "contact@msging.net/default",
  "method": "set",
  "status": "success"
}
```

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Lime.Protocol;
using Take.Blip.Client;
using Take.Blip.Client.Receivers;
using Take.Blip.Client.Extensions.Resource;
using Lime.Messaging.Contents;

namespace Extensions
{
    public class ResourceMessageReceiver : IMessageReceiver
    {
        private IResourceExtension _resourceExtension;

        public ResourceMessageReceiver(IResourceExtension resourceExtension)
        {
            _resourceExtension = resourceExtension;
        }
        public async Task ReceiveAsync(Message message, CancellationToken cancellationToken = default(CancellationToken))
        {
            var plainText = new PlainText
            {
                Text = "To use our services, please send a text message."
            };

            await _resourceExtension.SetAsync("help-message", plainText);   
        }
    }
}
```

Storing a `text plain` document with `help-message` key.

### Get all Resources

```http
POST https://msging.net/commands HTTP/1.1
Content-Type: application/json
Authorization: Key {YOUR_TOKEN}

{  
  "id": "3cbdd83c-d7ad-4d1e-886a-a0dffb96fd37",
  "method": "get",
  "uri": "/resources"
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "id": "3cbdd83c-d7ad-4d1e-886a-a0dffb96fd37",
    "type": "application/vnd.lime.collection+json",
    "resource": {
        "total": 1,
        "itemType": "text/plain",
        "items": [
            "xyz1234"
        ]
    },
    "method": "get",
    "status": "success",
    "from": "postmaster@msging.net/#az-iris1",
    "to": "docstest@msging.net",
    "metadata": {
        "#command.uri": "lime://docstest@msging.net/resources"
    }
}
```

Getting all bot resources.

### Get a specific resource

```http
POST https://msging.net/commands HTTP/1.1
Content-Type: application/json
Authorization: Key {YOUR_TOKEN}

{  
  "id": "78981a10-d7a9-4fbb-84cf-1916a8ed93b8",
  "method": "get",
  "uri": "/resources/xyz1234"
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "id": "78981a10-d7a9-4fbb-84cf-1916a8ed93b8",
    "type": "application/vnd.lime.media-link+json",
    "resource": {
        "type": "image/jpeg",
        "size": 227791,
        "uri": "http://2.bp.blogspot.com/-pATX0YgNSFs/VP-82AQKcuI/AAAAAAAALSU/Vet9e7Qsjjw/s1600/Cat-hd-wallpapers.jpg",
        "previewUri": "https://encrypted-tbn3.gstatic.com/images?q=tbn:ANd9GcS8qkelB28RstsNxLi7gbrwCLsBVmobPjb5IrwKJSuqSnGX4IzX",
        "previewType": "image/jpeg",
        "title": "Cat",
        "text": "Here is a cat image for you!"
    },
    "method": "get",
    "status": "success",
    "from": "postmaster@msging.net/#az-iris1",
    "to": "docstest@msging.net",
    "metadata": {
        "#command.uri": "lime://docstest@msging.net/resources/xyz1234"
    }
}
```

Getting a specific resource by id.

### Delete a specific resource

```http
POST https://msging.net/commands HTTP/1.1
Content-Type: application/json
Authorization: Key {YOUR_TOKEN}

{  
  "id": "a07258fa-0137-4596-a67e-859a5c2ce38f",
  "method": "delete",
  "uri": "/resources/xyz1234"
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "id": "a07258fa-0137-4596-a67e-859a5c2ce38f",
    "method": "delete",
    "status": "success",
    "from": "postmaster@msging.net/#az-iris1",
    "to": "docstest@msging.net",
    "metadata": {
        "#command.uri": "lime://docstest@msging.net/resources/xyz1234"
    }
}
```

### Getting a specific resource by id.

```http
POST https://msging.net/commands HTTP/1.1
Content-Type: application/json
Authorization: Key {YOUR_TOKEN}

{  
  "id": "a07258fa-0137-4596-a67e-859a5c2ce38g",
  "method": "get",
  "uri": "/resources/xyz1234"
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "id": "a07258fa-0137-4596-a67e-859a5c2ce38g",
    "type": "application/vnd.lime.media-link+json",
    "resource": {
        "type": "image/jpeg",
        "size": 227791,
        "uri": "http://2.bp.blogspot.com/-pATX0YgNSFs/VP-82AQKcuI/AAAAAAAALSU/Vet9e7Qsjjw/s1600/Cat-hd-wallpapers.jpg",
        "previewUri": "https://encrypted-tbn3.gstatic.com/images?q=tbn:ANd9GcS8qkelB28RstsNxLi7gbrwCLsBVmobPjb5IrwKJSuqSnGX4IzX",
        "previewType": "image/jpeg",
        "title": "Cat",
        "text": "Here is a cat image for you!"
    },
    "method": "get",
    "status": "success",
    "from": "postmaster@msging.net/#az-iris1",
    "to": "docstest@msging.net",
    "metadata": {
        "#command.uri": "lime://docstest@msging.net/resources/xyz1234"
    }
}
```

#### Replacement variables

It is possible to use contact replacement variables in the created resources. For more information, please check the documentation of the [**Contacts** extension](#contacts).

### Store a **text/plain** resource with replacement variable


```http
POST https://msging.net/commands HTTP/1.1
Content-Type: application/json
Authorization: Key {YOUR_TOKEN}

{  
  "id": "3",
  "method": "set",
  "uri": "/resources/welcome-message",
  "type": "text/plain",
  "resource": "Welcome to our service, ${contact.name}!"
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": "3",
  "from": "postmaster@msging.net/#irismsging1",
  "to": "contact@msging.net/default",
  "method": "set",
  "status": "success"
}
```

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Lime.Protocol;
using Take.Blip.Client;
using Take.Blip.Client.Receivers;
using Take.Blip.Client.Extensions.Resource;
using Lime.Messaging.Contents;

namespace Extensions
{
    public class ResourceMessageReceiver : IMessageReceiver
    {
        private IResourceExtension _resourceExtension;

        public ResourceMessageReceiver(IResourceExtension resourceExtension)
        {
            _resourceExtension = resourceExtension;
        }
        public async Task ReceiveAsync(Message message, CancellationToken cancellationToken = default(CancellationToken))
        {
            var plainText = new PlainText
            {
                Text = "Welcome to our service, ${contact.name}!"
            };

            await _resourceExtension.SetAsync("welcome-message", plainText);            
        }
    }
}
```

Storing a `text plain` document with `welcome-message` key using replacement variables.

#### Sending a resource message

[Click here](#resource) to see how can you send a resource message.