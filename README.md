## Table of contents

- [Creating EJS Template](#Creating-Our-First-EJS-Template-and-why-we-need-them)
  - [Running Code](#Running-Code-Inside-the-EJS-Template)
  - [Passing Data](#Passing-Data-from-Your-Webpage-to-Your-Server)
  - [Links](#links)
- [Adding Static Files](#Adding-Static-Files)
### Creating Our First EJS Template and why we need them
In express.js with app.get we can do tons of calculations on our server side and send the response back to the user with res.send(); so it takes a lot less processing power from the client side.
 - Let's write, for example, a fast program that processes which day of the week it is from the user's machine automatic input, which will get proccessed in our server and will send back the result to the user with either "it's a weekend" or "it's a weekday" depending on the day.
```js
app.get("/", function(req, res){
    var today = new Date();

    if(today.getDay() == 6 || today.getDay() == 0){
        res.send("Yay it's the weekend!");
    }else {
        res.send ("Boo! I have to work!");
    }
});
We can also render the sent data as html 
    if(today.getDay() == 6 || today.getDay() == 0){
        res.send("<h1>Yay it's the weekend!</h1>");
    }else {
        res.send ("<h1>Boo! I have to work!</h1>");
    }
```
*Keep in mind that when the browser sees `res.send` it views it as the last piece of instruction before executing and sending everything in the app.get to the client [like break;]. We cannot send multiple res.send, we can use res.write to send mulpiple pieces of information instead.*
```js
 if(currentDay == 6 || currentDay == 0){
        res.send("<h1>Yay it's the weekend!</h1>");
    }else {
        res.write ("<p>It's not the weekend.</p>");
        res.write ("<h1>Boo! I have to work!</h1>");
        res.send();
    }
```
We must put a res.send(); at the end [which is like hitting the send button on a messager app] for all of that information to be sent back to our user from our server.
This can allows us to send multiple pieces of html, which is not recommended as this can get very repetitive.
What if we had multiple lines of html code that needed to be sent? We could send a whole html page with `res.sendFile` but then we'd have to make a different page for each response. So the best solution here is to cut from this repetitivness is **Templating** which allows us to change snippets of our html code depending on the logic of our server. One of the ways to do this is with the external module **EJS** = Embedded Javascript templating, [here is their documentation](https://ejs.co/#install). 
[Documentation on how to use EJS with Express](https://github.com/mde/ejs/wiki/Using-EJS-with-Express).
To add this external module to our current project as always we run the npm command: `npm i ejs`. Then in our code we want to add `app.set('view engine', 'ejs');` which tells the app, which is a variable generated using express, to use ejs as it's view engine.  
In app.get we can use a new method called `res.render('index', {foo: 'FOO'});` which uses the view engine, which we have set to render a particular page, in this case a page called index (which must end with the .ejs extension) which must be in our default folder called 'views', which is located in our working directory, here is where the engine by default goes and looks for the files you try to render. So for eg: 
```js
app.get("/", function(req, res){
    var today = new Date();
    var currentDay = today.getDay();
    var day = "";

    if(currentDay == 6 || currentDay == 0){
        day = "weekend";
    }else {
        day = "weekday";
    }
    res.render("list", {kindOfDay: day});
});
```
In this example we are using `res.render("list", {kindOfDay: day});` which renders a file called list.ejs inside of our folder with the default name of 'views'. We are then passing through a single variable with the name of `kindOfDay`, which is specified and exists in our html (ejs) code `<%= kindOfDay %>`, the value of the variable day that is within our js code.
[`<%=` is used to output the value into the template]  
We can get it to post the specific day of the week with a switch statement: 
```js
app.get("/", function(req, res){
    var today = new Date();
    var currentDay = today.getDay();
    var day = "";

    switch(currentDay){
        case 0:
        day = "Sunday";
        break;
        case 1:
        day = "Monday";
        break;
        case 2:
        day = "Tuesday";
        break;
        case 3:
        day = "Wednesday";
        break;
        case 4:
        day = "Thursday";
        break;
        case 5:
        day = "Friday";
        break;
        case 6:
        day = "Saturday";
        break;
        default:
            console.log("Error: current day is equal to : " + currentDay);
    }
    res.render("list", {kindOfDay: day});
});
```
### Running Code Inside the EJS Template
We can also run JS inside of our template with `<%` which stands for **Scriptlet tag** and it has to be added to any part of the code that is Javascript within the html file [just like with PHP], scriptlet tags only have control flow, meaning that all the logic needs to happen in the server :
```html
<body>
    <% if (kindOfDay ==="Saturday" || kindOfDay === "Sunday"){ %> 
    <h1 style="color: pink">It's a <%= kindOfDay %> ToDo List</h1>
    <% } else { %>
        <h1 style="color: blue">It's a <%= kindOfDay %> ToDo List</h1>
   <% } %>
</body>
```
Depending on what day it is the correct statement will get triggered. If it's a weekend then the h1 element will be rendered pink, if a weekday then it will be blue. (Each part of the js code needs to be covered in scripter tags.)

### Passing Data from Your Webpage to Your Server
 - To start with, let's make our code shorter we will be using [toLocaleDateString();](https://stackoverflow.com/questions/3552461/how-do-i-format-a-date-in-javascript), a javascript function which allows us to take apart a specific piece of a date and format it in any way we please thought the `options` object and it's specific key properties.  
```js
app.get("/", function(req, res){
    var today = new Date();         //<---

    var options ={              //<---
        weekday: "long",
        day: "numeric",
        month: "long"
    };

    var day = today.toLocaleDateString("en-US", options);   //<---

    res.render("list", {kindOfDay: day});
});
```
 - To make it more clear how useful EJS is we will be creating a list app. 
Here is the whole HTML code that we will be starting with:  
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>To Do List</title>
</head>
<body>
    <% if (kindOfDay ==="Saturday" || kindOfDay === "Sunday"){ %>
    <h1 style="color: pink">It's <%= kindOfDay %> ToDo List</h1>
    <% } else { %>
        <h1 style="color: blue">It's <%= kindOfDay %> ToDo List</h1>
   <% } %>

   <ul>
    <li>Buy Cat Food</li>
    <li>Cook the Cat Food</li>
    <li>Serve my Cat the Cat Food</li>
    <!-- added new EJS -->
    <li><%= newListItem %></li>     
   </ul>

    <!-- added a form so we can catch list item data from user input -->
   <form action="/" method="POST">

    <input type="text" name="newItem">  
    <button type="submit" name="button">Add</button>
    
   </form>
</body>
</html>
```
The whole JS code:
```js
const express = require("express");
const bodyParser = require("body-parser");

const app = express();
app.set('view engine', 'ejs');
app.use(bodyParser.urlencoded({extended: true}));


app.get("/", function(req, res){
    var today = new Date();

    var options ={
        weekday: "long",
        day: "numeric",
        month: "long"
    };

    var day = today.toLocaleDateString("en-US", options);

    res.render("list", {kindOfDay: day});
});


app.post("/", function(req, res){
    var item = req.body.newItem;        //<---
    console.log(item);                  //<---
});

app.listen(3000 , function() {
    console.log("Server started on port 3000");
});
```
What happens here is that when the user clicks on the **Submit button** (Add) they will trigger a **GET** request to our server (app.js)which takes them inside the `app.get` scope, which will do all of the **logic** behind the scene and send back a **POST** request to the user with the `app.post` which takes the user's **submitted data** and console logs it in our CLI.  
*(**Note**: the reason we are not using `res.sendFile(__dirname + "/index.html");` in our `app.get` anymore is because we have already specified that we wish to render a page called 'list.ejs' with this line of code: `res.render("list", {kindOfDay: day});` at the end of `app.get` **(?)**.)*  
Now, how can we **add** this item `newItem` from the user's input into our **html's unordered list** instead of just console logging it's value in our terminal?  
You might be tempted to do this:
```js 
app.get("/", function(req, res){
    var today = new Date();

    var options ={
        weekday: "long",
        day: "numeric",
        month: "long"
    };

    var day = today.toLocaleDateString("en-US", options);

    res.render("list", {kindOfDay: day});
});


app.post("/", function(req, res){
    var item = req.body.newItem;
    res.render("list", {newListItem: item}); //<---
});
```
 - But this will generate an **error** because all of the key values must be specified in the **first** `res.render` that we have written.**(?)**  
So the right way to pass `item`'s data from the app.js(->server) to `newListItem` in the list.ejs (->html file) is this:
```js
app.get("/", function(req, res){
    var today = new Date();

    var options ={
        weekday: "long",
        day: "numeric",
        month: "long"
    };

    var day = today.toLocaleDateString("en-US", options);

    res.render("list", {kindOfDay: day, newListItem: item});   //<---
});


app.post("/", function(req, res){
    var item = req.body.newItem;

     res.redirect("/");             //<---
});
```
*(**Personal Note:** From what I understand, if we want to get user inputs with express then we must use **form** with the **post** action and that data (from the form) is only retrievable through app.post.  
So when we open localhost:3000 (root route/working directory) everything executes in this order (for our specific example at least): **APP.JS** --> code inside app.js redirects user to list.ejs (html file) at end of app.get scope (we do not see this because it happens very fast so we think list.ejs is the first thing that gets rendered) (app.post does not triggered yet because there were no post requests made)--> **LIST.EJS** --> in our html file (list.ejs) form redirects user to the root route "/" (localhost:3000) once the user has clicked on the **Submit** button --> **APP.JS** --> code inside app.js executes again (updating/replacing data in app.get scope with more current ones) and now executing code in app.post scope too due to the **post request** made from the **form**, the app.post redirects user to "/" root route --> **APP.JS** -->  code inside app.js gets executed again without going inside app.post. The code in app.get redirects the user to list.ejs --> **LIST.EJS** --> user sees updated data on the html page without understand how this magic happened (at least I didnt until I wrote this down lol) **(?)**)*  
 - Once the **POST** request gets triggered from our server side it will **redirect** the user back to the **root** route which will execute the code inside of `app.get` once more because the user has been redirected there thanks to `res.redirect("/");` in `app.post`. This **updates** the value of `newListItem` in the `app.get` thanks to the `app.post` assigning it a new value through variable `item`. 
Now, since we are using the variable '`item`' in two different scopes we have to **globalize** it so it can be modified **outside and within the scopes**.  
The other issue with this code is that it updates the variable '`item`'(`newListItem` in html) to it's latest value. But we want to store all of `item`'s values. We need to **append** the lastest entered value with the js function `.push();` and to do that we need to store all of these new values in an **array** and pass it to the res.render which updates the value of `newListItem` in html with the values in the array '`items`':
```js
var items = [];                                                   //<---


app.get("/", function(req, res){
    var today = new Date();

    var options ={
        weekday: "long",
        day: "numeric",
        month: "long"
    };

    var day = today.toLocaleDateString("en-US", options);

    res.render("list", {kindOfDay: day, newListItem: items});     //<---
});


app.post("/", function(req, res){
    var item = req.body.newItem;

    items.push(item);                                             //<---
    
    res.redirect("/"); 
});
```

 - This will add all submitted items and display them in one array string, which looks bad. What if we wish to post each new added item in a new line(li)? We can use the Scriptlet tag to make a **for loop** inside our html code and display each item in the `newListItems` array inside an li.  
*(I did not know newListItems was automatically considered an array too if it's key value (items) was one. I did not know that you can use [i] which is a piece of code inside the <%= > which can only output  the value into the template)*  
 [**Note**: updated variable name from newListItem to newListItems to make it more obvious that it's an array]
 ```html
  <ul>
    <li>Buy Cat Food</li>
    <li>Cook the Cat Food</li>
    <li>Serve my Cat the Cat Food</li>
    <% for (let i = 0; i < newListItems.length; i++) { %>
        <li><%= newListItems[i] %></li>
        
    <% } %>
   </ul>
   ```
This is how we can pass data from the server to our template, populating it with dynamic content.  

### Adding Static Files
Now remember how to add images/css/js static files to our project from my Express notes?  
Firstly, if we add a **stylesheet link** (like Bootstrap's) it will work and load on our page because those files reside in a **remote location** (their server) but if we have a file like styles.css in our **working directory** (which means it's **local** to our working directory || a **statis page** in our local file system that we are trying to pull up) it will not load up, because Express does not automatically serve up all of the files in our working directory (root route). Express only serves the 'main access point' which is defined in the json file as 'app.js' and it also serves up the 'views' folder but everything else gets ignored. And we are linking the styles.css from our root route `css/styles.css`, expecting it to go to `localhost:3000/css/styles.css` which is expected to work for a **static website**, which we no longer operate. *All of our files need to be served up by our server itself. We can no longer use a href based link to serve up static files because we are using a  server based website.*    
To fix this issue we need to **tell Express specifically to server up x and y static files** with their location.  
We can do this with Express' function `static`: 
```js
app.use(express.static("public"));
```
Where 'public' is a folder inside of our working directory where all of our static files reside. With this in mind, **we do not need to add 'public' in front of our pathings** as it is already relative to every static file inside the public folder already, basically meaning that we are inside the public folder already **(?)**:    
so instead of:  
`public/css/styles.css`  
it should just be:  
`css/styles.css`

# Templating vs Layouts
- For this exercise we will make a new To Do list page called 'work'
    - We will change the '`kindOfDay`' template name to '`listTitle`' because we want it to display a different list names depending on which To Do list page we're landing on, so the name sounds more *intuitive*. (If the user tries to access the home route ("/") then `listTitle` will display as the *current date* but if they access the work page we can make `listTitle` display a *dynamic* piece of data that we can decide for that page.) :
    ```js
    app.get("/work", function(req,res){
    res.render("list", {listTitle: "Work List", newListItems: workItems});
    });
    //listTitle will display "Work List"  when user makes GET request to /work route

    app.post("/work", function(req, res){
        var item = req.body.newItem;

        workItems.push(item);
        //we push new submitted item to workItems array
        
        res.redirect("/work");
        //redirect to app.get("/work") which renders list.ejs with work  templatings
    });
    ```
- *Now, the issue with this code is that when we submit a new item to our list on the work page it will get added to the root route page's list and redirect us to the root route page.* Why is that? :  
     ```html
      <form class="item" action="/" method="POST">
          <input type="text" name="newItem" placeholder="New Item" autocomplete="off">  
          <button type="submit" name="button">+</button>
         </form>
In this form when we press the Submit button it redirects us to the root route which then goes into the `app.post("/")` (home route) which tells the code to add the newly submitted item in the `items` array (which belongs to the root route's To Do list) and redirects us to the home route. 

- If we give the **button a dynamic value** which changes depending on which **route** we accessed the form from then we can use that value later on for an if statement which will decide if  the **submitted item** goes in the `items` or `workItems` array depending on which route the user finds themselves submitting from:  
*html*
```html
<body>
    <div class="box" id="heading">
       <h1> <%= listTitle %> </h1>
    </div>

    <div class="box">
         <% for (let i = 0; i < newListItems.length; i++) { %>
            <div class="item">
                <input type="checkbox">
                 <p><%= newListItems[i] %></p>
            </div>
         <% } %>

         <form class="item" action="/" method="POST">
          <input type="text" name="newItem" placeholder="New Item" autocomplete="off">  
          <button type="submit" name="list" value=<%= listTitle %>>+</button>
         </form>
    </div>

</body>
```
 - Now if user finds themselves in the work page's path then the button's value will be "Work" because we specified in the `app.get("/work")` we render the template `listTitle` to equal to "Work List" (it only takes first word, "Work")
 - We take that value "**Work**" and make an if statement inside of the `app.post("/"
 )` that if the button's value is === "Work" then we will 
    - push newly added item to `workItems` array 
    - redirect user to `app.get("/work")`
- Else if form's button value is equal to **anything else** then push newly submitted item to `items` array and redirect to `app.get("/")` :
```js
app.post("/", function(req, res){

    var item = req.body.newItem;
    
    if(req.body.list === "Work"){       //<---
        workItems.push(item);
        res.redirect("/work");
    } else{
            items.push(item);
            res.redirect("/");
    }
});
```
This is one example of how useful templating can be.
### Layouts
What if we have **multiple pages** where the only thing that changes is their **content** but the styling, boilet plates and footers, for example, remain the same?  
We can save those **repetitive pieces of codes** in ejs files (called Layouts or Partials) within our views folder. Let's say we saved the 
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="css\styles.css">
    <title>To Do List</title>
</head>
<body>
```
in a file called *header.ejs* and  
```html
</body>
<footer>
    <p>Coded with love by <a href="https://github.com/anasolomon" target="_blank">Ana-Maria Tanasciuc </a> @<a href="https://github.com/angelabauer" target="_blank">Angela Yu</a></p>
</footer>
</html>
```
in a file called *footer.ejs*, both of these files in the views folder.  
If we make a new page, we can simply integrate these two Layouts as our boiler plate, our page now should look like :
```html
<%- include('header'); -%>
    <div class="box" id="heading">
       <h1> <%= listTitle %> </h1>
    </div>

    <div class="box">
         <% for (let i = 0; i < newListItems.length; i++) { %>
            <div class="item">
                <input type="checkbox">
                 <p><%= newListItems[i] %></p>
            </div>
         <% } %>

         <form class="item" action="/" method="POST">
          <input type="text" name="newItem" placeholder="New Item" autocomplete="off">  
          <button type="submit" name="list" value=<%= listTitle %>>+</button>
         </form>
    </div>

    <%- include('footer'); -%>
```
Where we include those two files in the place of that repetitive code.  
So if we try to make a completely new ejs page, giving it a app.get route and render it with EJS, the boilet plate will look completely the same.  
*html*
```html
<%- include('header'); -%>
<h3>This is my about page</h3>

<p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Mollitia expedita, impedit at ipsa placeat et laudantium. Libero reiciendis ullam repellendus voluptatibus enim, error deleniti? Facilis laboriosam quisquam quis assumenda aspernatur!</p>

<%- include('footer'); -%>
```
*JS*
```js
app.get("/about", function(req,res){
    res.render("about");
});
```

