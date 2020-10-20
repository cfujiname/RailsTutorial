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
- create a method def index
- create a file index.html.erb on /posts folder and create a <h1>Index</h1>
- go to /config/locales/routes.rb and create: root 'posts#index' --> it relates to our posts controller

# class PagesController < ApplicationController

> rails g controller pages

- create a method in the PagesController def about 
- create file about.html.erb in /pages folder and add <h1>About</h1>
- go to /config/locales/routes.rb and create add underneath the root:  get 'about' => 'pages#about' (this is the GET request for route 3000/about)
- back to the pages_controller.rb, add in method about: @title = 'About Us';
- go to about.html.erb and add <%= @title %> inside of header instead of the hard coded word About that was written before, sending data from controller to view much easier
- add @content to pages_controller.rb with the same syntax and add this variable to the about.html.erb, but inside <p></p>

# Back to class PostsController < ApplicationController

- because we need a lot of methods in posts, like show, add, destroy:
  - go to /config/locales/routes.rb
  - underneath get about => 'pages#about'
  - add: resources :posts
  - go to terminal and > rake routes       this shows all the routes in the application
- this routes show the methods we need to create in posts_controller.rb
- then start adding the methods we need:
  - def new
     - then create inside /posts the file new.html.erb, add a header <h1>Add Post</h1>
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
> rails g model Post
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
