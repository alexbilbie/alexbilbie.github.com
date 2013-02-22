---
title: Developing an OAuth 2.0 authorization server
permalink: /2013/02/developing-an-oauth2-authorization-server/
layout: default
published: true
date: "2013-02-22 09:00:00"
---

In my previous post I announced my new OAuth 2.0 PHP libraries. This is the first in a number of posts that will show you how to use it.

---

This guide will show you how to setup a basic OAuth 2.0 authorization server which supports the  authorization code grant type.

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

---

You could alternatively add the library as a git submodule or download a [zip](https://nodeload.github.com/lncd/OAuth2/zip/master).

## Set up the database

To setup the database just import _sql/mysql.sql_

## Create your first client

In OAuth terms a _client_ is an application (it could be a website or a mobile app) that communicates with your API.

Insert a client into the `oauth_clients` table. It is recommended that you make the `id` and `secret` fields random alphanumeric strings - [http://randomkeygen.com/](http://randomkeygen.com/) is a useful for this. The `auto_approve` parameter should be to _1_ if you want the user to automatically approve access to the client, otherwise set it to _0_.

If you want to use the _authorization grant_ (where a user is redirected to the auth server from the client and the back in order to “sign-in” or “connect” with the client) then in the `oauth_client_endpoints` add a redirect URI (where the user is redirected back to after signing in). You can add multiple redirect URIs for production and development.

## Create the storage models

In order to persist data to the database you should create classes which implement the following three interfaces:

* `\OAuth2\Storage\ClientInterface`
* `\OAuth2\Storage\ScopeInterface`
* `\OAuth2\Storage\SessionInterface`

## The authorization code grant

For reference here is an overview of how the authorization code grant works:


    +--------+                               +---------------+
    |        |--(A)- authorisation Request ->|   Resource    |
    |        |                               |     Owner     |
    |        |<-(B)-- authorisation Grant ---|               |
    |        |                               +---------------+
    |        |
    |        |                               +---------------+
    |        |--(C)-- authorisation Grant -->| authorisation |
    | Client |                               |     Server    |
    |        |<-(D)----- Access Token -------|               |
    |        |                               +---------------+
    |        |
    |        |                               +---------------+
    |        |--(E)----- Access Token ------>|    Resource   |
    |        |                               |     Server    |
    |        |<-(F)--- Protected Resource ---|               |
    +--------+                               +---------------+


**(A)**  The client requests authorisation from the resource owner.  The authorisation request can be made directly to the resource owner (as shown), or preferably indirectly via the authorisation server as an intermediary.

**(B)**  The client receives an authorisation grant, which is a credential representing the resource owner's authorisation, expressed using one of four grant types defined in this specification or using an extension grant type.  The authorisation grant type depends on the method used by the client to request authorisation and the types supported by the authorisation server.

**(C)**  The client requests an access token by authenticating with the authorisation server and presenting the authorisation grant.

**(D)**  The authorisation server authenticates the client and validates the authorisation grant, and if valid issues an access token.

**(E)**  The client requests the protected resource from the resource server and authenticates by presenting the access token.

**(F)**  The resource server validates the access token, and if valid, serves the request.

## Create an _oauth_ controller

_NOTE: This is assuming you’re using a framework that follows an MVC pattern, If you’re using individual files for each page then you create a new page for each controller route listed henceforth._

In your controller constuctor you should instantiate the auth server:

<pre><code data-language="php">public function __construct()
{
    // Initiate the request handler which deals with $_GET, $_POST, etc
    $request = new \OAuth2\Util\Request();

    // Create the auth server, the three parameters passed are references
    //  to the storage models
    $this->authserver = new \OAuth2\AuthServer(
        new ClientModel,
        new SessionModel,
        new ScopeModel
    );

    // Enable the authorization code grant type
    $this->authserver->addGrantType(new \OAuth2\Grant\AuthCode());

    // Set the TTL of an access token in seconds (default to 3600s / 1 hour)
    $this->authserver->setExpiresIn(86400);
}
</code></pre>

Create your first route (for example “index” - which would resolve to _/oauth_).

<pre><code data-language="php">public function action_index()
{
    try {

        // Tell the auth server to check the required parameters are in the
        //  query string
        $params = $this->authserver->checkAuthoriseParams();

        // Save the verified parameters to the user's session
        Session::put('client_id', $params['client_id']);
        Session::put('client_details', $params['client_details']);
        Session::put('redirect_uri', $params['redirect_uri']);
        Session::put('response_type', $params['response_type']);
        Session::put('scopes', $params['scopes']);

        // Redirect the user to the sign-in route
        return Redirect::to(‘oauth/signin');

    } catch (Oauth2\Exception\ClientException $e) {

        // Throw an error here which says what the problem is with the
        //  auth params

    } catch (Exception $e) {

        // Throw an error here which has caught a non-library specific error

    }
}
</code></pre>

Next create a sign-in route:

<pre><code data-language="php">public function action_signin()
{
    // Retrieve the auth params from the user's session
    $params['client_id'] = Session::get('client_id');
    $params['client_details'] = Session::get('client_details');
    $params['redirect_uri'] = Session::get('redirect_uri');
    $params['response_type'] = Session::get('response_type');
    $params['scopes'] = Session::get('scopes');

    // Check that the auth params are all present
    foreach ($params as $key=>$value) {
        if ($value === null) {
            // Throw an error because an auth param is missing - don't
            //  continue any further
        }
    }

    // Process the sign-in form submission
    if (Input::get('signin') !== null) {
        try {

            // Get username
            $u = Input::get('username');
            if ($u === null || trim($u) === '') {
                throw new Exception('please enter your username.');
            }

            // Get password
            $p = Input::get('password');
            if ($p === null || trim($p) === '') {
                throw new Exception('please enter your password.');
            }

            // Verify the user's username and password
            // Set the user's ID to a session

        } catch (Exception $e) {
            $params['error_message'] = $e->getMessage();
        }
    }

    // Get the user's ID from their session
    $params['user_id'] = Session::get('user_id');

    // User is signed in
    if ($params['user_id'] !== null) {

        // Redirect the user to /oauth/authorise route
        return Redirect::to('oauth/authorise');

    }

    // User is not signed in, show the sign-in form
    else {
        return View::make('oauth.signin', $params);
    }
}
</code></pre>

In the sign-in form HTML page you should tell the user the name of the client that their signing into.

Once the user has signed in (if they didn’t already have an existing session) then they should be redirected the authorise route where the user explicitly gives permission for the client to act on their behalf.

<pre><code data-language="php">public function action_authorise()
{
    // Retrieve the auth params from the user's session
    $params['client_id'] = Session::get('client_id');
    $params['client_details'] = Session::get('client_details');
    $params['redirect_uri'] = Session::get('redirect_uri');
    $params['response_type'] = Session::get('response_type');
    $params['scopes'] = Session::get('scopes');

    // Check that the auth params are all present
    foreach ($params as $key=>$value) {
        if ($value === null) {
            // Throw an error because an auth param is missing - don't
            //  continue any further
        }
    }

    // Get the user ID
    $params['user_id'] = Session::get('user_id');

    // User is not signed in so redirect them to the sign-in route (/oauth/signin)
    if ($params['user_id'] === null) {
        return Redirect::to('signin');
    }

    // Check if the client should be automatically approved
    $autoApprove = ($params['client_details']['auto_approve'] === '1') ? true : false;

    // Process the authorise request if the user's has clicked 'approve' or the client
    if (Input::get('approve') !== null || $autoApprove === true) {

        // Generate an authorization code
        $code = $this->authserver->newAuthoriseRequest('user', $params['user_id'], $params);

        // Redirect the user back to the client with an authorization code
        return Redirect::to(
            \OAuth2\Util\RedirectUri::make($params['redirect_uri'],
            array(
                'code'  =>  $code,
                'state' =>  isset($params['state']) ? $params['state'] : ''
            )
        ));
    }

    // If the user has denied the client so redirect them back without an authorization code
    if (Input::get('deny') !== null) {
        return Redirect::to(
            \OAuth2\Util\RedirectUri::make($params['redirect_uri'],
            array(
                'error' =>  $this->authserver->exceptionCodes[2],
                'error_message' =>  $this->authserver->errors[$this->authserver->exceptionCodes[2]],
                'state' =>  isset($params['state']) ? $params['state'] : ''
            )
        ));
    }

    // The client shouldn't automatically be approved and the user hasn't yet
    //  approved it so show them a form
    return View::make('oauth.authorise', $params);
}
</code></pre>

In the authorize form the user should again be told the name of the client and also list all the scopes (permissions) it is requesting.

The final route to create is where the client exchanges the authorization code for an access token.

<pre><code data-language="php">public function action_access_token()
{
    try {

        // Tell the auth server to issue an access token
        $response = $this->authserver->issueAccessToken();

    } catch (\Oauth2\Exception\ClientException $e) {

        // Throw an exception because there was a problem with the client's request
        $response = array(
            'error' =>  $this->authserver::getExceptionType($e->getCode()),
            'error_description' => $e->getMessage()
        );

    } catch (Exception $e) {

        // Throw an error when a non-library specific exception has been thrown
        $response = array(
            'error' =>  'undefined_error',
            'error_description' => $e->getMessage()
        );
    }

    header('Content-type: application/json');
    echo json_encode($response);
}
</code></pre>

---

A complete example of an authorisation server can be found at [https://github.com/lncd/oauth2-example-auth-server](https://github.com/lncd/oauth2-example-auth-server).