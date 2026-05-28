List of files changed and respective changes:

db/migrate/20260420054941_add_done_to_todo.rb

```ruby
def change
  add_column :todos, :done, :boolean, default: false
end
```

app/controllers/todos_controller.rb

```ruby
def todo_params
    params.expect(todo: [ :description, :due_date, :done ])
end
```

app/views/todos/_form.html.erb

<div>
  <%= form.label :done %>
  <%= form.check_box :done %>
</div>


app/views/todos/show.html.erb

<p>
  <strong>Done:</strong>
  <%= @todo.done %>
</p>    


config/routes.rb

```ruby
get '/new_todo', to: 'todos#new'
```

config/routes.rb

```ruby
root "todos#index"
```

Deployed application link:
https://pacific-temple-49773-6ecce3bb11bc.herokuapp.com/
