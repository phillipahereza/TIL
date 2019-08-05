## When QuerySets are evaluated

Internally, a QuerySet can be constructed, filtered, sliced, and generally passed around without actually hitting the database. No database activity actually occurs until you do something to evaluate the queryset.

Things that cause a Queryset to trigger a database call:
- Iteration `[model.name for mode in model.objects.all()]`
- Slicing `Entry.objects.all()[:5]`
- Pickling/Caching queries
- `len()` but it's more efficient to use `.count()`
- list `entry_list = list(Entry.objects.all())`
