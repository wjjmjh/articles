# Hands-On Guide to Secure React Routes with Authentication Context

Welcome to this practical and hands-on tutorial where we will explore an elegant approach to implementing and protecting
your React routes using a secure authentication context. Proper resource access control is crucial for any application,
and today, we will show you how to achieve that with React.

In this tutorial, we will create a React application with some protected routes accessible only by authenticated users.
The protected routes will be guarded by an authentication context, ensuring that unauthorized users cannot access them.

## Getting Started

Before we dive into the code, let's set up the initial React repository. Don't worry my friend; I've got you covered.
I've already initialized a repository that you can use as a starting point for us to build on, and feel free to use it
as your future React projects starting point. Simply follow these steps:

```
git clone -b init https://github.com/wjjmjh/react-auth-routes.git

cd react-auth-routes/

npm install
```

## Creating basic components

To build our authentication system, we need some basic components for login, logout, protected routes, and unprotected
routes. Let's start by creating these components.

- login component:

```
import React, { useState } from "react";

const Login = () => {
  const [credentials, setCredentials] = useState({
    username: "",
    password: "",
  });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setCredentials((prevCredentials) => ({
      ...prevCredentials,
      [name]: value,
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    // Add your login logic here
    console.log("Login with:", credentials);
  };

  return (
    <div>
      <h1>Login Page</h1>
      <form onSubmit={handleSubmit}>
        <div>
          <label htmlFor="username">Username:</label>
          <input
            type="text"
            id="username"
            name="username"
            value={credentials.username}
            onChange={handleChange}
            required
          />
        </div>
        <div>
          <label htmlFor="password">Password:</label>
          <input
            type="password"
            id="password"
            name="password"
            value={credentials.password}
            onChange={handleChange}
            required
          />
        </div>
        <button type="submit">Login</button>
      </form>
    </div>
  );
};

export default Login;
```

- logout component:

```
import React from "react";

const Logout = () => {
  const handleLogout = () => {
    console.log("logout!");
    // Add your logout logic here
  };

  return <button onClick={handleLogout}>Logout</button>;
};

export default Logout;
```

- protected component:

```
import React from "react";

const Protected = () => (
  <p>
    Do you know why tacos never share their secrets? Because they don't want
    anyone to spill the beans or lettuce out!
  </p>
);

export default Protected;
```

- unprotected component

```
import React from "react";

const Unprotected = () => (
  <p>
    Do you know why tacos share their secrets? Because they believe in a
    'taco-tell-all' policy, spilling the beans and lettuce out without any
    cheesy reservations!
  </p>
);

export default Unprotected;
```

I hope you like tacos 🌮🌮🌮, my friend

- Integrating Components in App

Now that we have our basic components, let's integrate them into the main App component and set up the routes.

```
import React from "react";
import Login from "@/components/Login";
import Logout from "@/components/Logout";
import Protected from "@/components/Protected";
import Unprotected from "@/components/Unprotected";
import { Route, BrowserRouter as Router, Routes } from "react-router-dom";

const App = () => {
  return (
    <Router>
      <Routes>
        <Route path={"/login"} element={<Login />} />
        <Route path={"/logout"} element={<Logout />} />
        <Route path={"/protected"} element={<Protected />} />
        <Route path={"/unprotected"} element={<Unprotected />} />
      </Routes>
    </Router>
  );
};

export default App;
```

At this stage, if you want to get the repo for current progress , you can do:

```
git clone -b basic-components https://github.com/wjjmjh/react-auth-routes.git
```

To play around:

```
npm start
```

## The Main Course - Implementing the Authentication Context

Next, let's create an authentication context to manage user authentication.

First, create a new file called `api.ts` under the `src` folder. This is where you will handle backend API calls.

Inside `api.ts`, define the `WhoAmIRequest` function. It is essential to ensure that this request is made with valid
user
credentials in your application. Your backend will use these credentials to determine the user making the request. If
the request fails, it indicates that the client is not authenticated or unable to attach valid credentials, such as a
JWT cookie.

```
export const WhoAmIRequest = async () => {
  // please add your own logic here to get who am i user info
  throw new Error("have no idea who am i");
};
```

In the WhoAmIRequest function, you should replace the placeholder logic with actual backend calls to retrieve user
information. For example, you might make an API request to your backend, passing along the user's credentials, and then
validate the response to determine if the user is authenticated.

we specifically want throw an error here to pretend we are not authenticated.

we specifically throw an error here to pretend we are not authenticated.

Time to make the auth context!

