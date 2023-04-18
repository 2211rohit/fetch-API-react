When you develop an application, you will often need to fetch data from a back-end or a third-party API. In this article, we will learn different ways to fetch and display data from API in React.

API stands for “Application Programming Interface”, which is a method of communication between different applications. For example, a news application contains regular news data, and the same data comes to the application with the help of APIs. There are various types of APIs, like the REST API, the GraphQL API, the SOAP API, etc.

How an API works?
The workings of an API is very easy to understand. Let’s understand it with an example. Say we want to build a new application and we don’t have our own back-end. Now, to display the news in our application, we need some third party APIs to access their back-end server and display the data in our app.

Now, there are 3 things we must have noted, i.e., an application, a server, and an API. Most times, the API is in between the app and server because whenever the client requests the data, the API makes a GET request to the server and sends that back to the application for display.

Methods of fetching data using API
We commonly use a Web API called REST, or Representational State Transfer API which consists of HTTP methods to fetch data from the server and display it in the application. A REST API has several methods, which are discussed further below:

GET: This method is used to fetch the data from a server endpoint.
POST: This method is used to post the data to a server endpoint.
DELETE: This method is used to delete the data from a server endpoint.
PUT: This method is used to update or modify the data from a server endpoint.
Now as you have understood all the methods of the API, we can now move on to how the data is fetched from the server. To fetch the data, we use the GET method.

The different ways of Fetching the data in a React application are given below:

Using JavaScript Fetch API
Using Axios library
Using async — await
Using React Hooks
Using React query
In all example, we are going to use mock data provided freely by https://jsonplaceholder.typicode.com/ in JSON format. We are going to use the user’s endpoint from that API i.e., https://jsonplaceholder.typicode.com/users.

👉 Fetching data using inbuilt fetch API.
All modern browsers come with an inbuilt fetch Web API, which can be used to fetch data from APIs. In this tutorial, we will be fetching data from the JSON Server APIs.

import React, { useEffect, useState } from "react"

