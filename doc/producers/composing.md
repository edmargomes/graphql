# Composing data producers

Often you will find that you need to call multiple producers in a sequence in order to get the data you actually want. Maybe because you call a producer that only returns an `id` and then you need an `entity_load` producer to use that `id` to return the actual entity, or maybe a route that returns a URL Object and then you want to take that URL can get the entity out of it using the `route_entity` data producer.

This can be accomplished using some of the built-in helpers inside the `$builder` object called `compose`. given our example for the previous example `current_user`, this is how it works : 

```php
$registry->addFieldResolver('Query', 'currentUser', $builder->compose(
    $builder->produce('current_user'),
    $builder->produce('entity_load', [
        'mapping' => [
            'type' => $builder->fromValue('user'),
            'id' => $builder->fromParent(),
        ],
    ])
));
```

We are chaining the two data producers together here, one after the other and calling `fromParent` will give us the result that was returned in the previous step.

## Custom steps

What if we need to do some massaging but not necessarily using any data producer? The `$builder` object includes a callback property as well that we can use for this : 

```php
$registry->addFieldResolver('Query', 'currentUser', $builder->compose(
    $builder->produce('current_user'),
    $builder->produce('entity_load', [
        'mapping' => [
            'type' => $builder->fromValue('user'),
            'id' => $builder->fromParent(),
        ],
    ]),
    $builder->callback(function ($entity) {
        // Here we can do anything we want to the data. We get as a parameter anyting that was returned
        // in the previous step.
    })
));
```