```
import { WhoAmIRequest } from "@/api";
import React, { useContext, useEffect, useState } from "react";

export enum AuthStatus {
  Loading,
  SignedIn,
  SignedOut,
}

export interface IAuth {
  authStatus?: AuthStatus;
  signIn?: any;
  signOut?: any;
}

const defaultState: IAuth = {
  authStatus: AuthStatus.Loading,
};

type Props = {
  children?: React.ReactNode;
};

export const AuthContext = React.createContext(defaultState);

export const AuthIsSignedIn = ({ children }: Props) => {
  const { authStatus }: IAuth = useContext(AuthContext);

  return <>{authStatus === AuthStatus.SignedIn ? children : null}</>;
};

export const AuthIsNotSignedIn = ({ children }: Props) => {
  const { authStatus }: IAuth = useContext(AuthContext);

  return <>{authStatus === AuthStatus.SignedOut ? children : null}</>;
};

const AuthProvider = ({ children }: Props) => {
  const [authStatus, setAuthStatus] = useState(AuthStatus.Loading);

  useEffect(() => {
    async function getWhoAmI() {
      try {
        const res = await WhoAmIRequest();
        setAuthStatus(AuthStatus.SignedIn);
      } catch (e) {
        setAuthStatus(AuthStatus.SignedOut);
      }
    }
    getWhoAmI().then();
  }, [setAuthStatus, authStatus]);

  function signIn() {
    setAuthStatus(AuthStatus.SignedIn);
  }

  function signOut() {
    setAuthStatus(AuthStatus.SignedOut);
  }

  const state: IAuth = {
    authStatus,
    signIn,
    signOut,
  };

  if (authStatus === AuthStatus.Loading) {
    return null;
  }

  return <AuthContext.Provider value={state}>{children}</AuthContext.Provider>;
};

export default AuthProvider;
```

In summary, the authentication context we've created uses the WhoAmIRequest function and relies on the response status
to determine the rendering of different child components.

Here's how it works:

The WhoAmIRequest function is called when the application initializes or when there's a need to check the user's
authentication status.

If the request is successful and receives a valid response, it means the user is authenticated. In this case, the
context sets the `SignedIn` status as true in the React runtime memory. As a result, the application renders the
protected
routes, allowing the authenticated user to access them.

On the other hand, if the request fails or the response indicates that the user is not authenticated, the context sets
the `SignedOut` status as true in the React runtime memory. Consequently, the application displays the unprotected
routes,
restricting access to authenticated users only.

By managing the authentication status in the React runtime memory through the authentication context, the application
can dynamically render different components based on the user's authentication state. This approach ensures that
unauthorized users are redirected to appropriate routes, maintaining proper access control and enhancing the security of
your React application.

Now let us separate our current routes into 2 groups: `AuthIsSignedIn` and `AuthIsNotSignedIn`, depends on if `SignedIn`
or `SignedOut`

```
import Login from "@/components/Login";
import Logout from "@/components/Logout";
import Protected from "@/components/Protected";
import Unprotected from "@/components/Unprotected";
import AuthProvider, {
  AuthIsNotSignedIn,
  AuthIsSignedIn,
} from "@/contexts/AuthContext";
import {
  Navigate,
  Route,
  BrowserRouter as Router,
  Routes,
} from "react-router-dom";

export default function App() {
  return (
    <AuthProvider>
      <AuthIsSignedIn>
        <Router>
          <Routes>
            <Route path={"/logout"} element={<Logout />} />
            <Route path={"/protected"} element={<Protected />} />
          </Routes>
        </Router>
      </AuthIsSignedIn>
      <AuthIsNotSignedIn>
        <Router>
          <Routes>
            <Route path={"/login"} element={<Login />} />
            <Route path={"/unprotected"} element={<Unprotected />} />
            <Route path="/*" element={<Navigate replace to={"/login"} />} />
          </Routes>
        </Router>
      </AuthIsNotSignedIn>
    </AuthProvider>
  );
}
```

Given our current implementation of the "problematic" WhoAmIRequest, where it always throws an error, you will quickly
notice that you can only access the unprotected routes, and not the protected ones. However, if you "hack" the
`WhoAmIRequest` by not throwing an error, you can effectively gain access to the protected routes.

⚠️ Please note that this is purely for demonstration purposes and should not be used in a production environment. The
purpose of this demonstration is to highlight the importance of properly implementing the WhoAmIRequest function with
actual backend authentication logic to ensure proper security.

Here's how you can "hack" the WhoAmIRequest by modifying it not to raise an error:

