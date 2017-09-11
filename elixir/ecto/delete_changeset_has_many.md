## Action
```elixir
def delete(conn, %{"id" => id}) do

  # find record category
  record_category = Repo.get!(RecordCategory, id)
  
  # delete changeset
  changeset = RecordCategory.delete_changeset(record_category, %{})
  
  # try to delete
  case Repo.delete(changeset) do
  ...
  end
end
```

## Changeset
```elixir
  # schema with has_many
  schema "record_categories" do
  ...
  has_many :records, App.Record
  ...
  end
  
  def delete_changeset(struct, params \\ %{}) do
    struct
    |> cast(params, [...fields...])
    |> cast_assoc(:records)
    |> no_assoc_constraint(:records)
  end
```
