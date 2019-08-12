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
    qs2 = qs.filter(published=True)
    L2 = list(qs2)

print(context.final_queries - context.initial_queries)
# 2
```

`.select_related(fk)` - does an inner join across the foreign key

```
with CaptureQueriesContext(connection) as context:
    for item in models.MainModel.objects.all():
        name = item.fk.name

print(context.final_queries - context.initial_queries)
# 6
```

```
with CaptureQueriesContext(connection) as context:
    for item in models.MainModel.objects.select_related('fk').all():
        name = item.fk.name

print(context.final_queries - context.initial_queries)
# 1
```

You can also join multiple tables deep by using Django's double-underscore syntax, for example `.select_related('foo__bar')` would join our main model's table with the table for `foo`, and then further join with the table for `bar`

if the relationship runs the other way, resulting in a `one-to-many` relationship, use `prefetch_related()` instead

