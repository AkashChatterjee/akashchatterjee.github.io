---
layout: post
title: "Process OpenID JWT Tokens in Spring Boot"
date: 2021-06-11
categories: tech
tags: [spring-boot, openid, jwt, authentication, java, security]
---

## What is OpenID?

Have you come across the option to log in to a website using your Google account? That's OpenID in action. Simply put, OpenID enables you to use a single identification account across multiple websites / applications. The user benefits from not having to memorize another username-password combination and the web developers avoid the need to develop a comprehensive security shield to protect the same. A classic win-win situation.

However, this post is not meant to explain the theory behind OpenID. That's for some other time. The goal behind this short post is to quickly give you an overview of how to process the ID Token (JWT) sent by an OpenID provider. I shall be providing the example in Spring Boot. Although the general approach remains the same, you might have to search for appropriate libraries if you're working with a different tech stack.

In this post, I shall be covering the exact code to process OpenID tokens from two of the most popular OpenID providers out there – Google and Microsoft.

In addition to the actual processing step, I shall be explaining how to leverage the information contained in the JWT for each API call via Request Filters.

## Who is this post for?

- You're relatively new to the concept of JWTs or OpenID.
- You're a backend developer who just got handed an OpenID JWT from the frontend but have no idea what to do with it.
- You have a decent idea about at least one backend tech stack (e.g. Spring Boot, Express, Django etc.). This will help you absorb the basic flow even if you're not hands-on with Spring Boot.

## So what's the general flow when processing the OpenID JWT token?

