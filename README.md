# keycloak-login-recaptcha

By default Keycloak (up to 4.4.0) only supports ReCaptcha for Registration, not Login. This module
allows you to enable ReCaptcha for Login.

#		How to use
For building it you need to run `mvn clean install`. It will produce a jar `target/recaptcha-login.jar`.
For making it accessible in Keycloak you should copy this jar into Keycloak's `standalone/deployments/` directory.

If you're using it in docker environment, then you should mount it in `/opt/jboss/keycloak/standalone/deployments/recaptcha-login.jar`
Docker Compose file Example:
```
...
keycloak:
	image: jboss/keycloak:4.2.1.Final
	.
	.
	.
	volumes:
		- ./realm-config:/opt/jboss/keycloak/realm-config
		- ./my-theme/:/opt/jboss/keycloak/themes/my-theme/
		- ./kc-recaptcha-module/target/recaptcha-login.jar:/opt/jboss/keycloak/standalone/deployments/recaptcha-login.jar
...
```
Add the following piece of code in your `login.ftl` template file:
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
You should paste it inside your login `<form></form>` in your login template (`login.ftl`)

Path of login.ftl inside docker container - /opt/jboss/keycloak/themes/base/login/login.ftl

And finally you should enable external origin `https://google.com` as mentioned in keycloak documentation.  [Recaptcha Documentation](https://www.keycloak.org/docs/latest/server_admin/index.html#_recaptcha)
