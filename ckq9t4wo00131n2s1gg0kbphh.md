## Laravel Has Many Through Relationship Explained with Example

**Eloquent Relationships** is one of the most useful and powerful features of the Laravel Framework. It is one of the reasons why I like Laravel most. Mainly it helps us to fetch or insert data in a very easy and efficient way.

As we know there are several types of relationships in Laravel. We (developers) use the first four of the most. These are: **One To One**, **One To Many**, **One To Many (Inverse) / Belongs To**, **Many To Many**.

I like **Many To Many** and **Has Many Through** most. Honestly speaking I enjoy these two relations most. Today I will explain **Has Many Through** with an easy example. I hope you'll be very clear after finishing this article.

**So let's jump to our main section**.

Let us have a scenario where we are creating a restaurant's items/menu and ***Items*** belongs to the ***Type*** and ***Types*** belongs to ***Category***.

In simple words ***Category*** has many ***Types*** and ***Type*** has many ***Items***. Now if we want all the Items which belongs to the Category, we need to keep the `category_id` in ***items*** table. But out items mainly belongs to ***Type***. Basically ***Items*** are directly connected to ***Types***.

So it's the case where we should use ***Has Many Through***. By this kind of relation, we can fetch the data via another model. Like our scenario, we can directly fetch ***Items*** from ***Category*** via ***Types***.

For your better understand I have created a [GitHub Repository](https://github.com/mahmudulhsn/relationship-explained). You can visit this repository if you want. There I gave two types of examples for ***Has Many Through***.

I try to give a very simple example so that I can make you understand easily. So, Let's start.

As I mentioned earlier that we are work with ***Category***, ***Type***, and ***Item***.

So I make my migration very simple. 

> Here is my migration for ***Category***. Let's have a look at this.

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateCategoriesTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('categories', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('categories');
    }
}
``` 

You can see I kept only category name here. 

> Let's have look at my ***Category Model***.

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Category extends Model
{
    use HasFactory;

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name',
    ];
}
```

> Here is my types migration. Let's have a look.

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateTypesTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('types', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->unsignedInteger('category_id');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('types');
    }
}
```

I just kept `category_id` as foreign key. So ***Category*** is connected with ***Type***.

> Here is the ***Type*** model.

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Type extends Model
{
    use HasFactory;

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name', 'category_id'
    ];
}
```
You might find it very simple.

> Let's jump to my ***Items Migration***.

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateItemsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('items', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->longText('description');
            $table->unsignedInteger('type_id');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('items');
    }
}
```
So here you can see that I just kept `type_id` as a foreign key. So ***Type*** is connected with ***Item***. I didn't keep any `category_id` in ***Items Migration***. Here is the main fact you need to understand that ***Item*** is not directly connected with ***Category***. ***Item*** is connected with ***Category*** via ***Type***. So that's why it's called ***Has Many Through*** relationship.

> Now here is the model for **Item**.

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Item extends Model
{
    use HasFactory;

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name', 'description', 'type_id'
    ];
}
```

Now we will build a ***Has Many Through*** Relationship from **Category** to **Item**. 

> Let's create this relation to ***Category*** model.

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Category extends Model
{
    use HasFactory;

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name',
    ];

    /**
     * Get all of the items for the user.
     */
    public function items()
    {
        return $this->hasManyThrough(Item::class, Type::class);
    }
}
```

Here `items` is the relation that will fetch all the items that belongs to the category via type. 

> You can also use relation like this:
```php
 return $this->hasManyThrough(
        Item::class,
        Type::class,
        'category_id', // Foreign key on the types table...
        'type_id', // Foreign key on the items table...
        'id', // Local key on the users table...
        'id' // Local key on the categories table...
 );
```

There it goes. Our ***Has Many Through*** relationship has been build up successfully. 

I want to give you another scenario where you can use this relationship for your better understand. 

> Scenario

Suppose we have three models.

**Team** (id, user_id, name)
**User** (id, name)
**Goal** (id, user_id, no.of goals)
So, the relationship is like this.

Team hasMany **User** (`team_id` inside **User** model)

User hasMany **Goal** (`user_id` inside **Goal** model)

See, from the relationship, we can see that here, the User model is an intermediary model.

We can not store the `goal_id` directly to the Team table because we already store the `goal_id` in the User table.

So, now the User model is in a relationship with the Team model. So, inside the User model, there is `team_id`.

Finally, if we need to access how many goals the Team has created then, we can go through **User** model.

If that type of scenario is generated for your use case then and then you need to define ***Has Many Through*** relationship.

I hope it is clear now.

# Suggested Reads
* [Laravel Documentaiton](https://laravel.com/docs/8.x/eloquent-relationships#has-many-through)