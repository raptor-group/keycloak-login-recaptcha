# keycloak-login-recaptcha

By default Keycloak (up to 8.0.2) only supports ReCaptcha for Regsitration, not login. so we created a simple module for activating recaptcha for login

#		How to use
for building you need to run `mvn clean install`.  it will produce a jar `target/recaptcha-login.jar`.
for making it accessible in Keycloak you should copy this jar into keycloaks `standalone/deployment/` directory.
just it.
if you're using it in docker environment you should mount it in `/opt/jboss/keycloak/standalone/deployment/recaptcha-login.jar`
for example in my docker compose file:
```
...
keycloak:
	image: jboss/keycloak:8.0.2
	.
	.
	.
	volumes:
		- ./realm-config:/opt/jboss/keycloak/realm-config
		- ./my-theme/:/opt/jboss/keycloak/themes/my-theme/
		- ./kc-recaptcha-module/target/recaptcha-login.jar:/opt/jboss/keycloak/standalone/deployments/recaptcha-login.jar
...
```
and in your theme file you should add this piece of code in your `login.ftl` template file:
```
<#if recaptchaRequired??>
<div class="form-group">
	<div class="${properties.kcInputWrapperClass!}">
		<div class="g-recaptcha" data-size="compact" data-sitekey="${recaptchaSiteKey}">			
		</div>
	</div>
</div>
</#if>
```
you should past it inside your login `<form></form>` in your login template (`login.ftl`)

And finally you should enable external origin `https://google.com` like the way in keycloaks'  [Recaptcha Documentation](https://www.keycloak.org/docs/latest/server_admin/index.html#_recaptcha) mentioned.


# Thanks

Thanks to [@jabolina](github.com/jabolina) for fixing unsuccessful login problem.
