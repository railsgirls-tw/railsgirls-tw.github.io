---
layout: default
title: 給 Rails Girls 應用加入評論功能
permalink: commenting
---

# Rails Girls App 評論功能

*Created by Janika Liiv, [@janikaliiv](https://twitter.com/janikaliiv)*

We are going to add the possibility to comment ideas in your *railsgirls* application.

The instructions for installing rails and building the ideas app can be found [here](/app)

## Step 1: 加入 foreigner gem

加入 Gemfile

{% highlight ruby %}
gem 'foreigner'
{% endhighlight %}

安裝相依套件（如果伺服器正在跑，按 `CTRL-C` 停止。）

{% highlight sh %}
bundle install
{% endhighlight %}

## Step 2: 建立 comment 鷹架

建立 comment 鷹架，有評論者的姓名、評論內容以及這是哪個評論哪個 idea （idea_id）。

{% highlight sh %}
rails g scaffold comment user_name:string body:text idea_id:integer
{% endhighlight %}

## Step 3: 加入 foreign key 連結

Add to migration the foreign key connection. Open db/migrate/ and the file, which name ends with 'create_comments.rb'. After

{% highlight ruby %}
t.timestamps
end
{% endhighlight %}

add

{% highlight ruby %}
add_foreign_key :comments, :ideas
{% endhighlight %}

Now migrate the database changes by typing in your terminal
{% highlight sh %}
rake db:migrate
{% endhighlight %}

start your server with:
{% highlight sh %}
rails s
{% endhighlight %}

## Step 4: 加入模型關係

You need to make sure that Rails knows the connection between objects (ideas and comments).
As one idea can have many comments we need to make sure the idea model knows that.
Open app/models/idea.rb and after the row
{% highlight ruby %}
class Idea < ActiveRecord::Base
{% endhighlight %}
add
{% highlight ruby %}
has_many :comments
{% endhighlight %}

The comment also has to know that it belongs to an idea.So open app/models/comment.rb and after
{% highlight ruby %}
class Comment < ActiveRecord::Base
{% endhighlight %}

add the row
{% highlight ruby %}
belongs_to :idea
{% endhighlight %}

## Step 5: 渲染評論的表格及加入現有評論

Open app/views/ideas/show.html and after the image_tag
{% highlight erb %}
<%= image_tag(@idea.picture_url, :width => 600) if @idea.picture.present? %>
{% endhighlight %}

add
{% highlight erb %}
<h3>Comments</h3>
<% @idea.comments.each do |comment| %>
  <div>
    <strong><%= comment.user_name %></strong>
    <br />
    <p><%= comment.body %></p>
  </div>
<% end %>
<%= render 'comments/form' %>
{% endhighlight %}

In app/controllers/ideas_controller.rb add to show action after the row
{% highlight ruby %}
@idea = Idea.find(params[:id])
{% endhighlight %}

this
{% highlight ruby %}
@comment = @idea.comments.build
{% endhighlight %}

Open app/views/comments/_form.html and after
{% highlight erb %}
  <div class="field">
    <%= f.label :body %><br />
    <%= f.text_area :body %>
  </div>
{% endhighlight %}


add the row
{% highlight erb %}
<%= f.hidden_field :idea_id %>
{% endhighlight %}
That's it. Now view an idea you have inserted to your application and there you should see the form for inserting a comment
