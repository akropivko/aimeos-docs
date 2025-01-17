This article contains all actions for retrieving and managing products.

!!! tip
    The product domain supports [fetching related resources](basics.md#include-related-resources).

# Get product by ID

=== "Query"
    ```graphql
    query {
      getProduct(id: "1") {
        id
        siteid
        type
        code
        label
        url
        dataset
        datestart
        dateend
        config
        instock
        scale
        status
        target
        boost
        mtime
        ctime
        editor
      }
    }
    ```
=== "Javascript"
    ```javascript
    const body = JSON.stringify({'query':
    `query {
      getProduct(id: "1") {
        id
        siteid
        type
        code
        label
        url
        dataset
        datestart
        dateend
        config
        instock
        target
        scale
        status
        boost
        mtime
        ctime
        editor
      }
    }`});

    fetch($('.aimeos').data('graphql'), {
        method: 'POST',
        credentials: 'same-origin',
        headers: { // Laravel only
            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
        },
        body: body
    }).then(response => {
        return response.json();
    }).then(data => {
        console.log(data);
    });
    ```

Response:

```json
{
  "data": {
    "getProduct": {
      "id": "113",
      "siteid": "1.",
      "type": "default",
      "code": "demo-article",
      "label": "Demo article",
      "url": "demo-article",
      "dataset": null,
      "datestart": null,
      "dateend": null,
      "config": "{}",
      "instock": null,
      "target": null,
      "scale": 1,
      "status": 1,
      "boost": 1,
      "mtime": "2023-01-13 11:25:51",
      "ctime": "2022-12-01 11:59:00",
      "editor": "aimeos@aimeos.org"
    }
  }
}
```

# Find product by code

=== "Query"
    ```graphql
    query {
      findProduct(code: "demo-article") {
        id
        siteid
        type
        code
        label
        url
        dataset
        datestart
        dateend
        config
        instock
        scale
        status
        target
        boost
        mtime
        ctime
        editor
      }
    }
    ```
=== "Javascript"
    ```javascript
    const body = JSON.stringify({'query':
    `query {
      findProduct(code: "demo-article") {
        id
        siteid
        type
        code
        label
        url
        dataset
        datestart
        dateend
        config
        instock
        scale
        status
        target
        boost
        mtime
        ctime
        editor
      }
    }`});

    fetch($('.aimeos').data('graphql'), {
        method: 'POST',
        credentials: 'same-origin',
        headers: { // Laravel only
            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
        },
        body: body
    }).then(response => {
        return response.json();
    }).then(data => {
        console.log(data);
    });
    ```

Response:

```json
{
  "data": {
    "findProduct": {
      "id": "1",
      "siteid": "1.",
      "type": "default",
      "code": "demo-article",
      "label": "Demo article",
      "url": "demo-article",
      "dataset": null,
      "datestart": null,
      "dateend": null,
      "config": "{}",
      "instock": null,
      "scale": 1,
      "status": 1,
      "target": null,
      "boost": 1,
      "mtime": "2023-01-13 11:25:51",
      "ctime": "2022-12-01 11:59:00",
      "editor": "aimeos@aimeos.org"
    }
  }
}
```

# Search products

The filter parameter is explained in the [filter section](basics.md#filtering-the-result) of the [GraphQL basics](basics.md) article.

=== "Query"
    ```graphql
    query {
      searchProducts(filter: "{\"=~\": {\"product.code\":\"demo-\"}}") {
        id
        siteid
        type
        code
        label
        url
        dataset
        datestart
        dateend
        config
        instock
        scale
        status
        target
        boost
        mtime
        ctime
        editor
      }
    }
    ```
=== "Javascript"
    ```javascript
    let filter = {
        "=~": {"product.code":"demo-"}
    };
    const fstr = JSON.stringify(filter).replace(/"/g, '\\"');
    const body = JSON.stringify({'query':
    `query {
      searchProducts(filter: "` + fstr + `") {
        id
        siteid
        type
        code
        label
        url
        dataset
        datestart
        dateend
        config
        instock
        scale
        status
        target
        boost
        mtime
        ctime
        editor
      }
    }`});

    fetch($('.aimeos').data('graphql'), {
        method: 'POST',
        credentials: 'same-origin',
        headers: { // Laravel only
            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
        },
        body: body
    }).then(response => {
        return response.json();
    }).then(data => {
        console.log(data);
    });
    ```

Response:

```json
{
  "data": {
    "searchProducts": [
      {
        "id": "1",
        "siteid": "1.",
        "type": "default",
        "code": "demo-article",
        "label": "Demo article",
        "url": "demo-article",
        "dataset": null,
        "datestart": null,
        "dateend": null,
        "config": "{}",
        "instock": null,
        "scale": 1,
        "status": 1,
        "target": null,
        "boost": 1,
        "mtime": "2023-01-13 11:25:51",
        "ctime": "2022-12-01 11:59:00",
        "editor": "aimeos@aimeos.org"
      },
      {
        "id": "2",
        "siteid": "1.",
        "type": "default",
        "code": "demo-selection-article-1",
        "label": "Demo variant article 1",
        "url": "demo-variant-article-1",
        "dataset": null,
        "datestart": null,
        "dateend": null,
        "config": "{}",
        "instock": null,
        "scale": 1,
        "status": 1,
        "target": null,
        "boost": 1,
        "mtime": "2022-12-01 11:59:05",
        "ctime": "2022-12-01 11:59:05",
        "editor": "core"
      }
    ]
  }
}
```

# Save single product

=== "Mutation"
    ```graphql
    mutation {
      saveProduct(input: {
        code: "test"
        label: "Test product"
      }) {
        id
      }
    }
    ```
=== "Javascript"
    ```javascript
    const body = JSON.stringify({'query':
    `mutation {
      saveProduct(input: {
        code: "test"
        label: "Test product"
      }) {
        id
      }
    }`});

    fetch($('.aimeos').data('graphql'), {
        method: 'POST',
        credentials: 'same-origin',
        headers: { // Laravel only
            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
        },
        body: body
    }).then(response => {
        return response.json();
    }).then(data => {
        console.log(data);
    });
    ```

Response:

```json
{
  "data": {
    "saveProduct": {
      "id": "18"
    }
  }
}
```

# Save multiple products

=== "Mutation"
    ```graphql
    mutation {
      saveProducts(input: [{
        code: "test-2"
        label: "Test 2 product"
      },{
        code: "test-3"
        label: "Test 3 product"
      }]) {
        id
      }
    }
    ```
=== "Javascript"
    ```javascript
    const body = JSON.stringify({'query':
    `mutation {
      saveProducts(input: [{
        code: "test-2"
        label: "Test 2 product"
      },{
        code: "test-3"
        label: "Test 3 product"
      }]) {
        id
      }
    }`});

    fetch($('.aimeos').data('graphql'), {
        method: 'POST',
        credentials: 'same-origin',
        headers: { // Laravel only
            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
        },
        body: body
    }).then(response => {
        return response.json();
    }).then(data => {
        console.log(data);
    });
    ```

Response:

```json
{
  "data": {
    "saveProducts": [
      {
        "id": "19"
      },
      {
        "id": "20"
      }
    ]
  }
}
```

# Delete single product

=== "Mutation"
    ```graphql
    mutation {
      deleteProduct(id: "18")
    }
    ```
=== "Javascript"
    ```javascript
    const body = JSON.stringify({'query':
    `mutation {
      deleteProduct(id: "18")
    }`});

    fetch($('.aimeos').data('graphql'), {
        method: 'POST',
        credentials: 'same-origin',
        headers: { // Laravel only
            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
        },
        body: body
    }).then(response => {
        return response.json();
    }).then(data => {
        console.log(data);
    });
    ```

Response:

```json
{
  "data": {
    "deleteProduct": "18"
  }
}
```

# Delete multiple products

=== "Mutation"
    ```graphql
    mutation {
      deleteProducts(id: ["19", "20"])
    }
    ```
=== "Javascript"
    ```javascript
    const body = JSON.stringify({'query':
    `mutation {
      deleteProducts(id: ["19", "20"])
    }`});

    fetch($('.aimeos').data('graphql'), {
        method: 'POST',
        credentials: 'same-origin',
        headers: { // Laravel only
            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
        },
        body: body
    }).then(response => {
        return response.json();
    }).then(data => {
        console.log(data);
    });
    ```

Response:

```json
{
  "data": {
    "deleteProducts": [
      "19",
      "20"
    ]
  }
}
```
