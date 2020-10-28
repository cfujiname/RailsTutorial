# Using Rails for creating an application

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


The ‘app’ folder usually contains all MVC folders .
Inside that, there is the application_controller, which is core controller. All other controllers will be extended from this one
then application.html.erb contains html that will wrap around all inner views (‘yield’)


## class PostsController < ApplicationController

- here, PostController is extending ApplicationController (see the '<' sign)
- create a method 
```
def index

end
```
- create a file index.html.erb on /posts folder and create a header Index
- go to /config/locales/routes.rb and create: root 'posts#index' --> it relates to our posts controller

## class PagesController < ApplicationController

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

## class PostsController < ApplicationController

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
 
## post.rb
 
 - go to post.rb
 - we need to validate, so it can throw errors if fields are not complete
 ```
 class Post < ActiveRecord::Base
   validates :title, presence: true, 
                      length: {minimum: 5}
 end
 ```
## post_controller.rb

 - go to post_controller.rb
 - change the code in method create to:
 ```
 def create
    @post = Post.new(post_params)
  
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
 
 
 ## new.html.erb
 
 - go to new.html.erb
 - insert 'if statement' underneath the form_for so code above can work on the page, so it throws error 
```
<% if @post.errors.any? %>
  <% @post.errors.full_messages.each do |msg| %>
  <div class='alert alert-danger'> <%= msg %> </div>]
<% end >

```

## index.html.erb

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
## show.html.erb

- go to show.html.erb
- we will add an 'Edit' button to the posts underneath the body like so:
```
<hr>
<%= link_to "Edit", edit_post_path(@post), :class => 'btn btn-default' %>

```

## post_controller.rb

- go to post_controller.rb to create method edit:
```
def edit
  @post = Post.find(params[:id]);
end
```
- here, we want to get the individual post just like in the show page
- then we need to create a file edit.html.erb in /views/posts

## edit.html.erb

- in this created file (pretty much the same code as in new.html.erb), but change the path to post_path(@post) and define method
```
<%= form_for :post, url: post_path(@post), method: :patch do |form| %>
     <% if @post.errors.any? %>
       <% @post.errors.full_messages.each do |msg| %>
       <div class='alert alert-danger'> <%= msg %> </div>]
     <% end >
     <p>
       <%= form.label :title %><br>
       <%= form.text_field (:title, {:class => 'form_control'}) %>
     </p>
 
     <p>
       <%= form.label :body %><br>
       <%= form.text_area (:body, { :class => 'form_control'}) %>
     </p>
 
     <p>
       <%= form.submit({ :class => 'btn btn-primary'}) %>
     </p>
     <% end %>
```
## post_controller.rb

- after changing the edit.html.erb, we need to go back to post_controller.rb
- we need to create the method 'update' so the code in edit.html.erb works
```
def update
  @post = Post.find(params[:id]);
  if(@post.update(post_params))
      redirect_to @post
    else
      render 'edit'
    end
end
```
- we want also to create a 'destroy' method to be able to delete the post
```
def destroy
  @post = Post.find(params[:id]);
  @post.destroy
  
  redirect_to posts_path
end

```
- so that this works, we need to go to show.html.erb

## show.html.erb

- we need to create a button for deleting in this page underneath the 'edit' button:
```
<%= link_to "Delete", post_path(@post), 
                    :method :delete,
                    :data {confirm: 'Are you sure?'},
                    :class => 'btn btn-danger' %>
```
# Adding comment functionality

- for this, we need to create the environment for it as well:

> rails g model Comment username:string body:text post:references

- references is because we want a relationship with the post and the comment
- to create the table

> rake db:migrate

## class Comment < ActiveRecord::Base (in models/concerns)

- here, you can see the following code generated by rails

```
belongs_to :post
```
- this is the reference between post and comment

## class Post < ActiveRecord::Base

- we need to insert in this class the relationship as well, before the validation, like so:
```
has_many :comments
```
## config/locales/routes.rb

- we need to create a route here as well, that will be inside resources :posts as we want the relationship between them

```
resources :posts do
  resources: comments
end
```
- run on the terminal

> rake routes

- and then

> rails g controller Comments

## show.hrml.erb

- we will now enable the commenting function
- underneath the codes we already got, insert:
```
<hr>
<h3>Add Comment</h3>
<%= form_for([@post, @post.comments.build]) do |form| %>
     <p>
       <%= form.label :username %><br>
       <%= form.text_field (:username, {:class => 'form_control'}) %>
     </p>
 
     <p>
       <%= form.label :body %><br>
       <%= form.text_area (:body, { :class => 'form_control'}) %>
     </p>
 
     <p>
       <%= form.submit({ :class => 'btn btn-primary'}) %>
     </p>
     <% end %>
```

## class CommentsController < ApplicationController

- we need to define a method to create the comment

```
def create
  @post = Post.find(params[:post_id])
  @comment = @post.comments.create(comment_params)
  redirect_to post_path(@post)