```
export const WhoAmIRequest = async () => {
  // Hack: Return a dummy user object to simulate successful authentication
  // This is for demonstration purposes only and should not be used in production
  return { username: "dummyuser" };
};
```

By returning a dummy user object instead of throwing an error, the application will treat this as a successful
authentication, and you will be able to access the protected routes.

⚠️ Again, please remember that this "hack" is not secure and should only be used for testing or learning purposes. In a
real-world application, you must implement proper backend authentication logic to validate user credentials and ensure
secure access to protected routes.

At this stage, if you want to see current progress repo,

```
git clone -b auth-context https://github.com/wjjmjh/react-auth-routes.git
```

To play around:

```
npm start
```

## Completing the Protected Routes

Now, let's proceed with completing the implementation of the protected routes. We'll integrate the login component with
the authentication context and modify the logic when the login form is submitted.

1, Integrate the Login Component with Auth Context:

```
const Login: React.FC = () => {

  const authContext = useContext(AuthContext);

  ...
}
```

then we modify the logic when login is submitted:

```
 const handleSubmit = (e: React.FormEvent<HTMLFormElement>): void => {
    e.preventDefault();
    localStorage.setItem("username", credentials.username);
    authContext.signIn();
    window.location.href = "/protected";
  };
```

⚠️ I have mocked the login logic via

```
localStorage.setItem("username", credentials.username);
```

For demonstration, we store the username in localStorage and mock the signIn function, In a real-world scenario, handle
login logic with backend authentication, and returning and setting JWT cookie etc...

2, Revise the Logout Component:

```
import { AuthContext } from "@/contexts/AuthContext";
import React, { useContext } from "react";

const Logout: React.FC = () => {
  const authContext = useContext(AuthContext);

  return (
    <button
      onClick={async () => {
        localStorage.setItem("username", "");
        authContext.signOut();
        window.location.href = "/login";
      }}
    >
      Logout
    </button>
  );
};

export default Logout;
```

it clears the stored username and mock the signOut function

3, let us mock a backend call to get who-am-i:

```
export const WhoAmIRequest = async () => {
  return localStorage.getItem("username");
};
```

4, Update Auth Context to Check Valid Username:

```
const AuthProvider = ({ children }: Props) => {
  const [authStatus, setAuthStatus] = useState(AuthStatus.Loading);

  useEffect(() => {
    async function getWhoAmI() {
      const res = await WhoAmIRequest();
      if (res !== "" && res) {
        setAuthStatus(AuthStatus.SignedIn);
      } else {
        setAuthStatus(AuthStatus.SignedOut);
      }
    }
    getWhoAmI().then();
  }, [setAuthStatus, authStatus]);

  ...

  return <AuthContext.Provider value={state}>{children}</AuthContext.Provider>;
};
```

5, let us make one more component called WhoAmI to display the user info, it should be a protected component, of course:

```
import { WhoAmIRequest } from "@/api";
import React, { useEffect, useState } from "react";

export default function WhoAmI() {
  const [whoAmI, setWhoAmI] = useState("");

  useEffect(() => {
    const fetchData = async () => {
      const whoAmI = await WhoAmIRequest();
      if (whoAmI !== "" && whoAmI) {
        setWhoAmI(whoAmI);
      } else {
        window.location.href = "/login";
      }
    };

    fetchData().then(() => {});
  }, []);

  return <div>{JSON.stringify(whoAmI)}</div>;
}
```

6, final step: update the App component to register new routes:

```

...

import WhoAmI from "@/components/WhoAmI";

export default function App() {
  return (
    <AuthProvider>
      <AuthIsSignedIn>
        <Router>
          <Routes>
            <Route path={"/"} element={<Protected />} />
            <Route path={"/login"} element={<Login />} />
            <Route path={"/logout"} element={<Logout />} />
            <Route path={"/protected"} element={<Protected />} />
            <Route path={"/whoami"} element={<WhoAmI />} />
          </Routes>
        </Router>
      </AuthIsSignedIn>
      <AuthIsNotSignedIn>
        <Router>
          <Routes>
            <Route path={"/"} element={<Login />} />
            <Route path={"/login"} element={<Login />} />
            <Route path={"/unprotected"} element={<Unprotected />} />
            <Route path="/*" element={<Navigate replace to={"/login"} />} />
          </Routes>
        </Router>
      </AuthIsNotSignedIn>
    </AuthProvider>
  );
}

```

Feel free to play around my friend!

```
npm start
```

You can find the complete code on GitHub: https://github.com/wjjmjh/react-auth-routes/tree/master. Happy protecting
React routes! 🚀
