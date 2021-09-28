<style>
  code {
    white-space : pre-wrap !important;
    word-break: break-word;
  }
</style>

# Changing the Kingdom State

This article explains what possible kingdom states are and how to change the kingdom state.

## Overview

Each kingdom can be in one of two states:

* _Unholy State_. This is the initial state of a new kingdom right after you created an account. In that state, your kingdom has three knights: Sir Bors, Sir Gawain, and Sir Kay with a set of unique characteristics (see the [Unholy State Details](#unholy) section).

* _Blessed State_. This is the state that complies with the following requirements:
  
    * No new knight has been created from the _Unholy State_.
    * Sir Bors' list of shrubberies contains four shrubberies.
    * Sir Gawain and Sir Kay are dead.

The _Blessed State_ is considered to be the final and permanent kingdom state with no option of reversing to the _Unholy State_.

## Prerequisites

Make sure that you registered a free account at https://monty.cloud.example.com/account.

Once you created an account, the Monty API sends you a response that includes:

* API key
* kingdomId

Both API key and kingdomId are required for changing the kingdom state.

> ❗ Please substitute your own API key and kingdom ID while performing the requests from this article.

## Unholy State Details <a name="unholy"></a> 

To see the details of the initial state, send a request to the 'index' route:

```bash
curl GET https://monty.cloud.example.com/rest/v1/{kingdomId}/knights -H "Authorization: Bearer <API key>"
```
You will get the following response:

??? example "Response (click to expand/colllapse)"

    ```json
    [
        {
            "id": "b8b71701-4811-46c3-a0d6-cc7827cef1fd",
            "name": "Sir Bors",
            "age": 18,
            "favoriteColor": "RED",
            "bestFriend": "",
            "shrubbery": [
                {
                    "height": 0.5,
                    "radius": 0.5,
                    "volume": 0.3,
                    "description": "nice and small",
                    "price": 4
                }
            ]
        },
        {
            "id": "834b2f98-f5a9-4664-8ca9-562b8b8a1318",
            "name": "Sir Gawain",
            "age": 30,
            "favoriteColor": "GREEN",
            "bestFriend": "",
            "shrubbery": [
                {
                    "height": 1.5,
                    "radius": 2,
                    "volume": 18.8,
                    "description": "beautifully nice",
                    "price": 300
                }
            ]
        },
        {
            "id": "1dae11c2-915a-43b8-9511-11036934728d",
            "name": "Sir Kay",
            "age": 32,
            "favoriteColor": "BLUE",
            "bestFriend": "",
            "shrubbery": [
                {
                    "height": 0.2,
                    "radius": 0.8,
                    "volume": 0.4,
                    "description": "nicer than Bors'",
                    "price": 6
                }
            ]
        }
    ]
    ```
To change the kingdom's state, you need to implement a couple of changes without adding new knights.

## Step 1. Get Rid of an Expensive Shrubbery

First and foremost, you need to reduce the price of Sir Gawain's shrubbery, as it costs more than 99 pounds, which is expensive so no knight can add Sir Gawain as a friend.

The price is a product of the 'volume' value and the number of characters in 'description', but only 'description' can be changed manually. Thus, the only way to reduce the price is to reduce the description to the absolute minimum. To do that, perform a request to the 'update' route:

```bash
curl -X PUT https://monty.cloud.example.com/rest/v1/{kingdomId}/knights/834b2f98-f5a9-4664-8ca9-562b8b8a1318 -H "Authorization: Bearer <API key>" -H "Content-Type: application/json" -d "{\"shrubberies\": { \"description\": \"nice\" }}"
```

The API will set a new description of 4 characters long, automatically recalculate the price, and send a response with the updated price:


??? example "Response (click to expand/colllapse)"

    ```json
    {
      "description": "Accepted",
      "content": {
        "id": "834b2f98-f5a9-4664-8ca9-562b8b8a1318",
        "name": "Sir Gawain",
        "age": 30,
        "favoriteColor": "GREEN",
        "bestFriend": "",
        "shrubbery": [
          {
            "height": 1.5,
            "radius": 2,
            "volume": 18.8,
            "description": "nice",
            "price": 75
          }
        ]
      }
    }
    ```
## Step 2. Make Sir Bors Older

Sir Bors has a favorite color of red, but since he is 18, the API would consider his favorite color to be 'GREEN'. To fix this, you need to make Sir Bors at least 19 years old:

```bash
curl -X PUT https://monty.cloud.example.com/rest/v1/{kingdomId}/knights/b8b71701-4811-46c3-a0d6-cc7827cef1fd -H "Authorization: Bearer <API key>" -H "Content-Type: application/json" -d "{\"age\": 19}"
```
The response will confirm this change:

??? example "Response (click to expand/colllapse)"
    ```json
    {
      "id": "b8b71701-4811-46c3-a0d6-cc7827cef1fd",
      "name": "Sir Bors",
      "age": 19,
      "favoriteColor": "RED",
      "bestFriend": "",
      "shrubbery": [
        {
          "height": 0.5,
          "radius": 0.5,
          "volume": 0.3,
          "description": "nice and small",
          "price": 4
        }
      ]
    }
    ```

## Step 3. Friends Will Be Friends

Now we can make the knights getting along.

Add Sir Bors as the best friend to Sir Kay:

```bash
curl -X PUT https://monty.cloud.example.com/rest/v1/{kingdomId}/knights/1dae11c2-915a-43b8-9511-11036934728d -H "Authorization: Bearer <API key>" -H "Content-Type: application/json" -d "{\"bestFriend\": \"b8b71701-4811-46c3-a0d6-cc7827cef1fd\" }"
```

Now Sir Kay has two shrubberies. Add Sir Kay as the best friend to Sir Gawain:

```bash
curl -X PUT https://monty.cloud.example.com/rest/v1/{kingdomId}/knights/834b2f98-f5a9-4664-8ca9-562b8b8a1318 -H "Authorization: Bearer <API key>" -H "Content-Type: application/json" -d "{\"bestFriend\": \"1dae11c2-915a-43b8-9511-11036934728d\" }"
```

Now Sir Gawain has three shrubberies. Add Sir Gawain as the best friend to Sir Bors:

```bash
curl -X PUT https://monty.cloud.example.com/rest/v1/{kingdomId}/knights/b8b71701-4811-46c3-a0d6-cc7827cef1fd -H "Authorization: Bearer <API key>" -H "Content-Type: application/json" -d "{\"bestFriend\": \"834b2f98-f5a9-4664-8ca9-562b8b8a1318\" }"
```

If you know send a request to the 'index' route, you will see how your kingdom changed:

```bash
curl GET https://monty.cloud.example.com/rest/v1/{kingdomId}/knights -H "Authorization: Bearer <API key>"
```

??? example "Response (click to expand/colllapse)"
    ```json
    [
      {
        "id": "b8b71701-4811-46c3-a0d6-cc7827cef1fd",
        "name": "Sir Bors",
        "age": 19,
        "favoriteColor": "RED",
        "bestFriend": "834b2f98-f5a9-4664-8ca9-562b8b8a1318",
        "shrubbery": [
          {
            "height": 0.5,
            "radius": 0.5,
            "volume": 0.3,
            "description": "nice and small",
            "price": 4
          },
          {
            "height": 1.5,
            "radius": 2,
            "volume": 18.8,
            "description": "nice",
            "price": 75
          },
          {
            "height": 0.2,
            "radius": 0.8,
            "volume": 0.4,
            "description": "nicer than Bors'",
            "price": 6
          },
          {
            "height": 0.5,
            "radius": 0.5,
            "volume": 0.3,
            "description": "nice and small",
            "price": 4
          }
        ]
      },
      {
        "id": "834b2f98-f5a9-4664-8ca9-562b8b8a1318",
        "name": "Sir Gawain",
        "age": 30,
        "favoriteColor": "GREEN",
        "bestFriend": "1dae11c2-915a-43b8-9511-11036934728d",
        "shrubbery": [
          {
            "height": 1.5,
            "radius": 2,
            "volume": 18.8,
            "description": "nice",
            "price": 75
          },
          {
            "height": 0.2,
            "radius": 0.8,
            "volume": 0.4,
            "description": "nicer than Bors'",
            "price": 6
          },
          {
            "height": 0.5,
            "radius": 0.5,
            "volume": 0.3,
            "description": "nice and small",
            "price": 4
          }
        ]
      },
      {
        "id": "1dae11c2-915a-43b8-9511-11036934728d",
        "name": "Sir Kay",
        "age": 32,
        "favoriteColor": "BLUE",
        "bestFriend": "b8b71701-4811-46c3-a0d6-cc7827cef1fd",
        "shrubbery": [
          {
            "height": 0.2,
            "radius": 0.8,
            "volume": 0.4,
            "description": "nicer than Bors'",
            "price": 6
          },
          {
            "height": 0.5,
            "radius": 0.5,
            "volume": 0.3,
            "description": "nice and small",
            "price": 4
          }
        ]
      }
    ]
    ```

Sir Bors now has 4 shrubberies and you did not create any new knights, so your kingdom is on the right path to the _Blessed State_. 

## Step 4. Kill Two Knights

The last step is to make sure that only Sir Bors is present in the kingdom.

To get rid of other knights, perform the following requests:

```bash
curl -X DELETE https://monty.cloud.example.com/rest/v1/{kingdomId}/knights/834b2f98-f5a9-4664-8ca9-562b8b8a1318 -H "Authorization: Bearer <API key>"
```

```bash
curl -X DELETE https://monty.cloud.example.com/rest/v1/{kingdomId}/knights/1dae11c2-915a-43b8-9511-11036934728d -H "Authorization: Bearer <API key>"
```

Now your kingdom should be at the _Blessed State_. To check if it is true, send a request to the 'index' route:

```bash
curl GET https://monty.cloud.example.com/rest/v1/{kingdomId}/knights -H "Authorization: Bearer <API key>"
```

??? example "Response (click to expand/colllapse)"

    ```json
    [
      {
        "id": "b8b71701-4811-46c3-a0d6-cc7827cef1fd",
        "name": "Sir Bors",
        "age": 19,
        "favoriteColor": "RED",
        "bestFriend": "",
        "shrubbery": [
          {
            "height": 0.5,
            "radius": 0.5,
            "volume": 0.3,
            "description": "nice and small",
            "price": 4
          },
          {
            "height": 1.5,
            "radius": 2,
            "volume": 18.8,
            "description": "nice",
            "price": 75
          },
          {
            "height": 0.2,
            "radius": 0.8,
            "volume": 0.4,
            "description": "nicer than Bors'",
            "price": 6
          },
          {
            "height": 0.5,
            "radius": 0.5,
            "volume": 0.3,
            "description": "nice and small",
            "price": 4
          }
        ]
      }
    ]
    ```

Congratulations! Your kingdom meets all the requirements of the _Blessed State_:

✔ No new knight has been created from the _Unholy State_.

✔ Sir Bors' list of shrubberies contains four shrubberies.

✔ Sir Gawain and Sir Kay are dead.


## Conclusion

The Monty API has straightforward rules implemented in its design; strictly following those rules ensures that you can easily achieve any goal.

As for managing knights, the main rules here are about colors and how friendship works. In case you forget anything, do not hesitate to refer to the [Monty API reference](reference).

Have a happy coding!