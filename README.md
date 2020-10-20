# RailsTutorial for blog

Controller: responsible for looking at the url and decide what to load, for talking to Model and View

Model: responsible for dealing with Database and API

Views: for user interface ---> similar to ruby and sinatra
-----
The ‘app’ folder usually contains all MVC folders .
Inside that, there is the application_controller, which is core controller. All other controllers will be extended from this one
then application.html.erb contains html that will wrap around all inner views (‘yield’)

# Create git repository

- First, create a repository for your project

# Run on your terminal

- You will need to have ruby installed, then:
> gem install rails -v 6.0.2.2

- Depending on what database you want to use, you need to install it as well.

- Run:
> bundle install

- After installing is complete, create your new application using this command:
> rails new applicationname

- it will create all the file structure needed to start your project

# MVC

Controller: responsible for looking at the url and decide what to load, for talking to Model and View

Model: responsible for dealing with Database and API

Views: for user interface ---> similar to ruby and sinatra
-----
The ‘app’ folder usually contains all MVC folders .
Inside that, there is the application_controller, which is core controller. All other controllers will be extended from this one
then application.html.erb contains html that will wrap around all inner views (‘yield’)


# class PostsController < ApplicationController

- here, PostController is extending ApplicationController (see the '<' sign)
- create a method 
```
def index

end
```
- create a file index.html.erb on /posts folder and create a header Index
- go to /config/locales/routes.rb and create: root 'posts#index' --> it relates to our posts controller

# class PagesController < ApplicationController


> rails g controller pages

- create a method in the PagesController 
```
def about

end
```
- create file about.html.erb in views/pages folder and add header 'About'
- go to /config/locales/routes.rb and create add underneath the root:  get 'about' => 'pages#about' (this is the GET request for route 3000/about)
- back to the pages_controller.rb, add in method about implement: 
```
def about
  @title = 'About Us';
  @content = 'What is this about?'
end
```
- go to about.html.erb and add <%= @title %> inside of header instead of the hard coded word About that was written before, sending data from controller to view much easier
- insert <%= @content => underneath @title and inside paragraph html tag

```
<h1><%= @title %></h1>
<p><%= @content %></p>
```

# Back to class PostsController < ApplicationController

- because we need a lot of methods in posts, like show, add, destroy:
  - go to /config/locales/routes.rb
  - underneath get about => 'pages#about'
  - add: resources :posts
  - go to terminal and > rake routes       this shows all the routes in the application
- this routes show the methods we need to create in posts_controller.rb
- then start adding the methods we need:
  ``` 
  def new
  
  end
  ```
     - then create inside /posts the file new.html.erb, add a header Add Post
     - check on browser 3000/posts/new and we can see that header
     - back to the file new.html.erb, add a form:
     
```
     <%= form_for :post, url: posts_path do |form| %>
     <p>
       <%= form.label :title %><br>
       <%= form.text_field :title %>
     </p>
 
     <p>
       <%= form.label :body %><br>
       <%= form.text_area :body %>
     </p>
 
     <p>
       <%= form.submit %>
     </p>
     <% end %>
```
- go to post_controller.rb and add 


``` 
    def create
      render plain: params[:post].inspect
    end 
```

- go to 3000/posts/new and insert title and body to see what is happening
- it returns 
```
{ "title" => "test title", "body" => "test body" }
```
- go to /config/locales/database.yml
--> this is where you put the database information so the users inputs can be saved (update as necessary)

- create model to save the posts
- go to terminal and:
> rails g model Post title:string body:text
(singular for model and plural for controller files)
- this creates also a migration file
- then run
> rake db:migrate 
- this creates the table in the database
- now we can go back to controller and create our methods
- go to posts_controller.rb
  ```
  def create
    @post = Post.new(params[:post])
  end
  ```
  - here, 'Post' in capitals represent the model, that needs to have the params passed to it
  - if we try to insert the post now in the 3000/posts/new, we get an error as we need to also pass the body and title
  - to fix it, we need to create a private method as we don't want to 'share' this method
  ```
  private
  
  def post_params
    params.require(:post).permit(:title, :body)
  end
  ```
  - then pass the method post_params to Post.new(post_params)
  - add a new method .save inside of create
  
  ```
  @post.save
  redirect_to @post
  
  ```
  - this will load the show view
  - add method show that will find the id of the url of that single unique post
  ```
  def show
    @post = Post.find(params[:id]);
  end
  ```
  - then in the /views/posts folder, create show.html.erb
    ```
    <h2><%= @post.title %></h2>
    <p><%= @post.body %></p>
    ```
  - 3000/posts/new , then add title and body, click submit.
  - Url now shows the post id (3000/posts/1)
  
- now back to post_controller.rb
```
def index
  @post = Post.all
end
```
  - go to index.html.erb and create a loop to iterate through the posts
  ```
  <% @posts.each do |post| %>
    <h3><%= post.title %></h3>
    <p><%= post.body %></p>
  <% end %>
  ```
  - now the index page shows the posts

# Styling
- bootstrap cdn
- get the css link
- go to views/layout/application.html.erb
- insert with your stylesheet tags:
```
<%= stylesheet_link_tag 'bootstrap link here' %>
```
- go to getbootstrap.com and click on 'getting started'
- click on starter template and go to 'view source' in your browser
- copy what is inside the nav html tag (after body)
- paste into your index.html.erb
- make the changes you want
- get rid of a tags referring to links
- insert on link tag
```
<li><%= link_to "Home", home_path %></li>

```
- you can add other links as well using the ul tags and li. tags in the same format as above
- go to config/routes.rb
- add to root: 
```
root 'posts#index', as: 'home'

```
- do the same with other routes needed, ex.: about, etc.
- add a div container around 'yield' field like so:
```
<div class="container>
  <%= yield %>
</div>
```
- go to new.html.erb and wrap :title in parenthesis to be able to pass other parameters
```
 <%= form.text_field (:title, {:class => 'form_control'}) %>
```
  - form_control is a bootstrap command
 - add the same to :body
 - and add some bootstrap to the button
 ```
 <%= form.submit({:class => 'btn btn-primary'}) %>
 ```
 
 # post.rb
 
 - go to post.rb
 - we need to validate, so it can throw errors if fields are not complete
 ```
 class Post < ActiveRecord::Base
   validates :title, presence: true, 
                      length: {minimum: 5}
 end
 ```
 # post_controller.rb
 - go to post_controller.rb
 - change the code in method create to:
 ```
 def create
    @post = Post.bew(post_params)
  
    if(@post.save)
      redirect_to @post
    else
      render 'new'
    end
 end
 ```
 - add in method 'new'
 ```
 def new
  @post = Post.new
 end
 ```
 
 
 
 # new.html.erb
 - go to new.html.erb
 - insert 'if statement' underneath the form_for so code above can work on the page, so it throws error 
```
<% if @post.errors.any? %>
  <% @post.errors.full_messages.each do |msg| %>
  <div class='alert alert-danger'> <%= msg %> </div>]
<% end >

```

# index.html.erb

- wrap post title, body and button in a div class, add a link to enable to read more about the post
```
<h1> Posts </h1>
<% @posts.each do |post| %>
  <div class='well'>
  <h3><%= post.title %>
  <p><%= post.body %>
  <%= link_to 'Read More', post_path(post), :class => 'btn btn-default' %>
  </div>
<% end %>  
```
