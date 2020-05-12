---
layout: post
title:      "Ruby CLI Project. How I printed an array to columns."
date:       2020-05-12 01:51:17 +0000
permalink:  ruby_cli_project_how_i_printed_an_array_to_columns
---


Working on my Ruby CLI project, I came to the question “how do I print data in columns?”
A bit of background. In my project, I was scraping a web site and all collected data was storing in an array. Naturally, my first line of code for displaying it was:

```
array.each.with_index(1) do |item, index|
   puts “#{index}. #{item}.”
end
```

However, the printed data appeared to be a one-word item and did not look good as a list. Here I realized I needed to break it in a few columns.
I wanted to use the simplest way of displaying data and came to a solution that I could have an array of arrays (nested array), and print each small array as a new line. Therefore, the next step was to split my class array on a number of columns that I wanted to have:

```
array_2 = array.group _by,with_index do |item, index|
   index % (array.length / number_of_columns)
end
```

and to print it I used the code:

```
array_2.each do |row|
   row.each do |mini_array|
      print mini_array.to_s  + “ “ 
      #here I was combining all items of a “mini” array into a string dividing them with “ ”.
   end
   puts
end
```

It worked but did not look good. All words had different length and I could not get the second column aligned. In addition, it was essential to have the indexes of the items, as they were used for choosing further options. 
With the help of my cohort, I figured out that I could make all the items of my array to be the same length by adding spaces to the end of the words and saving them with their indexes as a string. For that reason, I had to add one more array before my previous code:

```
array_1 = []
array_2 = []

array.each.with_index(1) do |item, index|
   piece = (("#{index}. " + "#{item}").to_s + " " * 15)[0,17]
   #here I was adding 15 spaces to the end of each item(word) and cut it to a length of 17 symbols counting from the first (0). I played with the length of each item to have a pleasing spacing for displaying the data. 
	array_1 << piece
end

array_2 = array.group _by,with_index do |item, index|
   index % (array.length / 3)
end

array_2.each do |row|
   row.each do |mini_array|
      print mini_array.to_s  + “ “ 
   end
   puts
end
```
In the end, I decided to display the data in 3 columns and it looked satisfying.

PS. One of my group mates found another way of correcting the lengths of words, which also looked good:
```
array.each.with_index(1) do |text, index|
   array_1 << ("%3.3s" % "#{index}. " + "%12.10s" % "#{text}" + "     ").to_s
end
```
