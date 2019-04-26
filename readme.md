# Nova List Card

[![Latest Version on Github](https://img.shields.io/github/release/dillingham/nova-list-card.svg?style=flat-square)](https://packagist.org/packages/dillingham/nova-list-card)
[![Total Downloads](https://img.shields.io/packagist/dt/dillingham/nova-list-card.svg?style=flat-square)](https://packagist.org/packages/dillingham/nova-list-card)


Add a variety of lists to your dashboard

![nova-list-card](https://user-images.githubusercontent.com/29180903/56833461-88905e80-683c-11e9-8a04-e3a7ce8dc582.png)

### Install
```bash
composer require dillingham/nova-list-card
```

### Basic Usage Example
Set the resource for the card
```php
(new \NovaListCard\ListCard)
    ->resource(\App\Nova\Post::class)
    ->heading('Recent Posts')
    ->subtitle('email')
    ->timestamp()
    ->viewAll()
```

### Possible Scenarios
- Latest resource / Oldest resource
- Upcoming - latest & custom timestamp 
- Past due - oldest & custom timestamp 
- Top resource by relationship count
- Worst resource by relationship count
- Top resource by relationship sum
- Worst resource by relationship sum

### Available Methods

| Method | Description |
| - | - |
| resource() | declare the resource |
| heading() | add a title to card |
| subtitle() | display subtitle value |
| timestamp() | display & format timestamp |
| value() | display right side value |
| withCount() | aggregate count value |
| withSum() | aggregate sum value |
| orderBy() | set the resource order |
| limit() | set number of resources |
| viewAll() | enable view all link |
| viewAllLens() | enable lens view all |
| zebra() | add alternate row color |
| id() | unique id for card's requests |
| classes() | add css classes to card |



**Multiple Headings**

When adding a row value, add second heading
```php
->heading('Top Bloggers', '# Posts')
```

**Resource Subtitle**

Display resource [subtitle](https://nova.laravel.com/docs/2.0/search/global-search.html#subtitles) beneath the title
```php
(new ListCard)
    ->resource(User::class)
    ->subtitle(),
```
Display resource proporties beneath the title
```php
(new ListCard)
    ->resource(User::class)
    ->subtitle('city'),
```

**Show View All Link**

You can link to the resource's index
```php
->resource(User::class)
->viewAll()
```
Or to a lens attached to the resource
```php
->resource(User::class)
->viewAllLens('most-popular-users')
```



**Side Values**

Display resource values on the right side
```php
(new ListCard)
    ->resource(User::class)
    ->value('city'),
```

**Aggregated Values**

Add counts of relationships:
```php
(new ListCard)
    ->resource(Category::class)
    ->withCount('posts')
    ->value('category_posts'),
```
Add sum of relationship column:
```php
(new ListCard)
    ->resource(User::class)
    ->withSum('orders', 'total')
    ->value('orders_sum'),
```
**Value formatting**

You can change the value output using [numeral.js](http://numeraljs.com/#format)

Format: 55.2k insread of 55200
```php
-value('orders_sum') // 55200
-value('orders_sum', '0.0a') // 55.2k
-value('orders_sum', '($ 0.00 a)') // $55.2k
```
Timestamp: add third parameter
```php
->value('created_at', 'mm/dd'', 'timestamp')
```

**Limit**

Set the number of items to display | default: 5:
```php
->limit(3)
```

**OrderBy**

Set the order of the resources:
```php
->orderBy('scheduled_at', 'desc')
```
Can also order by aggregated columns
```php
->orderBy('orders_sum', 'desc')
```

**Timestamps**

Defaults are created_at & moment.js format: MM/DD/YYYY:
```php
(new ListCard)->timestamp(),
(new ListCard)->timestamp('due_at'),
(new ListCard)->timestamp('completed_at', 'MM/DD'),
```
Relative timestamps: `5 days ago` | `in 5 days`
```php
(new ListCard)->timestamp('completed_at', 'relative'),
```

**Scoped Resource**

Pass an ID to the ListCard and check within [IndexQuery](https://nova.laravel.com/docs/2.0/resources/authorization.html#index-filtering):
```php
(new ListCard)->id('upcoming-tasks')
```
```php
public static function indexQuery($request, $query)
{
    if($request->input('nova-list-card') == 'upcoming-tasks') {
        $query->whereNull('completed_at');
    }

    return $query;
}
```

**CSS Classes**

Customize styles easily if you have your own theme.css
```css
.nova-list-card {}
.nova-list-card-heading {}
.nova-list-card-body {}
.nova-list-card-item {}
.nova-list-card-title {}
.nova-list-card-subtitle {}
.nova-list-card-timestamp {}
.nova-list-card-value {}
.nova-list-card-view-all {}
```
Also includes resource specific classes etc
```css
.nova-list-card.users.most-tasks
```
Also can target specific rows
```css
.nova-list-card-item-1 {
    color: green;
}
.nova-list-card-item-2 {}
.nova-list-card-item-3 {}
```
If you pass an ID with `->id('upcoming-tasks)`
```css
#upcoming-tasks {}
```
You can also add classes manually
```php
->classes('font-bold text-red some-custom-class')
```
You can also add alternate row formatting
```php
->zebra()
```

# Examples

```php
(new ListCard())
    ->resource(Contact::class)
    ->heading('Recent Contacts')
    ->subtitle('email')
    ->timestamp()
    ->limit(3)
    ->viewAll(),

(new ListCard())
    ->resource(Contact::class)
    ->heading('Contacts: Most tasks', 'Tasks')
    ->orderBy('tasks_count', 'desc')
    ->subtitle('email')
    ->value('tasks_count')
    ->withCount('tasks')
    ->zebra()
    ->viewAll(),

(new ListCard())
    ->resource(Contact::class)
    ->heading('Top Opportunities', 'Estimates')
    ->withSum('opportunities', 'estimate')
    ->value('opportunities_sum', '0.0a')
    ->viewAllLens('top-opportunities')
    ->orderBy('opportunities_sum', 'desc'),
```
