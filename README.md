# spring-security

Q: How is Security mechanism implemented using Spring?
Spring makes use of the DelegatingFilterProxy for implementing security mechanisms. It is a Proxy for standard Servlet Filter, delegating to a Spring-managed bean that implements the Filter interface. Its the starting point in the springSecurityFilterChain which instantiates the Spring Security filters according to the Spring configuration
Some of the features of Spring Security are
Comprehensive and extensible support for both Authentication and Authorization
Protection against attacks like session fixation, clickjacking, cross site request forgery, etc
Servlet API integration Optional integration with Spring Web MVC


Q: What is OAuth2 Authorization code grant type? How to implement it using Spring Boot Security?
A: OAuth (Open Authorization) is a simple way to publish and interact with protected data.
It is an open standard for token-based authentication and authorization on the Internet. It allows an end user's account information to be used by third-party services, such as Facebook, without exposing the user's password.
The OAuth specification describes five grants for acquiring an access token:
Authorization code grant
Implicit grant
Resource owner credentials grant
Client credentials grant
Refresh token grant
Consider the use case of Quora. Go to Quora.com.
If you are a new user you need to signup. You can signup using google or facebook account. When doing so you are authorizing Google or Facebook to allow quora to access you profile info with Quora. This authorizing is done using OAuth. Here you have in no way shared your credentials with Quora.


Q: What is JWT ? How to implement it using Spring Boot Security?

(JSON Web Token)
For better understanding we will be developing the project in stages
Develop a Spring Boot Application to expose a Simple REST GET API with mapping /hello.
Configure Spring Security for JWT. Expose REST POST API with mapping /authenticate using
which User will get a valid JSON Web Token. And then allow the user access to the api /hello only if it has a valid token


Q: What is OAuth2 Client Credentials Grant? How to implement it using Spring Boot Security?
A: The Client Credentials Grant involves machine to machine authentication. In case of Client credentials grant type
the user has no role to play. As previously stated it is machine to machine communication. 
This is typically used by clients to access resources about themselves rather than to access a user's resources.

Q: What is OAuth2 Password Grant? How to implement it using Spring Boot Security?
A: In case of Password grant type the user triggers the client to get some resource.
While doing so it passes the username and password to the client. The client then communicates
with the authorization server using the provided username, password and also its own clientId and
clientSecret to get the access token. Using this access token it then gets the required resource from the resource server.



<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-core</artifactId>
    <version>5.3.3.RELEASE</version>
</dependency>


@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) 
      throws Exception {
        auth.inMemoryAuthentication().withUser("user")
          .password(passwordEncoder().encode("password")).roles("USER");
    }

@Override
protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests()
      .anyRequest().authenticated()
      .and().httpBasic();
}

@Override
protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests()
      .anyRequest().authenticated()
      .and().formLogin()
      .loginPage("/login").permitAll();
}

protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests()
      .antMatchers("/", "/home").access("hasRole('USER')")
      .antMatchers("/admin/**")
      .hasRole("ADMIN")
      .and()
      // some more method calls
      .formLogin();
}

@Autowired
public void configureGlobal(AuthenticationManagerBuilder auth) 
  throws Exception {
    auth.inMemoryAuthentication()
      .withUser("user").password(passwordEncoder()
      .encode("password"))
       .roles("USER")
      .and()
      .withUser("admin")
     .password(passwordEncoder()
    .encode("password"))
.roles("USER", "ADMIN");
}

@Override
protected void configure(HttpSecurity http){
http.addFilter("preAuthentication")
.authorization.
.antMatchers("*/*").permitAll()
.antMatchers("*/*").hasAnyRole()
.and().csrf();
}


}

@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}


