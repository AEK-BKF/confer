# Confer
Add a real-time chat system to your laravel 5 website/application in a few lines of code

Recently I have had a few projects that have required a chat feature, and I wanted to create a laravel package - so here it is!

# Requirements
The project currently requires Pusher (php-server and javascript) to allow real-time chat messaging. I really recommend this service if you need to do anything real-time - it's fast, reliable and very easy to implement in your projects.

You can create a free sandbox account at [pusher.com](https://www.pusher.com) which lets you have 100,000 messages a day and 20 active users at one time. If you need higher limits they offer paid accounts at pretty decent prices.

Other requirements:

 * moment.js (it made me sad to have to require this, but it makes updating the chat timestamps so much easier)
 * jQuery

# Installation

Require the package via composer:
`composer require djb/confer`

Publish the assets:
`php artisan vendor:publish`

Add the seed to your database seed caller (default is `database\seeds\DatabaseSeeder.php`):

```php
class DatabaseSeeder extends Seeder {

/**
 * Run the database seeds.
 *
 * @return void
 */
public function run()
{
  Model::unguard();

  $this->call('ConferSeeder');
}
```

Migrate your database with the seeds in tow:
`php artisan migrate --seed`

Add the viewcomposers to your existing `ComposerServiceProvider.php` or create one (there is a great guide you can follow on [laravel's website](http://laravel.com/docs/5.0/views#view-composers)):

```php
class ComposerServiceProvider extends ServiceProvider {

/**
 * Register bindings in the container.
 *
 * @return void
 */
public function boot()
{
  View::composer('confer::confer', 'DJB\Confer\Http\ViewComposers\ConferComposer');
  View::composer('confer::barconversationlist', 'DJB\Confer\Http\ViewComposers\ConferBarComposer');
}
```

Add the trait to your User model:

```php
use DJB\Confer\Traits\CanConfer;

class User extends Model {

  use CanConfer;

}
```

Link to the css file, and import the view partials in whichever pages you wish to have the chat on, or put it in your app/master file (if you are using one) to show on all pages:

```html
<link href="/vendor/confer/css/confer.css" rel="stylesheet">
@include('confer::confer')

<script src="/js/pusher.min.js"></script>
<script src="/js/moment.min.js"></script>
@include('confer::js')</pre></div>
```

# Configuration
There are a number of options in the confer.php config file which are quite self explanatory, but in short you can:

 * Provide a company avatar (this is the image that will show as your global chat icon should you...)
 * Allow global chat - have a free-for-all open chat, or don't... it's up to you
 * Specify a different loader to the one used (default is a sweet looking .svg by [Sam Herbert](http://samherbert.net/svg-loaders/)
 * Change the directory where avatars are stored
 * Enable some serious grammar enforcing (currently capitals at start of sentences, and refusal to allow the use of numbers between 0-9 without converting them to their word format)

The avatar, loader and company avatar are all relative to your app's /public dir.

Your Pusher app details are not configured in the config file provided, they are instead expected to be provided in your `config\services.php` file in the format:

```php
'pusher' => [
  'public' => 'public_key',
  'secret' => 'secret_key',
  'app_id' => 'app_id'
]
```

# Assumptions of the package

The package assumes you have a User model in the App namespace, and that this model has a `name` attribute (hey, if you don't have one already, why not create one with a custom getter?) and an `avatar` attribute - which is simply the filename of the avatar image file (for example `avatar-dan.jpg`) which will be appended to your avatar_dir provided in the config file of the package to find your avatar.

# Potential updates

Likely updates include adding mentions, sounds and changing conversation names after the initial setup.

What would you like to see?

# Closing

If you use this package in your project it would mean the absolute world to me if you let me know! This is my first package, and my first piece of code shared so really... it's close to me.
That said please feel free to contribute to the project - I think it has a solid foundation for expansion.