end

private 

def comment_params
  params.require(:comment).permit(:username, :body)
end
```
## \_comments.html.erb and \_form.html.erb

- create a file \_comments.html.erb and \_form.html.erb in views/comments - with underscore because they are partials (Partial allows separation of the layout code out into a file which will be reused throughout the layout and/or multiple other layouts. For example, you might have a login form that you want to display on 10 different pages on your site)
- we will insert a code here to be able to show the comments:

```
<h3>Comments</h3>

<% @post.comments.each do |comment| %>
  <div clas='well'
  <p> <strong> <%= comment.username %> </strong>: <%= comment.body %> </p>
  </div>
<% end %>
```

# Creating a user with Devise.rb

- to create the User Class, you can install the gem 'devise'
- then run in your terminal
 
> rails g devise:install

- devise should then create your model, view and controller paths for the user class
- you can edit to the specification you want to your app, like sending email confirmations and so on

# Configure email functionality and server with devise

- to enable your app to send emails to the users, ensure to confirgure the mailer setting in your 3 environments 
- in config/initializers/devise.rb -> configure the mailer settings 
- set up the user_mailer.rb
- in routes.rb include the devise_for modules you want to use
- check if all devise modules you want to use in the User Class are correct. If changes need to be done, you need to cfreate a new migration file and migrate again to adjust the database accordingly


# Image processing using Active_Storage and AWS S3 

## GEMFILE 

 - gem 'image_processing'
 - gem 'aws-sdk-s3', require: false
 - gem 'active_storage_validations'
 
 > bundle install
 
 > rails active_storage:install
 
 ## Migrations
 
 - check if database is corrent and then setup and migrate
 
 ## class User < ApplicationRecord
 
 - underneath the devise modules, add
 ```
  has_one_attached :profile_picture, dependent :destroy
  validates :profile_picture, content_type: [:png, :jpg, :jpeg]
  
 ```
 
 ## routes.rb
 
 - underneath resources :users
 - this expects to have a user id coupled to it. To check, use rake routes | grep users
 
 ```
 member do
  delete 'delete_image/:image_id', action: 'delete_image', as: 'delete_image'
 end
 ```
 ## users_controller.rb
 
 - add the method to the controller
 
 ```
 def delete_image
  image = ActiveStorage::Attachment.find(params[:image_id])
  if current_user == image.record
    image.purge
    redirect_back(fallback_location: request.referer)
  else
    redirect_to root_url, notice: 'Don't know what this does'
  end
 end
 ```
 - this basically looks into the storage to see if there is an image attached to the user, then the action can be perfomed
   - the line current_user == image.record is to make sure current user can only delete his own, as it is coupled to his id 
 - add :profile_picture to the user_params method to enable the user to upload the picture
 
 ## views (user profile)
 
 - add the following to be able to upload picture
 
 ```
 <div class="form-group">
   <% if user.profile_picture.attached? && user.profile_picture.representable? %>
   <%= image_tag user.profile_picture.variant(resize '100x100').processed %>
    <div class="upload-hider-labels">
    <%= link_to '#', class: 'replace_upload' do %>
      Replace Profile Photo
    <% end %>
    <span class='separator-bar'>|</span>
      <%= link_to 'Remove Photo without replacing',
      delete_image_user_url(image_id: user.profile_picture.id),
      method: :delete,
      data: { confirm: 'Are you sure?' },
      class: 'remove-upload' 
        %>
    </div>
    <% else %>
    <div class='profile-picture no-profile-picture'></div>
    <% end %>
    
    <div class="upload-hider-fields">
      <%= form.file_field :profile_picture, class: 'form-control' %>
    </div>
 </div>  
   
 ```
## show.html.erb for users

- change the code to show image

## AWS S3
- create bucket
- create policy with following permissions:
 s3:ListBucket
 s3:PutObject
 s3:GetObject
 s3:Delete:Object

- add resources ARN
  - for the bucket: just paste name of bucket
  - for objects, choose 'Any'
- name the policy and click on create
- create user and click on programmatic access
- attach the policy to the user, save
- download the csv provided

## rails credentials:edit
- insert the aws credentials

## storage.yml
- uncomment the aws ones and change region and bucket accordingly

## production.rb

- change the config of active_storage for :amazon
- active_storage uses local as default

--> Before deployment, SSH and install imagemagick because we allow image manipulation such as resizing

# Profile and Post Photo upload functionality

- install gem 'mini-magick'
- check that your classes accept the relationships necessary to be able to upload the images
- make sure you have an upload button in your html.erb files and that the image can be shown
- minimagick allows you to edit the image as per functionality allowed
  - in our projecft, we used the _resize_ functionality
  
# Friendship functionality

- install gem 'has_friendship'
- make sure the routes are correctly prepared 
- check migration files before setting up the database
- change your views html.erb to the way you want them to be/redirect, etc.



 

