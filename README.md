# Relation On Pivot Table

## ER model

```mermaid
erDiagram
    USER ||--o{ POST : has
    USER ||--o{ COMMENT : has
    POST ||--o{ COMMENT : has
    COMMENT ||--o{ EMOJI : has

    USER {
        column_name migration_function "modifier"
        id id PK
        name string
        email string "unique"
        email_verified_at timestamp "nullable"
        password string
        remember_token rememberToken
        created_at timestamp
        updated_at timestamp
    }

    POST {
        column_name migration_function "modifier"
        id id PK
        author_id foreignId FK "constrained:users"
        content text
        created_at timestamp
        updated-at timestamp
    }

    COMMENT {
        column_name migration_function "modifier"
        id id PK
        post_id foreignId FK "constrained"
        user_id foreignId FK "constrained"
        created_at timestamp
        updated_at timestamp
    }

    EMOJI {
        id id PK
        comment_id foreignId FK "constrained"
        type string
        created_at timestamp
        updated_at timestamp
    }
```

## Setup Project

1. Set DB_DATABASE

```shell
cp .env .env.example
```

2. Generate app key

```shell
php artisan key:generate
```

3. Install dependencies

```shell
composer install

npm install
```

4. Migrate database tables

```shell
php artisan migrate
```

## Demo commands

```shell
php artisan tinker

$user = User::factory()->has(Post::factory())->create()
$post = $user->posts()->first()
$user->commentedPosts()->attach($post, ['comment' => 'test'])
$comment = $user->commentedPosts()->first()->pivot
$comment->emojis()->create(['type' => 'like']);

# Load data
$users = User::with('commentedPosts')->get()
$users = $users->map(function ($user, $key) {
    $user->commentedPosts = $user->commentedPosts->map(function ($post, $key) {
        return $post->pivot->load('emojis');
    });

    return $user;
})

# Show results
$users
```
