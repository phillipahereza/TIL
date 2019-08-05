## When QuerySets are evaluated

Internally, a QuerySet can be constructed, filtered, sliced, and generally passed around without actually hitting the database. No database activity actually occurs until you do something to evaluate the queryset.

Things that cause a Queryset to trigger a database call:
- Iteration `[model.name for mode in model.objects.all()]`
- Slicing `Entry.objects.all()[:5]`
- Pickling/Caching queries
- `len()` but it's more efficient to use `.count()`
- list `entry_list = list(Entry.objects.all())`


A QuerySet somehow reuses its previous data when we ask for it again
```
with CaptureQueriesContext(connection) as context:
    qs = Entry.objects.all()
    L = list(qs)
    L2 = list(qs)

print(context.final_queries - context.initial_queries)
# 1
```

But if for some reason the queryset is modified, then it makes another trip to get the data
```
with CaptureQueriesContext(connection) as context:
    qs = Entry.objects.all()
    L = list(qs)
    qs = Entry.objects.filter(published=True)
    L2 = list(qs)

print(context.final_queries - context.initial_queries)
# 2
```

