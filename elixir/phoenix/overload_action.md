### Phoenix action overloading
By creating an `action/2` function in a controller, you can intercept all the actions in the controller and change their behavior. Here, we make all actions to be `/3` instead of `/2`, with the 3rd argument to be the `current_user` which has been set in Plug.Conn.

```elixir
def action(conn, _params) do
  apply(__MODULE__, action_name(conn),
    [conn, conn.params, conn.assigns.current_user])
  end
end
```

[Kernel.apply/3](https://hexdocs.pm/elixir/Kernel.html#apply/3)

```elixir
apply(module_name, action, params)
__MODULE__ # gets replaced by current module name on compile time
action_name(conn) # retrieves the action name from the current Plug.Conn
[conn, conn.params, conn.assigns.current_user] # new arguments, first is the Plug.Conn, second are the params as they were, third is the current user
```
