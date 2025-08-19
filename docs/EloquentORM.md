# Home
[<< Click here!](README.md)
## Create Records
### Create a New User
```php
$user = new User;
$user->name = 'John Doe';
$user->email = 'john@example.com';
$user->save();
```

### Create a New User with create Method (Mass Assignment)
```php
$user = User::create([
    'name' => 'Jane Doe',
    'email' => 'jane@example.com',
]);
```

## Update Records
### Update an Existing User
```php
$user = User::find(1);
$user->name = 'John Smith';
$user->save();
```

### Update Records Matching Conditions
```php
User::where('status', 'inactive')->update(['status' => 'active']);
```

## Deleting Records
### Delete a Specific User
```php
$user = User::find(1);
$user->delete();
```

### Delete Records Matching Conditions
```php
User::where('status', 'inactive')->delete();
```