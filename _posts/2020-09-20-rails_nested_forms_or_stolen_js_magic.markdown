---
layout: post
title:      "Rails nested forms or stolen JS magic. "
date:       2020-09-20 23:25:29 +0000
permalink:  rails_nested_forms_or_stolen_js_magic
---


Once upon a time, there was a beginning coder, who was very enthusiastic about the future. But it was 2020 and those times weren’t easy for anyone. One day dark force visited him at the starting of his Rails project path and has stolen JS magic. As we already know, the young coder was too optimistic, and decided to continue his way without it…

I wish everything was magical, but unfortunately, it is not. Long story short, for some reason I’ve got a JS error right after generating a new Rails Project. And after a day of fighting, I gave up and started working on the actual application. Everything was going well until I came to the point where I had to save data from the nested forms into three different models. 

I had 3 Models:

```
class Cocktail < ApplicationRecord
    belongs_to :user
    has_many :cocktail_ingredients
    has_many :ingredients, through: :cocktail_ingredients
    accepts_nested_attributes_for :cocktail_ingredients
```

```
class CocktailIngredient < ApplicationRecord
    belongs_to :cocktail
    belongs_to :ingredient 
    accepts_nested_attributes_for :ingredient
```

```
class Ingredient < ApplicationRecord
    has_many :cocktail_ingredients
    has_many :cocktails, through: :cocktail_ingredients
```

And my nested form looked like this:

```
<%= form_for @cocktail do |f| %>
    <div class="cocktail_name"> <%= f.label :name %> <%= f.text_field :name, :size=>"72" %> </div><br>
    <div class="cocktail_description"> <%= f.label :description %> <%= f.text_field :description, :size=>"68" %> </div><br>
    <div class="cocktails_ingredients"><%= f.label "Ingredients:" %><br><br>
        <%= f.fields_for :cocktail_ingredients do |ff| %>
            <div class="ingredient_fields">
                <%= ff.fields_for :ingredient do |fff| %>
                    <%= fff.label :name %>
                    <%= fff.text_field :name %>
                <% end %>
            <%= ff.label :quantity %> <%= ff.text_field :quantity %>
            <%= ff.select :unit, options_for_select(CocktailIngredient::UNITS, :selected => ff.object.unit) %> </div><br>
        <% end %>
    </div> <br>
    <div class="cocktail_instructions"> <%= f.label :instructions %> <%= f.text_field :instructions, :size=>"68" %> </div> <br>
  <%= f.submit "Submit" %>
<% end %> 
```

My goal was to build an application, that kept all the unique ingredients and added new ones on initialization. More than a week I spent on trying and failing, researching, reading an endless list of blogs and websites to build a perfect method of creating a new Cocktail.

```
   def prepare_cocktail
        @cocktail.cocktail_ingredients.each do |cocktail_ingredient|
            if ingredient = Ingredient.where('LOWER(name) = ?', cocktail_ingredient.ingredient.name.downcase).first
                cocktail_ingredient.ingredient_id = cocktail_ingredient.ingredient.id = ingredient.id 
            end
        end
    end
```

I was going to initialize a new Cocktail with 5 ingredients, and it seemed ok until I realized that not all the cocktails had five ingredients. 

```
            @cocktail = current_user.cocktails.new
            5.times do
                @cocktail.cocktail_ingredients.build.build_ingredient
            end
```

The main issue was that the method was breaking whenever there was an empty text field and I needed a way to change the number of ingredients dynamically. 

Google was screaming “use Cocoon!”, but JS error was against it. I spend another few days trying to fix the problem and playing with the Cocoon gem – nothing was working.

So, one day a silly idea came to my mind. I added a new view and a controller that was taking a necessary quantity of the ingredients for the Cocktail and saving it as a variable:

```
def i
            if params[:i]
                @@i = params[:i][:i].to_i
                if @@i == 0
                    flash[:message] = "Please enter a number greater than 0!"
                    render 'i'
                else
                    redirect_to cocktails_i_new_path
                end
            else
                render 'i'
            end
    end
```

It was a bridge to a new perfect cocktail. As you can see, I also had to change “cocktails#new” route to get it working.

People might call it cheating. I call it mother wit.

I agree, it is not perfect, but it is working. And I hope that my assessor will think the same way. Wish me luck! 

