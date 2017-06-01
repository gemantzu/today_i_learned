## Ecto query to select all the fields from a table distinct by a field
```elixir
from q in query, distinct: q.phone, select: q
```
