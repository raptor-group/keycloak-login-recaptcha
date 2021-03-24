# keycloak-login-recaptcha

By default Keycloak (up to 12) only supports ReCaptcha for Registration, not login. so we created a simple module for activating recaptcha for login

# How to use
* first run `mvn clean install`. it will produce `target/recaptcha-login.jar`.
* Then start keycloak with `docker-compose up`
    * the `target/recaptcha-login.jar` is mounted to `/opt/jboss/keycloak/standalone/deployments/recaptcha-login.jar`, so it will
      be detected by JBoss automatically as a plugin.
    * `login.ftl` is the login template inside `base` theme for keycloak in `/opt/jboss/keycloak/themes/base/login/login.ftl`.
    We decided to override it to add recaptcha related items into it directly. so we have a modified `login.ftl` file inside project.
      which is mounted into the container.
    * we injected realm settings in to keycloaks' container by `playground-realm.json`.
      One of the most important realm settings is `X-Frame-Options` which is set to `ALLOW-FROM https://www.google.com` in 
      the file. The other important setting is `Content-Security-Policy` which is set to `frame-src 'self' https://www.google.com; frame-ancestors 'self'; object-src 'none';`.
* Go to `localhost:8080` and login with `admin` username and `admin` password.
* Make sure You're in `Playground` realm. Go to `Authentication` section in left side menu. Go to `Flows` tab.
* Pick `Browser With Recaptcha` flow in the drop down
* Under `Recaptcha Username Password Form` row click `Actions` and then `Config`
* Put the `sitekey` and `secret` obtained from `https://www.google.com/recaptcha/admin` into the config
* Go back to `Authentication` side menu, go to `Bindings` tab and change `browser flow` to `browser with recaptcha`
* Go to `Users` side menu, click `Add user`. create a user and make it enabled, and set it's credential properly.
* Go back to `Users`, click `View all users` and on that specifiec user you have created in prev step, click `impersonate`.
* Then `Sign Out` and then `Sign In`. If you have done everything precisely, then you'll be able to see the recaptcha form.


# Explaination

Inside the `login.ftl`, We have just added this part inside `<form></form>` section.
So inside `RecaptchaUsernamePasswordForm` we will set `recaptchaRequired` to `true`, and this part
of `login.ftl` will be enabled for recaptcha based logins.
If using normal `UsernamePasswordForm` then this part will not be enabled in the login form.
```
<#if recaptchaRequired??>
    <div class="form-group">
        <div class="${properties.kcInputWrapperClass!}">
            <div class="g-recaptcha" data-size="compact" data-sitekey="${recaptchaSiteKey}"></div>
        </div>
    </div>
</#if>
```
