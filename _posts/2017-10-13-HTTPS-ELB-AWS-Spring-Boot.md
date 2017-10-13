---
layout: post
title: Configure a Spring Boot app for HTTPS on Amazon AWS.
---

Even trivial websites are moving from HTTP to HTTPS these days, as Google has made the deprecation of HTTP websites a corporate priority.

I didn't want to drop $1000 on a certificate, or even $5/mo. for the privilege, and I was already hosting my sites on Amazon EC2. I decided to leave my plain old web app running as an EC2 instance on port 80 and to hide it behind an Elastic Load Balancer handling the HTTPS task. I did have to transfer my domain name from GoDaddy to Amazon's Route 53 service for $12. Having the domain name inside the Amazon mothership made everything easy.

### Help from Amazon

Setting up a load balancer in front an Amazon EC2 instance is pretty simple. Amazon walks you through it.

[Tutorial: Create a Classic Load Balancer](http://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-getting-started.html)

What is nice about this solution is that the instance runs on port 80 (or port 8080 or whatever) behind the load balancer. Furthermore, when setting up the load balancer, if you request port 443, Amazon will walk you through the process of obtaining a security certificate.

What has hard was configuring the setup to redirect http requests to https so that all your old links would work and the site is still easy to find. I solved that by configuring the Spring Boot instance to detect http requests by watching the X-Forwarded-Proto header. If it contains http, Spring Boot will redirect to the same url, but with https. And Spring Boot does it automatically if you configure it properly.

### Solution Diagram

![_config.yml]({{ site.baseurl }}/images/amazon_aws_elb.png)

### Configuration Details

The Spring Boot app needs some configuration to enable it to serve https requests and redirect http to https.

#### application.properties

Add these configuration settings.

    security.require-ssl=true
    server.tomcat.remote_ip_header=x-forwarded-for
    server.tomcat.protocol_header=x-forwarded-proto

#### spring app security file

What is important are these settings. This will force the http requests to redirect to https if the header is `X-Forwarded-Proto: http`

    .requiresChannel().anyRequest().requiresSecure()

Here is what the entire spring security configuration migh look like:

    package net.tallgeorge.homepage.security;

    import org.springframework.context.annotation.Configuration;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

    @EnableWebSecurity
    @Configuration
    public class AppSecurity extends WebSecurityConfigurerAdapter {

        @Override
        protected void configure(HttpSecurity httpSecurity) throws Exception {
            httpSecurity
                    .requiresChannel().anyRequest().requiresSecure()
                    .and()
                    .csrf().disable()
                    .authorizeRequests()
                    .antMatchers("/login", "/error")
                    .permitAll()
                    .antMatchers("/")
                    .hasRole("USER")
                    .antMatchers("/admin")
                    .hasRole("ADMIN")
                    .anyRequest().authenticated()
                    .and()
                    .formLogin()
                    .loginPage("/login")
                    .defaultSuccessUrl("/")
                    .and()
                    .logout()
                    .and()
                    .sessionManagement()
                    .maximumSessions(60)
                    .expiredUrl("/login?expired");
        }
    }