function App() { 

  const url = "https://jsonplaceholder.typicode.com/users"
  const [users, setUsers] = useState([])

  const fetchData = () => {
    fetch(url)
      .then(response => {
        return response.json()
      })
      .then(data => {
        setUsers(data)
      })
  }

  useEffect(() => {
    fetchData()
  }, [])

  return (
    <div>
      {users.length > 0 && (
        <ul>
          {users.map(user => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      )}
    </div>
  )
}

export default App;
In the above code,

We have are using a useEffect hook, which will be executed once the component is mounted (alternative of componentDidMount in class-based components). Inside the useEffect hook, we are calling fetchData function.
In the fetchData function, we are making the API call to fetch users and set the users to a local state.
If users exist, then we are looping through them and displaying their names as a list.
Since the API calls are asynchronous, fetch API returns a Promise. Hence, we chain the then method with a callback, which will be called when we receive the response from the server/back-end.

Since we need the response to be resolved to a JSON, we call .json() method with the returned response. Again .json() return a promise, therefore we need to chain another then method to resolve the second promise.

Since the then callbacks have only one line, we can use implicit returns to shorten the fetchData method as follows:

const fetchData = () =>
  fetch(url)
    .then(response => response.json())
    .then(data => setUsers(data))
Error Handling:
While relying on external data, we should always have error handling in place. An API might fail because of any issues in the server or due to incorrect information passed from the client-side.

We can fix this by checking if the response has a HTTP 2XX response code or not and if the server responds with anything other than 2XX, then we will throw an error and handle it in the catch method callback:

import React, { useEffect, useState } from "react"

function App() {

  const url = "https://jsonplaceholder.typicode.com/404"
  const [users, setUsers] = useState([])
  const [error, setError] = useState("")

  const fetchData = () => {
    setError("")
    fetch(url)
      .then(response => {
        // If the HTTP response is 2xx then it response.ok will have a value of true
        if (response.ok) {
          return response.json()
        } else {
          // If the API responds meaningful error message,
          // then you can get it by calling response.statusText
          throw new Error("Sorry something went wrong")
        }
      })
      .then(data => {
        setUsers(data)
      })
      .catch(error => {
        // It is always recommended to define the error messages
        // in the client side rather than simply relying on the server messages,
        // since server messages might not make sense to end user most of the time.
        setError(error.message)
      })
  }

  useEffect(() => {
    fetchData()
  }, [])

  return (
    <div>
      {error && <p>{error}</p>}
      {users.length > 0 && (
        <ul>
          {users.map(user => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      )}
    </div>
  )
}

export default App;
Also, note that if any error other than 4xx or 5xx error, such as network error happens, then it will directly go to catch callback without going to the first then callback.

👉 Fetching data in React using Axios
Axios is a JavaScript library that we use to make HTTP requests same as fetch(). There is a difference between these two as in fetch() we have to convert the result to a JSON object but in Axios it already returns the result as a JSON object, so we don’t need to convert it.

Let’s take the example of Axios library. First, we have to install the Axios library then we have to import axios from axios.

npm install axios
import axios from "axios"
import React, { useEffect, useState } from "react"

function App() {

  const url = "https://jsonplaceholder.typicode.com/users"
  const [users, setUsers] = useState([])

  const fetchData = () => {
    axios.get(url)
      .then(response => {
      setUsers(response.data)
    })
  }

  useEffect(() => {
    fetchData()
  }, [])

  return (
    <div>
      {users.length > 0 && (
        <ul>
          {users.map(user => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      )}
    </div>
  )
}

export default App;
Note that we do not need 2 then blocks here since axios will handle converting response to JSON for us. The response data can be accessed via response.data. Also, we do not have to check for response.ok as in the case of fetch since all the errors will come to the catch method callback:

...
...
const fetchData = () => {
  axios
    .get("https://jsonplaceholder.typicode.com/users")
    .then(response => {
      setUsers(response.data)
    })
    .catch(error => {
      console.log({ error })
      // Handle error
    })
}
...
...
👉 Fetching data in React using async - await
We use Async - Await as it is an asynchronous technique that is operated via an event loop. Async functions will always return a value. It is the preferred way of fetching the data from an API as it enables us to remove our .then() callbacks and return asynchronously resolved data. In the async block, we can use Await function to wait for the promise.

In case you like to use async - await syntax instead of then callbacks, you can write the same example as follows:

import React, { useEffect, useState } from "react"

function App() {

  const url = "https://jsonplaceholder.typicode.com/users"
  const [users, setUsers] = useState([])

  const fetchData = async () => {
    const response = await fetch(url)
    const data = await response.json()
    setUsers(data)
  }

  useEffect(() => {
    fetchData()
  }, [])

  return (
    <div>
      {users.length > 0 && (
        <ul>
          {users.map(user => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      )}
    </div>
  )
}

export default App;
Make sure that you do not use async - await inside the useEffect hook. If you convert the useEffect hook itself to an async function, then React will show the following warning:

Effect callbacks are synchronous to prevent race conditions. Put the async function inside
Effect callbacks are synchronous to prevent race conditions. Put the async function inside
or we can write async - await in axios also

...
...
async function fetchData() {
    try {
      const response = await axios.get("https://jsonplaceholder.typicode.com/users")
      setUser(response.data)
    } catch (error) {
      console.error(error);
    }
  }

  useEffect(() => {
    fetchData();
  },[])
...
...
Error handling in async - await
To handle errors while using async - await syntax, we can go for the traditional try-catch blocks:

import React, { useEffect, useState } from "react"

function App() {

  const url = "https://jsonplaceholder.typicode.com/404"
  const [users, setUsers] = useState([])
  const [error, setError] = useState("")

  const fetchData = async () => {
    setError("")
    try {
      const response = await fetch(url)
      if (!response.ok) {
        // If the API responds meaningful error message,
        // then you can get it by calling response.statusText
        throw new Error("Sorry something went wrong")
      }
      const data = await response.json()
      setUsers(data)
    } catch (error) {
      // It is always recommended to define the error messages
      // in the client side rather than simply relying on the server messages,
      // since server messages might not make sense to end user most of the time.
      setError(error.message)
    }
  }

  useEffect(() => {
    fetchData()
  }, [])

  return (
    <div>
      {error && <p>{error}</p>}
      {users.length > 0 && (
        <ul>
          {users.map(user => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      )}
    </div>
  )
}

export default ErrorAsyncAwait
👉 Fetching Data in React when a button is clicked
If you want to fetch data conditionally, say when a button is clicked, you can do that as shown below:

import React, { useState } from "react"

function App() {

  const url = "https://jsonplaceholder.typicode.com/users"
  const [users, setUsers] = useState([])

  const fetchData = () => {
    fetch(url)
      .then(response => {
        return response.json()
      })
      .then(data => {
        setUsers(data)
      })
  }

  return (
    <div>
      <button onClick={fetchData}>Fetch Users</button>
      {users.length > 0 && (
        <ul>
          {users.map(user => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      )}
    </div>
  )
}

export default App;
