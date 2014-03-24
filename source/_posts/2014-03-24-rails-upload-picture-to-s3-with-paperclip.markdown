---
layout: post
title: "rails: upload picture to s3 with paperclip"
date: 2014-03-24 14:30
comments: true
categories: Rails 
---

* Create a new s3 bucket, add CORS policy:<br>
```
<?xml version="1.0" encoding="UTF-8"?>
<CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <CORSRule>
        <AllowedOrigin>*/AllowedOrigin>
        <AllowedMethod>GET</AllowedMethod>
        <AllowedMethod>POST</AllowedMethod>
        <AllowedMethod>PUT</AllowedMethod>
        <MaxAgeSeconds>3000</MaxAgeSeconds>
        <AllowedHeader>*/AllowedHeader>
    </CORSRule>
</CORSConfiguration>
```
Make the s3 bucket as public <br>
```
{
	"Version": "2008-10-17",
	"Statement": [
		{
			"Sid": "AllowPublicRead",
			"Effect": "Allow",
			"Principal": {
				"AWS": "*
			},
			"Action": "s3:GetObject",
			"Resource": "arn:aws:s3:::bucketname/*
		}
	]
}
```

* Edit Gemfile to add following gems<br>
```
# Gemfile
gem 'paperclip', '~>3.5.0'
# 3.5.0 version paperclip works with Rails 4
gem 'aws-sdk'
gem 's3_direct_upload'
```


* Specify AWS configuration variables for production environment<br>
```
# config/environments/production.rb
config.paperclip_defaults = {
  :storage => :s3,
  :s3_credentials => {
    :bucket => ENV['S3_BUCKET_NAME'],
    :access_key_id => ENV['AWS_ACCESS_KEY_ID'],
    :secret_access_key => ENV['AWS_SECRET_ACCESS_KEY']
  }
}
```

* Set AWS configuration variables on heroku app<br>
```
$ heroku config:set S3_BUCKET_NAME=your_bucket_name
$ heroku config:set AWS_ACCESS_KEY_ID=your_access_key_id
$ heroku config:set AWS_SECRET_ACCESS_KEY=your_secret_access_key
``` 
(your aws credentials reside in account->security credentials)<br>
check heroku configuration<br>
```
$ heroku config
```

* Rails update database
 
```
$ rails g migration AddPicPaperClip
```
Edit migration as below, we are adding four columns to the existing movies table<br>
```
class AddPicPaperclip < ActiveRecord::Migration
  def self.up
    add_column :movies, :pic_file_name, :string
    add_column :movies, :pic_content_type, :string
    add_column :movies, :pic_file_size,    :integer
    add_column :movies, :pic_updated_at,   :datetime
  end
  def self.down
    remove_column :movies, :pic_file_name
    remove_column :movies, :pic_content_type
    remove_column :movies, :pic_file_size
    remove_column :movies, :pic_updated_at
  end
end

```
Run the migration to update the db<br>
```
$ rake db:migrate
```

* Models
```
#app/model/movies.rb
class Movie < ActiveRecord::Base
  validates :title, :presence => true, :uniqueness => true, :length => {minimum: 3, too_short: "must have at least %{count} chars"}
  before_save :capitalize_title
  has_attached_file :pic, :styles =>
        { :medium => "300*00>", :thumb => "100*100>" }
end
```

* Views<br>
Modify form file for new/edit view to enable the file upload
```
#app/view/movies/_form.html.erb
<%= form_for(@movie, :html => { :multipart => true })  do |f| %>
  ...
  <p>
    <%= f.label :pic %><br/>
    <%= f.file_field :pic %>
  </p>
  ...
<% end %>
```

* Controller<br>
Permit the parameters
```
#app/controler/movie_controller.rb
 # POST /movies.json
  def create
    @movie = Movie.new(movie_params)

    respond_to do |format|
      if @movie.save
        format.html { redirect_to @movie, notice: 'Movie ' + @movie.title + ' was successfully created.' }
        format.json { render action: 'show', status: :created, location: @movie }
      else
        format.html { render action: 'new' }
        format.json { render json: @movie.errors, status: :unprocessable_entity }
      end
    end
  end

   def movie_params 
#     params.require(:movie).permit(:title, :rating, :description, :release_date) 
     params.require(:movie).permit! 
   end 
```

* Show the uploaded picture 
```
#show.haml.html
%h4 Profile Pic:
  =image_tag @movie.pic.url(:medium)
```

* Push to Heroku
```
$ git push
$ git push heroku master
$ heroku run rake db:migrate
```


sources:
https://devcenter.heroku.com/articles/paperclip-s3<br>
http://www.blitztheory.com/category/tutorials/<br>

