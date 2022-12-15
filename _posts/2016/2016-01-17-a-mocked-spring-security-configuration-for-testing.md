---
layout: post
title: "A mocked Spring security configuration for testing"
date: "2016-01-17"
categories: 
  - "coding"
  - "java"
  - "security"
  - "spring"
---

Spring security is great! It gives you the possibility to secure your app and to create a login with a few simple lines of code. However, it could also become annoying during development, when you have to log yourself in again and again. So why not create a mocked Spring authentication for development and testing?

## Create a new profile to active the mock

Before we implement the actual mock, we first create a new profile to active or deactive it. Spring profiles give you the opportunity to decide which bean Spring should use when starting your app. This means you can say "Hey Spring! Use my mock!" or "Hey Spring! Use my production implementation!". Let's call our profile "SECURITY\_MOCK". First, we add the profile to our production security configuration, so that we can switch it off and use the mock instead. Note that this is the only change we need to do to our already existing code!

    @Profile("!SECURITY\_MOCK") // If we don't use the SECURITY\_MOCK profile, we use this bean!
    @Configuration
    @EnableWebSecurity
    public class SecurityConfig extends WebSecurityConfigurerAdapter {
        // Your production security configuration
    }

Now we create our mock configuration:

    @Profile("SECURITY\_MOCK") // If we use the SECURITY\_MOCK profile, we use this bean!
    @Configuration
    @EnableWebSecurity
    public class SecurityMockConfig extends WebSecurityConfigurerAdapter {
        // Your mocked security configuration
    }

Using a profile like shown above, we can decide which implementation Spring will use. We just need to pass the profile to our application properties:

    spring.profiles.active=SECURITY\_MOCK

## A mocked authentication provider

Now that we have defined a profile to switch our mock on and off, we need to do the actual implementation. The first thing we do, is to write a mocked authentication provider. Spring will use this authentication provider to check the user, validate its password and to return the user details. The authentication provider depends on our own implementation, but it might look like that:

    @Profile("SECURITY\_MOCK")
    @Primary
    @Service
    public class AuthenticationProviderMock implements AuthenticationProvider {
    
        @Override
        public Authentication authenticate(Authentication authentication) throws AuthenticationException {
            MyOwnUser myUser = getUser(authentication.getPrincipal().toString());
            UsernamePasswordAuthenticationToken usernamePasswordAuthenticationToken
                    = new UsernamePasswordAuthenticationToken(myUser, "", mockGrantedAuthorities());
            usernamePasswordAuthenticationToken.setDetails(myUser);
            return usernamePasswordAuthenticationToken;
        }
    
        private MyOwnUser getUser(String principal) {
            return ... // Return our own user class here!
        }
    
        @Override
        public boolean supports(final Class authentication) {
            return true;
        }
    
        private List mockGrantedAuthorities() {
            return ... // Create our own authorities here!
        }
    } 

A couple of things are important right here:

- Use the SECURITY\_MOCK profile again and annotate the class with @Primary so it will replace any other bean when using the mock profile!
- As I'm pretty sure you want to use a custom user class, set it to the details!

## The mocked security config

After you implemented the mocked authentication provider, the actual security config will be very easy. The only thing you need to do is to use your authentication provider:

    @Autowired
    private AuthenticationProviderMock authenticationProviderMock;
    
    @Override
    protected void configure(final HttpSecurity http) throws Exception {
    
        // Note that this might depend on your concrete app!
        http
               .authenticationProvider(authenticationProviderMock)
               .formLogin()
               .and()
               .authorizeRequests()
               .anyRequest()
               .hasAnyAuthority( /\* your roles! \*/ )
               .and()
               .sessionManagement()
               .invalidSessionUrl(HOME\_PAGE\_URI)
               .sessionFixation()
               .newSession();
    }
    
    @Override
    protected void configure(final AuthenticationManagerBuilder auth) throws Exception {
        auth.authenticationProvider(authenticationProviderMock);
    }

## Usage

After you implemented both, the mocked security config and the authentication provider mock, the usage is straight forward. You just activate the profile (in your application properties) and then you can use the authenticated user in our code:

    @Override
    public MyOwnUser getCurrentUser() {
        return (MyOwnUser) SecurityContextHolder
                .getContext()
                .getAuthentication()
                .getDetails();
    }

**Best regards,** Thomas.