1. Receive the JWT token (sent as part of your request's headers) in a request filter.
2. Decode the token to extract information about the issuer.
3. Based on the issuer, verify the token (i.e. if it was signed by the OpenID provider's private keys) by validating the signature against the OpenID provider's public keys.
4. Extract the user details to enrich the details already provided in the JWT token and pass it down to the Controller to be used as part of the actual business logic.

Let's break down the above.

## Step 1: Receive the JWT

Build a new component in your Spring Boot application. Let's call it `AuthFilter.java`. What this means, is that you should create a new Java class and annotate it with `@Component`.

Configure this class to extend the `OncePerRequestFilter` (org.springframework.web.filter). This ensures that this filter shall run for each request sent to the backend.

You'll be asked to override the `doFilterInternal` method with 3 arguments i.e. HttpServletRequest request, HttpServletResponse response and FilterChain filterChain.

```java
//Get the header field
String authorization = request.getHeader("Authorization");

//Get the token by removing the "Bearer " prefix in the header
String token = authorization.substring(7);
```

## Step 2: Decode the JWT

Import the following 3 projects from the Maven repository that we shall leverage in the next few steps:

```gradle
// https://mvnrepository.com/artifact/com.auth0/java-jwt
implementation group: 'com.auth0', name: 'java-jwt', version: '3.16.0'

// https://mvnrepository.com/artifact/com.auth0/jwks-rsa
implementation group: 'com.auth0', name: 'jwks-rsa', version: '0.18.0'

// https://mvnrepository.com/artifact/org.json/json
implementation group: 'org.json', name: 'json', version: '20210307'
```

Create a new method `decodeBase64` to process the token.

```java
public String decodeBase64(String input) {
    return new String(Base64.getDecoder().decode(input));
}
```

Use the following code snippet to extract the issuer information from the JWT token string we got in Step 1.

```java
//The input here is the String token we got in Step 1.
DecodedJWT decodedJWT = JWT.decode(token);

//Use a JSONObject to easily process such objects in Java
JSONObject jwtPayload = new JSONObject(decodeBase64(decodedJWT.getPayload()));

//I've noticed "iss" to be used for both Google and Microsoft Auth. You can check the payload data by visiting https://jwt.io if you run into some issues.
String issuer = (String) (jwtPayload.get("iss"));
```

The reason we're not extracting any more information in one-shot is because based on the issuer, the field names for other details like name, email etc. might be different in the JWT. You can paste the JWT on https://jwt.io and check out the exact field names.

Now based on the issuer, you can introduce the validation logic in Step 3.

## Step 3: Validate token based on issuer

Let's take concrete examples here as each OpenID provider might have slightly different approaches.

### Google OpenID:

Import the following project from the Maven repository:

```gradle
// https://mvnrepository.com/artifact/com.google.api-client/google-api-client
compile group: 'com.google.api-client', name: 'google-api-client', version: '1.31.1'
```

Ensure you have the Google Authentication Client ID with you. This is provided when the application is configured on the Google Cloud console. If you don't have it, simply ask the frontend developer. She/He would be already using it to generate the JWT. You can store this client ID in your `application.properties` file and fetch it using the `@Value` annotation in `AuthFilter.java`

```java
@Component
public class AuthFilter extends OncePerRequestFilter {

  @Value("${google.auth.client.id}")
  private String googleAuthClientId;

  @Override
  protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)  {
    //CODE
  }
}
```

Now, use the following code snippet to verify the token:

```java
try {
    GoogleIdTokenVerifier verifier = new GoogleIdTokenVerifier.Builder(new NetHttpTransport(), new JacksonFactory())
                .setAudience(Collections.singletonList(googleAuthClientId))
                .build();

    //The 'token' variable here is the token string extracted in Step 1.
    GoogleIdToken idToken = verifier.verify(token);

    if (idToken != null) {
            GoogleIdToken.Payload payload = idToken.getPayload();

            //Here is where we extract information from the Google OpenID token. More in Step 4.
    }

} catch (GeneralSecurityException | IOException e) {
    //Handle exception
}
```

### Microsoft OpenID:

To verify the Microsoft token, we don't have any out-of-the-box libraries provided by the verification by matching public keys behind the scenes. So let's validate it ourselves.

**Note:** This method also works for the Google OpenID. It's just always a good practice to use ready-made libraries published by the company wherever possible to stay future proof.

Use the following code snippet to validate the token.

```java
JwkProvider microsoftJwkProvider = new UrlJwkProvider(new URL("https://login.microsoftonline.com/common/discovery/v2.0/keys"));

//The 'decodedJWT' variable here is the one we obtained in Step 2. 
Jwk jwk = microsoftJwkProvider.get(decodedJWT.getKeyId());
Algorithm algorithm = Algorithm.RSA256((RSAPublicKey) jwk.getPublicKey(), null);

try {
    algorithm.verify(decodedJWT);
} catch (SignatureVerificationException e) {
    //Handle exception
}

if (decodedJWT.getExpiresAt().before(Calendar.getInstance().getTime())) {
    //Handle logic for expired tokens.
}
```

## Step 4: Extract more information from the token and pass it on to the Controller

Create a class to host the current user who's sending the request. Let's call it `CurrentUser.java`. Ensure you provide the `@Component` annotation with this class. In addition to this, add the `@RequestScope` annotation for this object.

```java
@Component
@RequestScope
@Data
public class CurrentUser {
    //Field Names e.g. email
    String email;
}
```

### Google OpenID:

```java
// Continuation from Step 3. Here we break down the payload we received.
GoogleIdToken.Payload payload = idToken.getPayload();
String email = payload.getEmail();

CurrentUser currentUser = new CurrentUser();
currentUser.setEmail(email);

//At this point, based on the email, you could also fetch more data from your database to enrich the CurrentUser object

//Set the CurrentUser as a request attribute
request.setAttribute("currentUser", currentUser);

//This ensures the FilterChain passes it on the API call to the Controller after whatever we needed is processed.
filterChain.doFilter(request, response);
```

### Microsoft OpenID:

Extract information.

```java
//Extract information like email from the jwtPayload obtained in Step 2.
String email = (String) jwtPayload.get("email");

CurrentUser currentUser = new CurrentUser();
currentUser.setEmail(email);

//At this point, based on the email, you could also fetch more data from your database to enrich the CurrentUser object

//Set the CurrentUser as a request attribute
request.setAttribute("currentUser", currentUser);

//This ensures the FilterChain passes it on the API call to the Controller after whatever we needed is processed.
filterChain.doFilter(request, response);
```

In the Controller, use the CurrentUser object for any information required of the authenticated user.

```java
//Yeah, I know this is a stupid example. But you get the idea.
@GetMapping("/current-user-email")
public ResponseEntity<String> getCurrentUser(
            @RequestAttribute(value = "currentUser") CurrentUser currentUser) {

    return ResponseEntity.ok(currentUser.getEmail());

}
```

## Conclusion

I hope this post gave you an overview of how to process the OpenID JWT tokens and pass it down to the Controller so that the information can be efficiently used in the business logic.
