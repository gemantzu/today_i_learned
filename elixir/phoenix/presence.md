## Generate Presence

```shell
mix phx.gen.presence
```

## Add Presence to Supervisors
```elixir
# lib/app/application.ex

def start(_type, _args) do
    children = [
      ...
      supervisor(AppWeb.Presence, []), #Added line
      ...
    ]
end
```

## Change Channel to use Presence

```elixir
# lib/app_web/channels/room_channel.ex
defmodule AppWeb.RoomChannel do
  use Phoenix.Channel
  alias AppWeb.Presence #Added alias
  alias App.Repo
  alias App.User # Or App.Coherence.User if using Coherence
  
  # use a callback right after joining channel
  def join("room", _payload, socket) do
    send(self, :after_join) #Added
    {:ok, socket}
  end
  
  # callback
  def handle_info(:after_join, socket) do
    # find User
    user = Repo.get(User, socket.assigns.user_id)
    # Track Presence
    {:ok, _} = Presence.track(socket, user.name, %{
      online_at: inspect(System.system_time(:seconds))
    })
    
    # push message to socket
    push socket, "presence_state", Presence.list(socket)
    
    {:noreply, socket}
  end
```

## Frontend
```javascript
// change import to include Presence
import {Socket, Presence} from "phoenix"

// initialize presences var
let presences = {}

// fetch user list element
let onlineUsers = document.getElementById("online-users")

// Helper function, plucks user from presence data
let listUsers = (user) => {
  return {
    user: user
  }
}

// creates list items that show present users
let renderUsers = (presences) => {
  onlineUsers.innerHTML = Presence.list(presences, listUsers)
  .map(presence => `<li>${presence.user}</li>`).join("")
}

// handle 'presence_state' message, render new user list
channel.on('presence_state', state => {
  presences = Presence.syncState(presences, state)
  renderUsers(presences)
});

// handle 'presence_diff' message (when something has changed), render new user list
channel.on('presence_diff', diff => {
  presences = Presence.syncDiff(presences, diff)
  renderUsers(presences)
});
```
