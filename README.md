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
		- ./keycloak-login-recaptcha/target/recaptcha-login.jar:/opt/jboss/keycloak/standalone/deployments/recaptcha-login.jar
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



# reCAPTCHA Support

To safeguard registration against bots, Keycloak has integration with Google reCAPTCHA. To enable this you need to first go to Google Recaptcha Website and create an API key so that you can get your reCAPTCHA site key and secret.

Next, there are a few steps you need to perform in the Keycloak Admin Console. Click the Authentication left menu item and go to the Flows tab. Select the Browser flow from the drop down list on this page. Click on the copy button so that you can copy this flow and give it a name "Login with captcha". After this is done, then you will see "Auth Type" column on the left side of the page. Go to the row where "Auth Type" is "Login with captcha". Then click on "Actions" hyperlink. Then click on "Add execution" and select "Recaptcha Username Password Form" from the dropdown and click on save. Then click on "Actions" hyperlink for "Recaptcha Username Password Form". Then click on config and fill google reCaptcha details and click on save. Then delete "Username Password Form" by clicking on "Actions hyperlink". Finally move "Recaptcha Username Password Form" before "OTP Form" by clicking on up arrow button in the UI. Finally select "REQUIRED" option for "Recaptcha Username Password Form".   

You should also enable external origin `https://google.com` as mentioned in keycloak documentation.  [Recaptcha Documentation](https://www.keycloak.org/docs/latest/server_admin/index.html#_recaptcha)

![Keycloak Security Headers](https://www.keycloak.org/docs/latest/server_admin/keycloak-images/security-headers.png)
