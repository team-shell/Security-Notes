# OAuth2 v OAuth Key Differences

* More OAuth Flows to allow better support for non-browser based applications. This is a main criticism against OAuth from client applications that were not browser based. For example, in OAuth 1.0, desktop applications or mobile phone applications had to direct the user to open their browser to the desired service, authenticate with the service, and copy the token from the service back to the application. The main criticism here is against the user experience. With OAuth 2.0, there are now new ways for an application to get authorization for a user.

* OAuth 2.0 no longer requires client applications to have cryptography. This hearkens back to the old Twitter Auth API, which didn't require the application to HMAC hash tokens and request strings. With OAuth 2.0, the application can make a request using only the issued token over HTTPS.

* OAuth 2.0 signatures are much less complicated. No more special parsing, sorting, or encoding.

* OAuth 2.0 Access tokens are "short-lived". Typically, OAuth 1.0 Access tokens could be stored for a year or more (Twitter never let them expire). OAuth 2.0 has the notion of refresh tokens. While I'm not entirely sure what these are, my guess is that your access tokens can be short lived (i.e. session based) while your refresh tokens can be "life time". You'd use a refresh token to acquire a new access token rather than have the user re-authorize your application.

* Finally, OAuth 2.0 is meant to have a clean separation of roles between the server responsible for handling OAuth requests and the server handling user authorization. More information about that is detailed in the aforementioned article.