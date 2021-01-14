---
layout: post
title:      "Leveraging Custom Serializers: JavaScript, Rails API, and Fast JSON API"
date:       2021-01-14 23:31:27 +0000
permalink:  leveraging_custom_serializers_javascript_rails_api_and_fast_json_api
---


Perhaps one of the biggest challenges of this project was sending data from the “client-side” to server-side (and vice-versa). I created **FoodMood**, a recipe finder designed for people wanting new dinner inspiration. Users can select the number of meals they want and what they are in the mood for. My application functions by capturing user input and retrieving related moods, recipes, ingredients, and comments from the database. 

Whenever information is exchanged, it must be serialized. In simple terms, serialization is the process of converting a data structure into a format that can be transferred, and eventually read and reconstructed at the destination. Serialization happens every time data is transmitted over a network. 

For example, in my application, `Mood.all( )` is invoked after the HTML content is loaded. This function makes a fetch request to the index action(“/moods”) in the MoodsController to retrieve all instances of the Mood class. 

 ![Listeners](https://i.imgur.com/g31clBg.png)
 
 ![MoodClass](https://i.imgur.com/Bl7YOku.png)
 
Mood instances must then be serialized before being sent back to the browser. To be clear, data from the server is **not** sent as an object. Data is serialized and most commonly sent in JSON format. JSON stands for JavaScript Object Notation and is text, written in JavaScript object notation. Notice below that both an error (rendered as plain JSON) and a successful response (rendered through the RecipeSerializer) are sent back as JSON in lines 13 and 15. 

 ![MoodsController](https://i.imgur.com/Q7LmU5w.png)
 
Look at the `ruby_object` and `serialized_object` variables below. These variables  demonstrate the subtle difference between a Ruby object and a serialized Ruby object in JSON format. In JSON format, the information is sent as text, and the keys of an object must be strings with double quotes. 

 ![Variables](https://i.imgur.com/QARVPZ3.png)

In the case of an error message or a small amount of data, it is easy to manually type the text you want to display. However, what happens when you need to convert a large dataset with multiple associations? As applications become larger and more complicated, you can imagine that manually typing render statements could quickly become messy. Even in my single page application, I needed a way to send my recipes and their associated ingredients and comments, as JSON, in a clean and standardized manner.

I used and highly recommend the [Fast JSON API](https://github.com/Netflix/fast_jsonapi) gem. Fast JSON API is a serializer specifically for Rails APIs. You can generate a serializer for each of your resources and specify which attributes and related attributes (and their attributes) to include. For example, I used a RecipeSerializer to send recipe attributes (:id, :name, :author, etc.) and related ingredients (:recipe_ingredients) using one simple statement. 

 ![RecipeSerializer](https://i.imgur.com/RzqKi9p.png)

The above, would return the following serialized output.

 ![RecipeSerializerOutput](https://i.imgur.com/vCGus1c.png)
 
Notice, however, that one very important detail about a recipe_ingredient is not included, the name of the ingredient! This is because, the name of an ingredient is an attribute of an ingredient and not the recipe_ingredient. But, because a recipe_ingredient "belongs_to" an ingredient, we can use the association in the serializer to obtain the name. 

The true magic of the Fast JSON API is the ability to create custom attributes. To create a custom attribute, type ‘attribute’ followed by the name you want to appear as the key in JSON data. Then, use Ruby block syntax to specify how you want the data to appear. I created a custom attribute for :recipe_ingredients, which allowed me to choose which attributes I wanted to send as JSON (including name). I only included the attributes I needed, so I could easily create JavaScript objects on the frontend, without having to filter through the data. 

 ![CustomSerializer](https://i.imgur.com/murxrHM.png)
 
The above, would return the following serialized output, including the name of the recipe_ingredient!

  ![CustomSerializerOutput](https://i.imgur.com/jgljjlv.png)

Serializers and custom attributes allow you to be selective with the data, the format, and the order of that data. Fast JSON API and similar tools for other languages are crucial for customizing JSON responses and keeping code clean and concise. Most importantly, it saves you time on the frontend and backend, by sending information in one, organized JSON formatted response.  
 
 
 
 
 
 

