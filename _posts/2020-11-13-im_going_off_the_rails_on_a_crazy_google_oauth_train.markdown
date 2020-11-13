---
layout: post
title:      "I'm going off the Rails on a crazy Google OAuth train..."
date:       2020-11-13 23:36:25 +0000
permalink:  im_going_off_the_rails_on_a_crazy_google_oauth_train
---


Perhaps one of the trickiest parts of building my Ruby on Rails application was implementing OAuth using OmniAuth and Devise. I spent hours trying to figure out the source of one seemingly small error.

![ErrorScreenshot](https://i.imgur.com/eomhhpd.png)

I checked the common ‘gotchas,’ scoured Stack Overflow for solutions, asked fellow students for help, and read every blog post I could find relating to the error. It was literally driving me CRAZY, and when I finally discovered the solution, it was so simple I had to share. Before we get to the bottom of it, let’s take a minute to understand how OAuth, OmniAuth, and Devise work together to authorize users. 

Open Authorization, commonly referred to as OAuth, is an authorization protocol which allows users to verify their identity through a third-party application. Users are able to sign in without having to create a new account or provide their password. Commonly used third-party providers include Facebook, Google, Twitter, and GitHub. 

To standardize the authorization process, OmniAuth was created. The OmniAuth library consists of “strategies,” which are used to easily implement OAuth for each provider. I used the omniauth-google-oauth2 strategy, which allows users to login to a web application using Google. I found an AWESOME post by [Adam Langsner](https://medium.com/@adamlangsner/google-oauth-rails-5-using-devise-and-omniauth-1b7fa5f72c8e) that discusses how to configure OAuth using Devise and OmniAuth. My application was already setup with Devise, so I will be covering the steps pertaining to OmniAuth only. 

To give you some context, my application is designed for high school strength and conditioning coaches. Coaches can create an account, and once logged in, add students and performance test results. Since I wanted coaches from the same school to have access to every student, I did not create an admin model, which differs from the implementation described in Langsner's article. 

**Step 1** – Add the OmniAuth gem and the provider specific gem to your Gemfile and run bundle install. 

```
gem 'omniauth'
gem 'omniauth-google-oauth2'
```

**Step 2** – In devise.rb add the following inside the setup block. This code is telling OmniAuth which provider they will be working with and defines the CLIENT_ID and CLIENT_SECRET environment variables. 

`config.omniauth :google_oauth2, ENV['GOOGLE_OAUTH_CLIENT_ID'], ENV['GOOGLE_OAUTH_CLIENT_SECRET']`

**Step 3** – Add the devise module :omniauthable to the User model.

```
class User < ApplicationRecord
 
  devise :database_authenticatable, :registerable,
     :recoverable, :rememberable, :validatable, 
     :omniauthable, omniauth_providers: [:google_oauth2]

end
```

**Step 4** – Add three new columns to the :users table to store information from Google. Run rails db:migrate after double checking the migration.

```
class AddOauthColumnsToUsers < ActiveRecord::Migration[6.0]
  def change
    add_column :users, :full_name, :string
    add_column :users, :avatar_url, :string
    add_column :users, :uid, :string
  end
end
```

**Step 5** – Define the omniauth callback route in config/routes.rb. The callback route handles the final redirect back to the server and contains an access token along with user information. Devise also requires a root path to be defined. 

```
Rails.application.routes.draw do

  devise_for :users, controllers: { omniauth_callbacks: 'users/omniauth_callbacks' }
	
  root 'students#index'

end
```

**Step 6** – Create a new project in the [Google Cloud Console](https://console.cloud.google.com/). Refer back to Langsner’s article for detailed instructions. After creating the project, you will have a new CLIENT_ID and CLIENT_SECRET to identify your application.  

**Step 7** – Install the dotenv-rails gem in the development and test block in your Gemfile. Dotenv allows environment variables to be loaded from the .env file into the ENV variable in development

```
group :development, :test do
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
  gem 'dotenv-rails', '~> 2.7'
end
```

**Step 8** – Create a .env file in the root project directory and add the CLIENT_ID and CLIENT_SECRET. 

```
GOOGLE_OAUTH_CLIENT_ID = ‘5325u5852853285732857.googleusercontent.com'
GOOGLE_OAUTH_CLIENT_SECRET = 's57T85373-E5AzkG'
```

**Step 9** – Add .env to gitignore file (located in the root directory of the project). Anything in the gitignore file will be untracked in your repository. 

**Step 10** – Generate an OmniAuth callbacks controller. This controller is responsible for handling the final redirect and processing user information. It is VERY IMPORTANT that it is nested under controllers and users (controllers/users/omniauth_callbacks_controller.rb).

```
class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
  
  def google_oauth2
    user = User.from_google(from_google_params)
    if user.present?
      sign_out_all_scopes
      flash[:success] = t 'devise.omniauth_callbacks.success', kind: 'Google'
      sign_in_and_redirect user, event: :authentication
    else
      flash[:alert] = t 'devise.omniauth_callbacks.failure', kind: 'Google', reason: "#{auth.info.email} is not authorized."
      redirect_to new_user_session_path
    end
  end

  protected

  def after_omniauth_failure_path_for(_scope)
    new_user_session_path
  end

  def after_sign_in_path_for(resource_or_scope)
    stored_location_for(resource_or_scope) || root_path
  end

  private

  def from_google_params
    @from_google_params ||= {
      uid: auth.uid,
      email: auth.info.email,
      full_name: auth.info.name,
      avatar_url: auth.info.image
    }
  end

  def auth
    @auth ||= request.env['omniauth.auth']
  end

  end
```

**Step 11** – Add the class method #fromgoogle to the User model. This method will try to find an existing user with an email matching the email in params. If a user is found, it will update the existing user’s information with data from google. If the user is not found, it will create a new user.

```
class User < ApplicationRecord
  
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :validatable, :omniauthable, omniauth_providers:   [:google_oauth2]
         
  has_many :performance_tests
  has_many :students, through: :performance_tests
  has_many :test_types, through: :performance_tests
  
  validates :full_name, presence: true

  def self.from_google(uid:, email:, full_name:, avatar_url:)
    user = User.find_or_create_by(email: email) do |u|
      u.uid = uid
      u.full_name = full_name
      u.avatar_url = avatar_url
      u.password = SecureRandom.hex
    end
    user.update(uid: uid, full_name: full_name, avatar_url: avatar_url)
  end
  
end
```

After following these steps, I was ready to test it out. I started my server and clicked the link “Sign in with GoogleOauth2.” I was successfully redirected to the google sign in page. 

![Google Sign In Page](https://i.imgur.com/RNqjESh.png)

I selected an existing account and crossed my fingers as I waited to be redirected back to my application. However, I received the following error. 

![ErrorScreenshot](https://i.imgur.com/eomhhpd.png)

“Could not find a mapping for true.” Hmmmm, although I had never seen this error before, it seemed like it had something to do with the routing. I navigated to [localhost:3000/rails/info/routes](localhost:3000/rails/info/routes) to check my routes. 

![routes](https://i.imgur.com/xw0MPyx.png)

Everything looked as it should, so I added a ‘byebug’ in multiple places to confirm that the method was correctly finding/creating a user and updating that user.

```
def self.from_google(uid:, email:, full_name:, avatar_url:)
    byebug
    user = User.find_or_create_by(email: email) do |u|
      u.uid = uid
      u.full_name = full_name
      u.avatar_url = avatar_url
      u.password = SecureRandom.hex
    end
    user.update(uid: uid, full_name: full_name, avatar_url: avatar_url)
    byebug
end
```

I confirmed that an existing user was in fact being selected from the users table and being updated. 

![byebug](https://i.imgur.com/6XaAcjT.png)

Aha! The return value from this method should be a user instance, not ‘true.’ When I looked up the #update method in the Ruby Guides, I confirmed that when an object is successfully updated, it returns true. If the object is invalid and cannot be saved, the method returns false. To fix this error and overcome the mapping error, I simply added the user to the end of the method. 

```
class User < ApplicationRecord
 
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :validatable, :omniauthable, omniauth_providers: [:google_oauth2]
         
  has_many :performance_tests
  has_many :students, through: :performance_tests
  has_many :test_types, through: :performance_tests
  
  validates :full_name, presence: true

  def self.from_google(uid:, email:, full_name:, avatar_url:)
    user = User.find_or_create_by(email: email) do |u|
      u.uid = uid
      u.full_name = full_name
      u.avatar_url = avatar_url
      u.password = SecureRandom.hex
    end
    user.update(uid: uid, full_name: full_name, avatar_url: avatar_url)
    user
  end
  
end
```

This small hiccup took lots of digging and debugging (much more than described here). It reinforced the idea of return values and how important it is to know what each method should return and what it is ACTUALLY being returned. When you hit an unfamiliar error, slowly step through the code to determine what is happening. Use byebug, pry, the rails console, server logs, and of course your peers, to crack the code and prevent yourself from going off the Rails. 






