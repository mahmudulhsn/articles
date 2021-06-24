## Use SweetAlert2 with Laravel Livewire

Recently I've been doing a lot of **Laravel Livewire** stuff and truth to be told, I'm falling in love with it.

One of the common things to do in a web application is showing toast notifications. In this article, I'll be sharing a strategy I've been following for a while now.

Let's jump to implementing **Sweet Alert 2** with **Laravel Livewire**.

> Step 1:

First of all, we need to install **Sweet Alert 2**. You can install it through `npm`.

```
npm install sweetalert2
```
Or you can use this through CDN. In this article, I am going to use with CDN.

```js
<script src="//cdn.jsdelivr.net/npm/sweetalert2@11"></script>
```
Put this CDN in your **Main Layout**. 

> Step 2: 

Now, If you use this in **Laravet Livewire**, you should have a **Livewire Component** and **Class**. So in that class, after doing an action you need to dispatch a browser event. Now put this code into your action.

```php
$this->dispatchBrowserEvent('swal', [
	'title' => 'Item has been removed.',
	'icon'=>'success',
	'iconColor'=>'blue',
]);
```
> Step 3:

Now you need to listen to this event so that it can fire an action to show the **Sweet Alert Message**. Now put this code to listen to the event which is fired from the **Component Class**.

```js
<script>
    window.addEventListener('swal',function(e) {
        Swal.fire({
            title:  e.detail.title,
            icon: e.detail.icon,
            iconColor: e.detail.iconColor,
            timer: 3000,
            toast: true,
            position: 'top-right',
            toast:  true,
            showConfirmButton:  false,
        });
    });
</script>
```

Here we go... We have done our job.

Here I am sharing a [GitHub Repository](https://github.com/mahmudulhsn/shopping-cart-with-laravel-livewire) for making you better understand how you can use it.

Here I used this code as `partials/_swal.blade.php`.
```js
<script>
    window.addEventListener('swal',function(e) {
        Swal.fire({
            title:  e.detail.title,
            icon: e.detail.icon,
            iconColor: e.detail.iconColor,
            timer: 3000,
            toast: true,
            position: 'top-right',
            toast:  true,
            showConfirmButton:  false,
        });
    });
</script>
```

Then I imported it in `layouts/app.blade.php`. You can easily understand it after seeing this repository.

## Suggested Reads
* [Sweet Alert 2 Documentaiton](https://sweetalert2.github.io/#download) 
* [Laravel Livewire Documentation](https://laravel-livewire.com/docs/2.x/events#firing-events)