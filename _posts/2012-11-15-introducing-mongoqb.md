---
layout: post
title: Introducing MongoQB
permalink: /2012/11/introducing-mongoqb/
---

A few years ago I released a [MongoDB library](https://github.com/alexbilbie/codeigniter-mongodb-library) for the [CodeIgniter](http://codeigniter.com/) PHP framework which has become quite popular thanks to it's likeness to CodeIgniter's query builder library.

I've just spent the last week travelling and to keep myself occupied I spent some time re-architecting the library's code; removing the framework dependancy, making it [Composer](http://getcomposer.org/) friendly and adding a full suite of unit tests. Check out the code on Github - [https://github.com/alexbilbie/MongoQB](https://github.com/alexbilbie/MongoQB).

## Install via Packagist and Composer

Add the following into your composer.json file:

{% highlight javascript %}
{
	"require": {
		"alexbilbie/mongoqb": "*"
	}
}
{% endhighlight %}

Then run

{% highlight bash %}
composer install
{% endhighlight %}

## Install via Git

{% highlight bash %}
git clone git://git@github.com:alexbilbie/MongoQB
{% endhighlight %}

## Download a zip/tarball

Download the latest version:

* [zip](https://github.com/alexbilbie/MongoQB/archive/master.zip)
* [tar](https://github.com/alexbilbie/MongoQB/archive/master.tar.gz)

(Note the zip/tarball won't include any unit tests or composer files)

## Unit tests

To run the unit test suite make sure you have MongoDB installed locally and running with no authentication and on the default port - 27017. The library currently has 100% unit test coverage.

Then run:

{% highlight bash %}
composer update --dev
cd vendor/alexbilbie/mongoqb
phpunit -c tests/phpunit.xml
{% endhighlight %}

## Example usage

### Connect to the database

{% highlight php %}
<?php
$qb = \MongoQB\Builder(array(
	'dsn'	=>	'mongodb://user:pass@localhost:27017/databaseName'
);
{% endhighlight %}

### Insert a document

{% highlight php %}
<?php
$qb->insert('collectionName', [
	'name'	=>	'Alex',
	'age'	=>	22,
	'likes'	=>	['whisky', 'gin']
]);
{% endhighlight %}

### Update a single document

{% highlight php %}
<?php
$qb
	->where(['name' => 'Alex'])
	->set([
		'country' => 'UK',
		'job' => 'Developer'
	])
	->push('likes', ['PHP', 'coffee'])
	->update('collectionName');
{% endhighlight %}

### Delete a single document

{% highlight php %}
<?php
$qb
	->where(['name' => 'Alex'])
	->delete('collectionName');
{% endhighlight %}

### Search for matching documents

{% highlight php %}
<?php
$results = $qb
	->whereGt('age', 21)
	->whereIn('likes', ['whisky'])
	->where('country', 'UK')
	->get('collectionName');
{% endhighlight %}

If you find any bugs please file a report in the [Issue tracker](https://github.com/alexbilbie/MongoQB/Issues)