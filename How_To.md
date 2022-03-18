# Project: User Manager

Create a User manager where an user can Sign in or Sign up using email and password. No additional data required.


## Knowledge 1: Package Management in Node

-   Install the module

    ```
    npm i is-odd
    ```

-   Import it

    ```js
    const isOdd = require("is-odd");
    ```

-   Use

    ```js
    console.log(isOdd("1"));
    console.log(isOdd(1));
    console.log(isOdd(4));
    ```

-   When you install a package Yow can see all of it in your node modules and package.json

## Knowledge 2: Reading and Writing files Synchronously in Node

-   Install `fs` module. `fs` stands for File System.

    ```
    npm i fs
    ```

-   To Read a file simply:

    ```js
    const fs = require("fs");
    const data = fs.readFileSync("file.txt");
    console.log(data);
    ```

-   Write a file:

    ```js
    const fs = require("fs");
    const data = "I love Dogs";
    fs.writeFileSync("file.txt", data);
    ```

## Task 1: Lets think about what we are building

### Lets find what all we want to add to this project

    -   A system to Sign In an user
    -   A system to sign Up a new user
    -   Noting more (I hope thats all written in the question)

### Lets break it down:

    -   Sign Up - Accept a email and password and add it to the database
    -   Sign In - Accept email and password and check if user is present in database, if present we would like to return his user Id

    This looks damn simple

### Terms used

    -   Btw we just talked about in sign in that we need to return the userId why so?
        It is mainly because everything in the database related to the user is identified by his userId
        Say in real life your name is your userId. When your asks someone to find Ram's copy from the pile of copies
        they check for ram's name, when it matches, they bring it out.

        - But name isn't a very good id for a database. Guess Why?
        - Though Email is unique but it isn't a vey good id as well. Guess Why? this is a difficult one!

    -   But man we are also talking soo much about a database. What the freak is that?

        Nothing fancy, it is just a place where you can store all your data securely, without thinking much about losing it.
        It might be a simple file, an SQL database, or the noSQl databases.

### Components we need

-   `signUp` function

    -   A function that accepts `email` and `password`
    -   Checks if an user exists with the same email - if user exists with the same email Sign Up unsuccessful - if user doesn't exist with same email, we sign him up with entered `email` and `password`
    -   return `userId`

-   following up from the `signUp` function we need - `getUser` function to check if user is already present

    -   A function that accepts `email` and `password`
    -   Checks if an user exists with the same email - if user exists with the same email check if password match
    -   return `emailExists` as `true/false`, `passMatch` as `true/false`, `userId` if `passMatch` is `true`

-   We also need an `addUser` function to add users to the database

    -   A function that accepts `email` and `password`
    -   Adds `email` and `password` to the database
    -   Returns `userId`

-   After all these we need a `signIn` Function

    -   A function that accepts `email` and `password`
    -   Adds `email` and `password` to the database
    -   Returns `userId`

-   Finally we need a main function to manage our belongings, and host a menu for sign in and sign up

### How to start coding

-   Lets start with the function that has the minimum possible Dependency.

    -   `signUp` function is dependant on `getUser` and `addUser` functions. (So not a good one to start with)
    -   `getUser` function looks good to start but not sure if we can write it properly without any data in the database
    -   `addUser` function looks perfect, we just simply add the user without thinking much about anything else (We can start with this)
    -   `signIn` function is dependant on getUser but would love to see someone sign in after sign up

-   We are talking a lot about database, shall we learn a SQL or noSQL for this. Nah! as mentioned before
    we can use a simple file as our database, so lets use a JSON file to store our data. lets name it `users.json`

## Task 2: Lets Start Coding

### Step 1: Initialize an empty package

-   Open your terminal or cm in the current empty directory
-   Initialize an empty package

    ```
    npm init
    ```

-   **Bonus** - Press as many enters as required XP

### Step 2: Create a new JS file

-   Create a new js file `manager.js`
-   Create the database `users.json` and put the following in it:

    ```json
    {
    	"users": {}
    }
    ```

-   Sit back and relax - your did a great job

### Step 3: Lets start with the functions

-   `addUser` function

