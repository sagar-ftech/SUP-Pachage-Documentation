[![License](https://poser.pugx.org/trexology/pointable/license)](https://packagist.org/packages/trexology/pointable)
# Reward Engine
Reward Engine Project to Generate Reward point based on certain events and perform transaction of them as digital currency.

This Package Generate Reward / Add Reward to the user Based on the Reward Classification group.
This Reward Classification stored into database.

##Installation
Require this package with composer.
This package is designer for Private users only so you have to
make changes into your composer file to location this packages
into shared private repository with you.

To do this you need to add below into your *composer.json* file.
```json
"repositories": [{
"type": "vcs",
"url": "https://github.com/usagar80/reward-engine"
}]
```
And then run Composer Require command
```shell
composer require sup/reward-engine
```
After package successful installation it will be automatically added to your
laravel service provider and alias will created as well.

You need to run migration to take Database table installation into your Database.

```shell
php artisan migrate
```

## Reward Classification
This module responsible for storing point whcih need to assign when activity occure.
i.e. User Register - give 50 Reward Point.

### Add new Reward Classifications
To add new reward Please run below artisan command and it will walk you through 
the steps.

```shell
php artisan classification:create
```

### List Reward Classifications
#### Using Artisan
To get list Reward Classification run below command which will output all available classifications.
```shell
php artisan classification:list
```
#### Using Programatically
To get avail transaction you can use below function
```php
// Get All Classification
$classifications = RewardEngine::Classifications();
// Get Specific Classification By Name
$classification = RewardEngine::Classifications('AccountVerified');
```
## Balance
To get user's balance you can use below fuction. The parameter shouild be the ID of user.
It will return 0 if no transaction performed for the user.
```php
// Auth::User()->id can be pass too.
RewardEngine::getBalance(1);
```
## Transaction
In this section all trasnsaction of Reward Related performed.

### Make Transaction (Using Listener)
You can perform transaction by attaching **ExecuteEventTransaction**
listner to your event in your app's event service Provider.

```php
protected $listen = [
    AccountVerified::class => [
        ExecuteEventTransaction::class,
    ],
];
```
You also need to implement **ShouldHandleRewardTransaction** 
into your event class and use trait **ConfigTransactional** and 
execute **init** method in to constructor.
```php
namespace App\Events;

use App\Models\User;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;
use SUP\RewardEngine\Events\ShouldHandleRewardTransaction;
use SUP\RewardEngine\Traits\ConfigTransactional;

class AccountVerified implements ShouldHandleRewardTransaction
{
    use Dispatchable, InteractsWithSockets, SerializesModels, ConfigTransactional;

    /**
     * Create a new event instance.
     *
     * @return void
     */
    public function __construct()
    {
        $this->init("AccountVerified", User::Find(1));
    }

    /**
     * Get the channels the event should broadcast on.
     *
     * @return \Illuminate\Broadcasting\Channel|array
     */
    public function broadcastOn()
    {
        return new PrivateChannel('channel-name');
    }
}
```

### Retrive Transactions
#### Retrive Transaction
To retrive all transaction or user's specific transaction you can use below methods.
```php
$transactionsAll = RewardEngine::getTransactions();
// Auth::user()->id can also pass
$transactions = RewardEngine::getTransactions(1);
```
#### Retrive Classification's Transaction
To Retrive all transaction of specific Classification
```php
$classification = RewardEngine::Classifications('AccountVerified');
$transactionOfClassification = $classification->transactions();
```
