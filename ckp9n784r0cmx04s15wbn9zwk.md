## Update existing table's column with migration without losing data in Laravel

Before going to start our coding we need to know about [Laravel Migration](https://laravel.com/docs/8.x/migrations) and how it helps us.

First of all, if we want to build a project. We need to design our database first. So what can we do? We can design our database directly. If you miss some field/column or some datatype, you need to fix it directly into the database which is a bad practice in Laravel.

In another scenario, Suppose you have a project and it needs to set up to your friend's device, you might need to send the SQL file; which is painful.

Now think if you have the feature like you pull the project and run a command, Boom your database is being set up. Isn't it very helpful?

Laravel migration does the same. You don't need to take the pain of SQL file or if you miss a data type or any spelling mistake, you can fix it through your coding. You need not work directly with the database. You will fix it to your code and just run **php artisan migrate:refresh**. So hopefully you got the benefit of Laravel migration. 

**Now let's jump to another scenario.**

Sometimes we are much worried when we need to add or modify our existing database table. 

For example, we have a User Model and users migration table and it contains **name, username, email_verified_at password**. 
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateUsersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->string('name');
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->rememberToken();
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
        Schema::dropIfExists('users');
    }
}

```


But after deploying our project and after having our many users we found, we need to add a new field in our table like **phone_number**. Also mentioned, we need to keep our existing data.


So, we will create a new migration file for adding a new column in our existing table.

Now use this command:

`php artisan make:migration add_phone_number_to_users_table`

Here we need to ensure that the table's name needs to match the new migration file like we use **users** in both places.

After using the command we can see a new migration file like this: 

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class AddPhoneNumberToUsersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('users', function (Blueprint $table) {
            //
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('users', function (Blueprint $table) {
            //
        });
    }
}
```

Now you can add your desire field in this migration file. As we need a **phone_number** field.

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class AddPhoneNumberToUsersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->string('phone_number')->unique()->after('email');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('users', function (Blueprint $table) {
            //
        });
    }
}
```

Here we added **phone_number** field after **email** in **up()** method. You have the flexibility to put that column where ever you want by using `after()` or `before()`. You can learn more modifier from [Laravel Documentation Column Modifier](https://laravel.com/docs/8.x/migrations#column-modifiers) 

**Now we are one step behind from our job done.**

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class AddPhoneNumberToUsersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->string('phone_number')->unique()->after('email');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->dropColumn('phone_number');
        });
    }
}
```
Here you can see that we are adding `$table->dropColumn('phone_number');` so that if we run the **rollback** command and it will work properly. If you have many filed you can use array of dropcolumn like this `$table->dropColumn(['phone_number']);`

And finally, here we go...

Now you need to run this command:

`php artisan migrate`

And we have done our job. :D 

You can visit [Laravel Migration Documentation](https://laravel.com/docs/8.x/migrations) for more migration options.

Hope it will help you.
