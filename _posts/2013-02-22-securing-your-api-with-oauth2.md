---
title: Securing your API with OAuth 2.0
permalink: /2013/02/securing-your-api-with-oauth-2/
layout: post
published: true
date: "2013-02-22 12:04:00"
---

In [a previous post](http://alexbilbie.com/2013/02/php-meet-oauth-2/) I announced my new OAuth 2.0 PHP libraries.

In this post I will show you how to use the server library to secure a simple API with OAuth 2.0.

---

## Install the library

The recommended way of installing the library is via [Composer](http://getcomposer.org).

If you already have a composer.json file in your root then add `”lncd/oauth2”: “*”` in the _require_ object. Then run `composer update`.

Otherwise create a new file in your project root called _composer.json_ add set the contents to:

<pre><code data-language="javascript">{
    "require": {
        "lncd/OAuth2": "*"
    }
}
</code></pre>

Now, assuming you have installed Composer run `composer install`.

Ensure now that you’ve set up your project to autoload composer packages.

You could alternatively add the library as a git submodule or download a [zip](https://nodeload.github.com/lncd/OAuth2/zip/master).

## Set up the database

To setup the database just import _sql/mysql.sql_

## Create the storage models

In order to retrieve data from the database you should create classes which implement the following interfaces:

* `\OAuth2\Storage\ScopeInterface`
* `\OAuth2\Storage\SessionInterface`

## Hooking it all up

Setting up the library is simple, just create a new instance of `\OAuth2\ResourceServer` and pass in your storage models.

<pre><code data-language="php">// Include the storage models
include 'model_scope.php';
include 'model_session.php';

// Initiate the Request handler
$request = new \OAuth2\Util\Request();

// Initiate the auth server with the models
$server = new \OAuth2\ResourceServer(new SessionModel, new ScopeModel);
</code></pre>

##  Checking for valid access tokens

Before your API responds you need to check that an access token has been presented with the request (either in the query string `?access_token=abcdef` or as an authorization header `Authorization: bearer abcdef`).

If you’re using a framework such as Laravel or CodeIgniter you could use a route filter to do this, or have a custom controller which other controllers extend from. In this example I’m using the Slim framework and I’m going to create a simple [route middleware](http://docs.slimframework.com/pages/routing-middleware/) which is run before each endpoint function.

<pre><code data-language="php">$checkToken = function () use ($server) {

    return function() use ($server)
    {
        // Test for token existance and validity
        try {
            $server->isValid();
        }

        // The access token is missing or invalid...
        catch (\OAuth2\Exception\InvalidAccessTokenException $e)
        {
            $app = \Slim\Slim::getInstance();
            $res = $app->response();
            $res['Content-Type'] = 'application/json';
            $res->status(403);

            $res->body(json_encode(array(
                'error' =>  $e->getMessage()
            )));
        }
    };

};
</code></pre>

When `$server->isValid()` is called the library will run the following tasks:

* Check if an access token is present in the query string
    * If not, check if a base64 encoded access token is contained in an authorization header.
        * If not, throw `\OAuth2\Exception\InvalidAccessTokenException`
* Check if the access token is valid with the database
    * If not, throw `\OAuth2\Exception\InvalidAccessTokenException`
* If the access token is valid:
    * Get the owner type (e.g. “user” or “client”) and their ID
    * Get a list of any scopes that are associated with the access token

Assuming an exception isn’t thrown you can then use the following functions in your API code:

* `getOwnerType()` - This will return the type of the owner of the access token. For example if a user has authorized another client to use their resources the owner type would be “user”.
* `getOwnerId()` - This will return the ID of the access token owner. You can use this to check if the owner has permission to do take some sort of action (such as retrieve a document or upload a file to a folder).
* `hasScope()` - You can use this function to see if a specific scope (or several scopes) has been associated with the access token. You can use this to limit the contents of an API response or prevent access to an API endpoint without the correct scope.

## A simple example

This example endpoint will return a user’s information if a valid access token is present. If the access token has the `user.contact` it will return additional information.

<pre><code data-language="php">$app->get('/user/:id', $checkToken(), function ($id) use ($server, $app) {

    $user_model = new UserModel();

    $user = $user_model->getUser($id);

    if ( ! $user)
    {
        $res = $app->response();
        $res->status(404);
        $res['Content-Type'] = 'application/json';
        $res->body(json_encode(array(
            'error' => 'User not found'
        )));
    }

    else
    {
        // Basic response
        $response = array(
            'error' => null,
            'result'    =>  array(
                'user_id'   =>  $user['id'],
                'firstname' =>  $user['firstname'],
                'lastname'  =>  $user['lastname']
            )
        );

        // If the acess token has the "user.contact" access token include
        //  an email address and phone numner
        if ($server->hasScope('user.contact'))
        {
            $response['result']['email'] = $user['email'];
            $response['result']['phone'] = $user['phone'];
        }

        // Respond
        $res = $app->response();
        $res['Content-Type'] = 'application/json';

        $res->body(json_encode($response));
    }

});
</code></pre>

## Limiting an endpoint to a specific owner type

In this example, only a user’s access token is valid:

<pre><code data-language="php">$app->get('/user', $checkToken(), function () use ($server, $app) {

    $user_model = new UserModel();

    // Check the access token's owner is a user
    if ($server->getOwnerType() === 'user')
    {
        // Get the access token owner's ID
        $userId = $server->getOwnerId();

        $user = $user_model->getUser($userId);

        // If the user can't be found return 404
        if ( ! $user)
        {
            $res = $app->response();
            $res->status(404);
            $res['Content-Type'] = 'application/json';
            $res->body(json_encode(array(
                'error' => 'Resource owner not found'
            )));
        }

        // A user has been found
        else
        {
            // Basic response
            $response = array(
                'error' => null,
                'result'    =>  array(
                    'user_id'   =>  $user['id'],
                    'firstname' =>  $user['firstname'],
                    'lastname'  =>  $user['lastname']
                )
            );

            // If the acess token has the "user.contact" access token include
            //  an email address and phone numner
            if ($server->hasScope('user.contact'))
            {
                $response['result']['email'] = $user['email'];
                $response['result']['phone'] = $user['phone'];
            }

            // Respond
            $res = $app->response();
            $res['Content-Type'] = 'application/json';

            $res->body(json_encode($response));
        }
    }

    // The access token isn't owned by a user
    else
    {
        $res = $app->response();
        $res->status(403);
        $res['Content-Type'] = 'application/json';
        $res->body(json_encode(array(
            'error' => 'Only access tokens representing users can use this endpoint'
        )));
    }

});
</code></pre>

You might use an API function like this to allow a client to discover who a user is after they’ve signed into your authorization endpoint ([see an example of how to do this here](http://alexbilbie.com/2013/02/developing-an-oauth2-authorization-server/)).

## Limiting an endpoint to a specific owner type and scope

In this example, the endpoint will only respond to access tokens that are owner by client applications and that have the scope `users.list`.

<pre><code data-language="php">$app->get('/users', $checkToken(), function () use ($server, $app) {

    $user_model = new UserModel();

    $users = $user_model->getUsers();

    // Check the access token owner is a client
    if ($server->getOwnerType() === 'client' && $server->hasScope('users.list'))
    {
        $response = array(
            'error' => null,
            'results'   =>  array()
        );

        $i = 0;
        foreach ($users as $k => $v)
        {
            // Basic details
            $response['results'][$i]['user_id'] = $v['id'];
            $response['results'][$i]['firstname'] = $v['firstname'];
            $response['results'][$i]['lastname'] = $v['lastname'];

            // Include additional details with the right scope
            if ($server->hasScope('user.contact'))
            {
                $response['results'][$i]['email'] = $v['email'];
                $response['results'][$i]['phone'] = $v['phone'];
            }

            $i++;
        }

        $res = $app->response();
        $res['Content-Type'] = 'application/json';

        $res->body(json_encode($response));
    }

    // Access token owner isn't a client or doesn't have the correct scope
    else
    {
        $res = $app->response();
        $res->status(403);
        $res['Content-Type'] = 'application/json';
        $res->body(json_encode(array(
            'error' => 'Only access tokens representing clients can use this endpoint'
        )));
    }

});
</code></pre>

You might secure an endpoint in this way to only allow specific clients (such as your applications’ main website) access to private APIs.

---

Hopefully you will see how easy it is to secure an API with OAuth 2.0 and how you can use scopes to limit response contents or access to endpoints.

You can download a complete working example here - [https://github.com/lncd/oauth2-example-resource-server](https://github.com/lncd/oauth2-example-resource-server).