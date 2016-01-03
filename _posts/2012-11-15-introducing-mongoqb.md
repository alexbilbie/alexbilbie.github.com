---
layout: post
title: Introducing MongoQB
permalink: /2012/11/introducing-mongoqb/
tags: [hacks]
---

A few years ago I released a [MongoDB library](https://github.com/alexbilbie/codeigniter-mongodb-library) for the [CodeIgniter](http://codeigniter.com/) PHP framework which has become quite popular thanks to it's likeness to CodeIgniter's query builder library.

I've just spent the last week travelling and to keep myself occupied I spent some time re-architecting the library's code; removing the framework dependancy, making it [Composer](http://getcomposer.org/) friendly and adding a full suite of unit tests. Check out the code on Github - [https://github.com/alexbilbie/MongoQB](https://github.com/alexbilbie/MongoQB).

## Install via Packagist and Composer

Add the following into your composer.json file:

<pre><code data-language="javascript">{
	"require": {
		"alexbilbie/mongoqb": "*"
	}
}
</code></pre>

Then run

<pre><code data-language="shell">composer install
</code></pre>

## Install via Git

<pre><code data-language="shell">git clone git://git@github.com:alexbilbie/MongoQB
</code></pre>

## Download a zip/tarball

Download the latest version:

* [zip](https://github.com/alexbilbie/MongoQB/archive/master.zip)
* [tar](https://github.com/alexbilbie/MongoQB/archive/master.tar.gz)

(Note the zip/tarball won't include any unit tests or composer files)

## Unit tests

To run the unit test suite make sure you have MongoDB installed locally and running with no authentication and on the default port - 27017. The library currently has 100% unit test coverage.

Then run:

<pre><code data-language="shell">composer update --dev
cd vendor/alexbilbie/mongoqb
phpunit -c tests/phpunit.xml
</code></pre>

## Example usage

### Connect to the database

<pre><code data-language="php">$qb = \MongoQB\Builder(array(
	'dsn'	=>	'mongodb://user:pass@localhost:27017/databaseName'
);
</code></pre>

### Insert a document

<pre><code data-language="php">$qb->insert('collectionName', [
	'name'	=>	'Alex',
	'age'	=>	22,
	'likes'	=>	['whisky', 'gin']
]);
</code></pre>

### Update a single document

<pre><code data-language="php">$qb
	->where(['name' => 'Alex'])
	->set([
		'country' => 'UK',
		'job' => 'Developer'
	])
	->push('likes', ['PHP', 'coffee'])
	->update('collectionName');
</code></pre>

### Delete a single document

<pre><code data-language="php">$qb
	->where(['name' => 'Alex'])
	->delete('collectionName');
</code></pre>

### Search for matching documents

<pre><code data-language="php">$results = $qb
	->whereGt('age', 21)
	->whereIn('likes', ['whisky'])
	->where('country', 'UK')
	->get('collectionName');
</code></pre>

If you find any bugs please file a report in the [Issue tracker](https://github.com/alexbilbie/MongoQB/Issues)