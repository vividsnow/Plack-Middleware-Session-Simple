# NAME

Plack::Middleware::Session::Simple - Make Session Simple

# SYNOPSIS

    use Plack::Builder;
    use Cache::Memcached::Fast;

    my $app = sub {
        my $env = shift;
        my $counter = $env->{'psgix.session'}->{counter}++;
        [200,[], ["counter => $counter"]];
    };
    

    builder {
        enable 'Session::Simple',
            cache => Cache::Memcached::Fast->new({servers=>[..]}),
            session_key => 'myapp_session';
        $app
    };



# DESCRIPTION

Plack::Middleware::Session::Simple is a yet another session management module.
This middleware supports psgix.session and psgi.session.options. 
Plack::Middleware::Session::Simple has compatibility with Plack::Middleware::Session 
and you can reduce unnecessary accessing to cache Store and Set-Cookie header.

This module uses Cookie to keep session state. does not support URI based session state.

# OPTIONS

- cache

    cache object instance that has get, set, and remove methods.

- session\_key

    This is the name of the session key, it defaults to 'simple\_session'.

- keep\_empty

    If disabled, Plack::Middleware::Session::Simple does not output Set-Cookie header and store session until session are used. You can reduce Set-Cookie header and access to session store that is not required. (default: true)

        builder {
            enable 'Session::Simple',
                cache => Cache::Memcached::Fast->new({servers=>[..]}),
                session_key => 'myapp_session',
                keep_empty => 0;
            mount '/' => sub {
                my $env = shift;
                [200,[], ["ok"]];
            },
            mount '/login' => sub {
                my $env = shift;
                $env->{'psgix.session'}->{user} = 'session user'
                [200,[], ["login"]];
            },
        };
        

        my $res = $app->(req_to_psgi(GET "/")); #res does not have Set-Cookie
        

        my $res = $app->(req_to_psgi(GET "/login")); #res has Set-Cookie

- path

    Path of the cookie, this defaults to "/";

- domain

    Domain of the cookie, if nothing is supplied then it will not be included in the cookie.

- expires

    Cookie's expires date time. several formats are supported. see [Cookie::Baker](http://search.cpan.org/perldoc?Cookie::Baker) for details.
    if nothing is supplied then it will not be included in the cookie, which means the session expires per browser session.

- secure

    Secure flag for the cookie, if nothing is supplied then it will not be included in the cookie.

- httponly

    HttpOnly flag for the cookie, if nothing is supplied then it will not be included in the cookie.

# LICENSE

Copyright (C) Masahiro Nagano.

This library is free software; you can redistribute it and/or modify
it under the same terms as Perl itself.

# AUTHOR

Masahiro Nagano <kazeburo@gmail.com>