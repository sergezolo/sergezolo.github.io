---
layout: post
title:      "Sinatra Project. Updating data in the secondary table. "
date:       2020-07-21 00:47:17 +0000
permalink:  sinatra_project_updating_data_in_the_secondary_table
---


Working on my Sinatra project, I faced a puzzle I could not solve by myself, and that is why I decided to share it in my Blog.

I had Users in my app, who set their Goals and had many of them. Goals, at the same time, had many Tasks and, by completing one task, the User got closer to the achievement of the Goal. The objective was straightforward; I needed three models along with the namesake tables.
Creating tasks was not a problem:

```
params[:tasks].each do |task|
@goal.tasks.create(task: task["task"])
end
```

Later, when I started working on edit action and was trying to update my tasks, the block was replacing all tasks to the last line of the list. 

```
@goal.tasks.each do |task|
task.update(task: params[:tasks][]["task"])
end
```

I was losing all my great ideas on how to “conquer the World” or “become the best fisherman” and was getting five the same lines. That was upsetting, but there was a very simple solution: the index! Just a small thing made my day brighter!  

```
@goal.tasks.each_with_index do |task, index|
task.update(task: params[:tasks][index]["task"])
end
```

..and voila! It worked!

Furthermore, I faced another issue I was not able to resolve in this project. The tasks were checkboxes. After completing, a task or maybe several, Users could check them and the Goal would show the percentage of completion. How useful I thought!  However, I was able to make it work only in one way. Users could check the finished tasks, but were not able to uncheck them back. That is why I am looking forward to learning more to be able to solve issues like that.  



