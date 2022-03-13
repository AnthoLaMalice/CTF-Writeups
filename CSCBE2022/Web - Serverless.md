# CSCBE 2022 - Serverless

## Web - Medium

In this challenge, we have been given a website where we cannot do anything except test a "serverless" form. This Serverless form allows us to send an email to an arbitrary email address.

![](https://i.imgur.com/QvYvLtN.png)

![](https://i.imgur.com/NOIviJn.png)

From there, we tried several things until we thought of a Server Side Template Injection 
(https://portswigger.net/research/server-side-template-injection)

So we tried some basic payloads to detect wheter SSTI is possible. The most basic payload to detect it is :
{{ 7*7 }}

If it is executed and the output is 49, then a SSTI is indeed possible.

![](https://i.imgur.com/aPoraer.png)

![](https://i.imgur.com/NXcxqJv.png)

And indeed it is, now we need to find out what type of template is running. To do it, we used this documentation : 

https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection

But we never managed to find out which template was used.
Looking again at the website and Serverless, we started to think that it might just be JavaScript.. So we searched for Templates used with Javascript and we came accross Handlebars.
While searching for documentation on this subject, we came across this blogpost.

http://mahmoudsec.blogspot.com/2019/04/handlebars-template-injection-and-rce.html

In this blogpost, he tried to include the environment variables that leaked the AWS secret keys. Let's try this.

![](https://i.imgur.com/ZQzi32h.png)


![](https://i.imgur.com/5EyqVUG.png)

Let's try to access the env variables 

![](https://i.imgur.com/ozOR766.png)

![](https://i.imgur.com/7ZCZ5XA.png)

There also seem to be some AWS variables involved.

Let's dump everything. This payload is inspired by the blogpost.

![](https://i.imgur.com/35Q4fTH.png)

![](https://i.imgur.com/Ng2WB8u.png)

The full output is as follows:

```json
Message: {
  "AWS_LAMBDA_FUNCTION_VERSION": "$LATEST",
  "AWS_SESSION_TOKEN": "IQoJb3JpZ2luX2VjEMr//////////wEaDGV1LWNlbnRyYWwtMSJHMEUCIQCnPVFUqNPlLE9ES6uXFP7rD40Kk4Sw8nTtzvAPfjQaMAIgG4Ru4OLPlI0tn5ouu8OcqAvcZhs474maqFG5WDhbAdUqmAIIRBAAGgw0MTExNDAwNTAxNTIiDCGQNAr8WqcUKctKeSr1AQUAMAukfp2Y6ogf3Cg2DWegTJIdGIf7jLtBd7ww9lIjR1Eleyy/CWzhq2f7GNcmafitzUjah5TeIhmQHXf3qPzX64yNVU5BAOsSpVUHzsQFFXSKUYe/s2GtHXhwOqm4QL4Y2awgEZnVdWFsJI5hektoTt08S0gbSsC5/OrTIFS7i+GIvoUI0eWqvpzamIlJ1TpeKkJQzk69zv6kDQ1C6uB5klHfCAMO48u+bUgRbzOEhwiIyr7edS/hjVN+8sIlBzpiw54JGBakgByyfiT/LwVORLwRdWVckgKnau75gdAXYe4A+IkParDLZ3lbW/msLgNyFj2qMJGMt5EGOpoBntE6VDizkegcUInwgS/t7Lhco6GV+LzcXC2KicFRuExmWkkPidKRVOXF2Xz5VQ0nZQzm6aR2zQeoJi+Z/azmerWUywr9vwjOGgwvjauwp3sFgsOBaPMTF+zWMP7Wc4qyiSLGemgzhZlOWPeFlWuv99nEnYGFlkJnAHF2C7KAINtaTP6JopSpsokmUYtAzytaIkBqDowMSoqu+g==",
  "AWS_LAMBDA_LOG_GROUP_NAME": "/aws/lambda/contact-form-lambda",
  "LAMBDA_TASK_ROOT": "/var/task",
  "LD_LIBRARY_PATH": "/var/lang/lib:/lib64:/usr/lib64:/var/runtime:/var/runtime/lib:/var/task:/var/task/lib:/opt/lib",
  "AWS_LAMBDA_RUNTIME_API": "127.0.0.1:9001",
  "AWS_LAMBDA_LOG_STREAM_NAME": "2022/03/13/[$LATEST]c64b9c5196c44645b7eef84498515eb8",
  "AWS_EXECUTION_ENV": "AWS_Lambda_nodejs12.x",
  "AWS_LAMBDA_FUNCTION_NAME": "contact-form-lambda",
  "AWS_XRAY_DAEMON_ADDRESS": "169.254.79.129:2000",
  "PATH": "/var/lang/bin:/usr/local/bin:/usr/bin/:/bin:/opt/bin",
  "AWS_DEFAULT_REGION": "eu-central-1",
  "PWD": "/var/task",
  "AWS_SECRET_ACCESS_KEY": "PYweD7Oy6uBDSZX9KhFWcM5e0uuJIObVQnH+fc4E",
  "LAMBDA_RUNTIME_DIR": "/var/runtime",
  "LANG": "en_US.UTF-8",
  "AWS_LAMBDA_INITIALIZATION_TYPE": "on-demand",
  "NODE_PATH": "/opt/nodejs/node12/node_modules:/opt/nodejs/node_modules:/var/runtime/node_modules:/var/runtime:/var/task",
  "TZ": ":UTC",
  "AWS_REGION": "eu-central-1",
  "AWS_ACCESS_KEY_ID": "ASIAV7OO3JTUP76PKQPL",
  "SHLVL": "0",
  "_AWS_XRAY_DAEMON_ADDRESS": "169.254.79.129",
  "_AWS_XRAY_DAEMON_PORT": "2000",
  "AWS_XRAY_CONTEXT_MISSING": "LOG_ERROR",
  "_HANDLER": "index.handler",
  "AWS_LAMBDA_FUNCTION_MEMORY_SIZE": "128",
  "NODE_EXTRA_CA_CERTS": "/etc/pki/tls/certs/ca-bundle.crt",
  "_X_AMZN_TRACE_ID": "Root=1-622dc611-7ab5631e74fb19b07704db6a;Parent=7d97fce66c8f21bc;Sampled=0"
}
```
Now that we have our AWS secret key, sessions token and access key leaked, we can connect to the instance. In order to do this, we had to read a lot of documentation and came accross a tool named Pacu.

https://github.com/RhinoSecurityLabs/pacu

Notes : 
- We kept thinking that the beginning of the challenge was about SSTI, but it's just that our Javascript input was interpreted. So the {{}} were not necessary. Although thinking about a SSTI helped us find the website that explained most of the steps.
- We didn't manage to solve this challenge because the token, secret key and access key were dynamic and changed every 10 minutes or so, and we didn't notice it. So we always got a "token expired" error when we tried to connect to the instance.

Let's move on and use Pacu to connect to the AWS instance
Some great documentation about Pacu and its usage can be found here: 

https://rzepsky.medium.com/playing-with-cloudgoat-part-5-hacking-aws-with-pacu-6abe1cf5780d

https://rhinosecuritylabs.com/aws/pacu-open-source-aws-exploitation-framework/

https://world.hey.com/alois/aws-capture-the-flag-write-up-e64fa089

https://blog.welcomethrill.house/2020/11/2020-hth-ctf-cloud-challenges.html

Let's start by running Pacu and setting up our region. We can find it in the variable `AWS_REGION`.

![](https://i.imgur.com/VxaPbYO.png)

Next, we need to create our session and enter our keys.

![](https://i.imgur.com/VUJWgR8.png)

- The key ID corresponds to AWS_ACCESS_KEY_ID
- The secret access key to AWS_SECRET_ACCESS_KEY
- And the session token to AWS_SESSION_TOKEN

Now we can use the module `iam__enum_users_roles_policies_groups` which allows us to list the different roles available.
https://rhinosecuritylabs.com/aws/escalating-aws-iam-privileges-undocumented-codestar-api/

![](https://i.imgur.com/cPd2NYr.png)

We can see that 8 roles are available. The output is saved in the Pacu database, it can be printed with the `data` command.
From there, we can find a secret-role which is apparently able to Access secrets. 

![](https://i.imgur.com/m8JUUtQ.png)

The policy of this role allows us to `Assume` it. This means that we can use this role to list the secrets. To assume this role, we need to use the `assume_role` functionality and pass it the ARN that is displayed in the role definition as an argument. 

![](https://i.imgur.com/0gqD1Re.png)

And now, we can run the `enum__secrets` module to list the secrets.
 
![](https://i.imgur.com/absefmE.png)

It seems that 3 secrets have been recovered, let's look at them by showing the content of the file that was created on our system.

![](https://i.imgur.com/B4wOWzc.png)

And there we find both the flag and a Rick Roll.

Another interesting challenge which we were really close to solve if we didn't miss the fact that the tokens were sometimes refreshed.
