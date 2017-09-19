## Create Room Channel
```elixir
# lib/app_web/channels/room_channel.ex
defmodule AppWeb.RoomChannel do
  use Phoenix.Channel
  def join("room", _payload, socket) do
    {:ok, socket}
  end
end
```

## Whitelist RoomChannel
```elixir
# lib/app_web/channels/user_socket.ex
defmodule AppWeb.UserSocket do 
  use Phoenix.Socket

  channel "room", AppWeb.RoomChannel
end
```

## App.js Socket

```javascript
import socket from './socket'
```

## Socket.js

```javascript
let channel = socket.channel("room", {})
```
**TODO:** Are all changes supposed to be after `socket.connect()` and before `channel.join()`?

### Handle events and push messages on channel (`socket.js`)

Here, we have to state an event that when it happens, we push a message on channel which we will catch on backend.
For example, if we have a `<input type="text" id="message-input" />` and we want to send the text it has 
when enter key is pressed (instead of clicking on a button) we do the following:

```javascript
let messageInput = document.getElementById('message-input')
messageInput.on('keypress', event => {
  if(event.keyCode == 13) { // check if key pressed is 'enter'
    channel.push('message:new', {message: messageInput.val()}) // Here we push the message to channel
    messageInput.val("")
  }
})
```

### Waiting for a message from backend (`socket.js`)

```javascript
channel.on('message:new', payload => {
  // do stuff on html
})
```

### Catch message on the room_channel (`lib/app_web/channels/room_channel.ex`)

```elixir
# We do a handle_in since we do not want to wait for this to respond back synchronously
# with broadcast!/3, we send a message back to the frontend
def handle_in("message:new", payload, socket) do
  broadcast! socket, "message:new", %{message: payload["message"]}
  {:noreply, socket}
end
```
