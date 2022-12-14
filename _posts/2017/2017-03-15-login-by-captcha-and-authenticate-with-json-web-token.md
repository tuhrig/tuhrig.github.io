---
layout: post
title: "Login by captcha and authenticate with JSON Web Token"
date: "2017-03-15"
categories: 
  - "coding"
tags: 
  - "angularjs"
  - "captcha"
  - "java"
  - "javascript"
  - "jwt"
  - "security"
---

We use a login to secure our application and restrict its access to a certain group of users. But what if everybody should be allowed to use our application if he can prove he's human? Well, we would use a captcha.

The following example shows how we can use [Google's ReCaptcha](https://www.google.com/recaptcha) for an anonymous login and authentication with [JSON Web Token](https://jwt.io).

# Add Google's ReCaptcha to AngularJS

First, we need to add Google's ReCaptcha to our AngularJS application. In order to use ReCaptcha, we must register our web site on [https://www.google.com/recaptcha](https://www.google.com/recaptcha). In addition to our domain, we can also register `localhost` for our local development environment. By registering our web site, we will receive a public and a private key. The public key is used in the HTML below, the private key is used in our backend to verify the captcha later on.

## Component

We encapsulate the captcha in an AngularJS component:

angular.module('my.app').component('captcha', {
    templateUrl: 'app/captcha/captcha.html',
    controller: 'CaptchaController'
});

## Template

We embed the captcha on our HTML by adding the ReCaptcha JavaScript library and a placeholder `div` which contains a certain class name and our public key. The captcha will be injected to this `div`.

Right under the `div` we add our login button.

<script src="https://www.google.com/recaptcha/api.js" async defer=""></script>

...

Login

## Controller

Our controller provides a function to get a new token from the backend. However, the user must solve the captcha for this. Otherwise the backend will reject the request.

angular.module('my.app').controller('CaptchaController', function($scope, $http, $document) {

    var vm = this;

    vm.createToken = function () {

        // The solution of the captcha is appended as a request parameter. The backend 
        // will validate this parameter and check if the solution is correct.
        var captchaSolution = window.grecaptcha.getResponse();
        var url = "/api/token/create?g-recaptcha-response=" + captchaSolution; 

        $http.post(url).then(function (response) {

            // If the captcha was correct, the backend will return a login token. We save the 
            // login token in order to use it for any further request.
            $http.defaults.headers.common.Authorization = 'Bearer ' + response.data.token;
        });
    };
});    

# Add Google's ReCaptcha to Spring Boot

After we added the captcha to our frontend, we must verify it in our backend. To do so, we use a [Spring Boot starter for Google's ReCaptcha](https://github.com/mkopylec/recaptcha-spring-boot-starter):

compile('com.github.mkopylec:recaptcha-spring-boot-starter:1.3.8')

We must add our ReCaptcha private key to the `application.properties`:

recaptcha.validation.secretKey = 7Kfz1xgUAAOOANHich8cMs6uH2J3hxy0NwCSBR5H

With this library, we can easily validate the captcha in the backend. If the captcha is invalid, we throw an exception. Otherwise, we return an authentication token to the user - this is the login.

@RestController
@RequestMapping("/api/token")
public class CaptchaController {

    private final TokenService tokenService;
    private final RecaptchaValidator recaptchaValidator;

    @Autowired
    public CaptchaController(RecaptchaValidator recaptchaValidator) {
        this.recaptchaValidator = recaptchaValidator;
        this.tokenService = new TokenService();
    }

    @RequestMapping(path = "/create" method = RequestMethod.POST)
    public Token createToken(HttpServletRequest request) throws ServletException {
        ValidationResult result = recaptchaValidator.validate(request);
        if (result.isSuccess()) {
            return tokenService.createToken();
        }
        throw new ServletException("Invalid Captcha");
    }
}

# JSON Web Token

So let's take a look at the token. A very simple way to implement an authentication token is [JSON Web Tokens](https://jwt.io). The integration with Spring Boot and AngularJS is very good.

## How JWT works

JWT is based on a private key encryption. The user will get an encrypted token which contains his user name and must send this token with every request to the backend. If the token is invalid or missing, the request is denied. Since the token is send with every request, it's stateless!

## Creating a token

To create an JWT we use another library ([JJWT](https://github.com/jwtk/jjwt)):

compile('io.jsonwebtoken:jjwt:0.7.0')

Creating and verifying a token becomes very easy:

public class TokenService {

    private static final String SECRET\_KEY = "my-super-secret-key";

    public boolean isValid(HttpServletRequest request) {

        String authHeader = request.getHeader("Authorization");
        if (authHeader == null || !authHeader.startsWith("Bearer ")) {
            return false;
        }
        String token = authHeader.substring(7); // The part after "Bearer "

        try {
            Jwts.parser()
                .setSigningKey(SECRET\_KEY)
                .parseClaimsJws(token);
            return true;
        }
        catch (final SignatureException e) {
            return false;
        }
    }

    public Token createToken() {
        String token = Jwts.builder()
                           .setSubject(UUID.randomUUID().toString()) // A random user ID
                           .setIssuedAt(new Date())
                           .signWith(SignatureAlgorithm.HS256, SECRET\_KEY)
                           .compact();
        return new Token(token);
    }
}

By the way, the token looks like this:

public class Token {

    private String token;

    public Token(final String token) {
        this.token = token;
    }

    public String getToken() {
        return token;
    }

    public void setToken(String token) {
        this.token = token;
    }
}

## Securing our APIs

We are almost done! We have added a captcha which verifies that the user is a human. If so, we create a token and save it in our AngularJS application. The last thing we need to do, is to check the token when our API are called:

public class TokenFilter extends GenericFilterBean {

    private final TokenService tokenService = new TokenService();

    @Override
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) throws IOException, ServletException {
        final HttpServletRequest request = (HttpServletRequest) req;
        if(tokenService.isValid(request)) {
            chain.doFilter(req, res);
        } else {
            throw new ServletException("Invalid token.");
        }
    }
}

@Configuration
public class TokenFilterConfig {

    @Bean
    public FilterRegistrationBean jwtFilter() {
        final FilterRegistrationBean registrationBean = new FilterRegistrationBean();
        registrationBean.setFilter(new TokenFilter());
        registrationBean.addUrlPatterns("/my/secured/api/\*");
        return registrationBean;
    }
}

Done!

# Note

Note that this post mixes two completely different things - captchas and JSON Web Tokens. You can use a captcha without JSON Web Tokens (or any other form of authentication) and you can use JSON Web Tokens without a captcha. However, if your application has a lot of `POST` calls which are not secured behind a login context, this approach might help to increase security (by using a captcha) and still provide a good user experience (with just a single captcha for the whole application).

**Best regards,** Thomas

# More

- [https://www.google.com/recaptcha](https://www.google.com/recaptcha)
- [https://jwt.io](https://jwt.io)
- [https://github.com/jwtk/jjwt](https://github.com/jwtk/jjwt)
- [https://github.com/mkopylec/recaptcha-spring-boot-starter](https://github.com/mkopylec/recaptcha-spring-boot-starter)
- [https://ciphertrick.com/2015/05/19/google-recaptcha-with-angularjs](https://ciphertrick.com/2015/05/19/google-recaptcha-with-angularjs)
