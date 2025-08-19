# Home
[<< Click here!](README.md)
## Basic Select Queries
### Getting All Rows from a Table
```php
$users = DB::table('users')->get();
foreach ($users as $user) {
    echo $user->name;
}
```

### Getting a Single Row/Column
```php
$user = DB::table('users')->where('id', 1)->first();
echo $user->name;

$name = DB::table('users')->where('id', 1)->value('name');
echo $name;
```

### Getting a List of Column Values
```php
$titles = DB::table('posts')->pluck('title');
foreach ($titles as $title) {
    echo $title;
}
```

## Aggregates
### Counting Rows
```php
$userCount = DB::table('users')->count();
echo $userCount;
```

### Sum, Max, Min, Avg
```php
$priceSum = DB::table('orders')->sum('price');
$maxPrice = DB::table('orders')->max('price');
$minPrice = DB::table('orders')->min('price');
$avgPrice = DB::table('orders')->avg('price');
```

## Joins
### Inner Join
```php
$users = DB::table('users')
           ->join('posts', 'users.id', '=', 'posts.user_id')
           ->select('users.*', 'posts.title')
           ->get();
```

### Left Join
```php
$users = DB::table('users')
           ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
           ->select('users.*', 'posts.title')
           ->get();
```

## Where Clauses
### Basic Where
```php
$users = DB::table('users')->where('status', 'active')->get();
```

### Or Where
```php
$users = DB::table('users')
           ->where('status', 'active')
           ->orWhere('status', 'pending')
           ->get();
```

### Where Between
```php
$users = DB::table('users')
           ->whereBetween('age', [18, 25])
           ->get();
```

### Where In
```php
$users = DB::table('users')
           ->whereIn('id', [1, 2, 3])
           ->get();
```

### Where Null
```php
$users = DB::table('users')
           ->whereNull('email_verified_at')
           ->get();
```