```js
const fs = require("fs");
const crypto = require("crypto");

/*
 *	addUser = (email: <string>, password: <string>) => { userId: <string>}
 *  Accept email and password of the user to be added
 *	Read get the users from the database
 *	Encrypt user email to get userId
 *	add user to the users database
 * 	return userId
 */
const addUser = (email, password) => {
	const jsonString = fs.readFileSync("users.json"); // Read the users file
	const data = JSON.parse(jsonString); // Parse the text to JSON in the data variable

	// encrypt the user email to create the userId
	const userId = crypto
		.createHash("sha1")
		.update(email, "utf8")
		.digest("hex");

	// add the user to data
	data.users[userId] = {
		email,
		password: password,
	};

	// Write the data to the file
	fs.writeFileSync("users.json", JSON.stringify(data, null, 4));
	return { userId }; // return userId
};

addUser("Test@myemail.com", "TestPassword");
```

-   `getUser` function

```js
/*
 *	getUser = (email: <string>, password: <string>) => 
 	{ 	emailExists: <boolean>,
		passMatch: <boolean>,
    	userId: null || <string> ,
	}
 *
 *  Accept email and password of the user to be added
 *	Read get the users from the database
 *	Encrypt user email to get userId
 *	add user to the users database
 * 	return userId
 */
const getUser = (email, password) => {
	const jsonString = fs.readFileSync("users.json"); // Read the users file
	const data = JSON.parse(jsonString); // Parse the text to JSON in the data variable

	// Set response Model with default values
	const response = {
		emailExists: false,
		passMatch: false,
		userId: null,
	};

	// Loop through all users
	for (const user in data.users) {
		// if email and password both matched that means it is the same user and
		// we can share userId and say that emailExists and passMatch
		if (
			data.users[user].email === email &&
			data.users[user].password === password
		) {
			response.emailExists = true;
			response.passMatch = true;
			response.userId = user;
			break;
		}
		// if email matched but password mismatch that means user entered wrong password
		// we shouldn't share userId and say that emailExists is true but passMatch is false
		else if (
			data.users[user].email === email &&
			data.users[user].password !== password
		) {
			response.emailExists = true;
			break;
		}
	}
	// return response at the end of the function
	return response;
};

getUser("Test@myemail.com", "Test");
getUser("A@myemail.com", "TestPassword");
getUser("Test@myemail.com", "TestPassword");
```

-   `signUp` function

```js
// Add to the top of the page where imports are done
// This module helps to accept input from user
const prompt = require("prompt-sync");
const input = prompt();
```

```js
/*
 *	signUp = () => 
 	{ 	userId: null || <string> ,
	}
 *
 *  Accept email and password input of the user to be added
 *	getUsers from the database with email
 * 	If user already exists ask to sign in
 *	Else add the user and return userId
 */
const signUp = () => {
	// Accept email and password input
	const email = input("Enter Your Email: ");
	const password = input("Enter Your Password: ");

	// getUsers from the database with Email
	const user = getUser(email, "");
	// if user already exists ask to sign in and return userId as null
	if (user.emailExists) {
		console.log("Email Already exists, please sign in");
		return { userId: null };
	}
	// else add the user and return userId
	else {
		const userId = addUser(email, password);
		console.log("SignUp Successful");
		return { userId };
	}
};

signUp();
```

-   `signIn` function

```js
/*
 *	signIn = () => 
 	{ 	userId: null || <string> ,
	}
 *
 *  Accept email and password input of the user to be signed In
 *	getUser with the inputted values and 
 * 	return userId if user is found else return userId as null
 */
const signIn = () => {
	// Accept email and password input
	const email = input("Enter Your Email: ");
	const password = input("Enter Your Password: ");

	// Try to get user with inputted email and password
	const user = getUser(email, password);

	// Log proper message
	if (user.passMatch) console.log("Successfully Signed In");
	else if (user.emailExists) console.log("Password Mismatch");
	else console.log("Email don't exist, please sign up");

	// Return userId if user present
	return { userId: user.userId };
};

signIn();
```

-   Finally `main` function

```js
/*
 * main = () => null
 *
 * menu driven user interaction
 */
const main = () => {
	let ch = input("Enter 'in' for Sign In and 'up' for Sign Up: ");
	ch = ch.toLowerCase();
	let userId;
	switch (ch) {
		case "in":
			userId = signIn().userId;
			break;
		case "up":
			userId = signUp().userId;
			break;
		default:
			console.log("Please SignUp/SignIn");
			main();
	}
};

main();
```

### Step 4: Submit

### Additional Tasks

-   Encrypt Password so that no one can see it
-   If wrong password while sign in ask to repeat up to 3 times
-   Re-enter password and verification feature in Sign Up
