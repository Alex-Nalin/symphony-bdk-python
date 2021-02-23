# Configuration

The BDK configuration is one of the most essential feature of the Symphony BDK which allows developers to configure 
their bot environment.

## Minimal configuration example
The minimal configuration file that can be provided look like:
```yaml
host: mypod.symphony.com                                     # (1)

bot: 
    username: bot-username                                  # (2)
    privateKey:
      path: /path/to/bot/rsa-private-key.pem                # (3)

app:
    appId: app-id                                           # (4)
    privateKey:
      path: /path/to/bot/rsa-private-key.pem                # (5)
```
1. hostname of your Symphony pod environment
2. your bot (or service account) username as configured in your pod admin console (https://mypod.symphony.com/admin-console)
3. your bot RSA private key according to the RSA public key upload in your pod admin console (https://mypod.symphony.com/admin-console)
4. the app id of your extension application configured in your pod admin console.
5. your RSA private key to authenticate the extension application according to the RSA public key associated with your extension application.

## How to load configuration
The Symphony BDK provides a single way to configure your bot environment.

```python
from symphony.bdk.core.config.loader import BdkConfigLoader

config_1 = BdkConfigLoader.load_from_file("/absolute/path/to/config.yaml")  # 1

config_2 = BdkConfigLoader.load_from_content(config_content_as_string)  # 2

config_3 = BdkConfigLoader.load_from_symphony_dir("config.yaml")  # 3
```
1. Load configuration from a file
2. Load configuration from a string object
3. Load configuration from the Symphony directory. The Symphony directory is located under `.symphony` folder in your
   home directory. It can be useful when you don't want to share your own Symphony credentials within your project
   codebase

## Full configuration example
```yaml
scheme: https
host: localhost.symphony.com
port: 8443

proxy:
  host: proxy.symphony.com
  port: 1234
  username: proxyuser
  password: proxypassword

pod:
  host: dev.symphony.com
  port: 443

agent:
   host: dev-agent.symphony.com
   port: 5678
   proxy:
     host: agent-proxy
     port: 3396

keyManager:
  host: dev-key.symphony.com
  port: 8444

sessionAuth:
  host: dev-session.symphony.com
  port: 8444

ssl:
  trustStore:
    path: /path/to/truststore.pem

bot:
  username: bot-name
  privateKey:
    path: /path/to/bot/rsa-private-key.pem

app:
  appId: app-id
  privateKey:
    path: path/to/private-key.pem

datafeed:
  version: v2
```

### Configuration structure

The BDK configuration now includes the following properties:
- The BDK configuration can contain the global properties for `host`, `port`, `context` and `scheme`.
These global properties can be used by the client configuration by default or can be overridden if
user specify the dedicated `host`, `port`, `context`, `scheme` inside the client configuration.
- `proxy` contains proxy related information. This field is optional.
If set, it will use the provided `host` (mandatory), `port` (mandatory), `username` and `password`.
It can be overridden in each of the `pod`, `agent`, `keyManager` and `sessionAuth` fields.
- `pod` contains information like host, port, scheme, context, proxy... of the pod on which
the service account using by the bot is created.
- `agent` contains information like host, port, scheme, context, proxy... of the agent which
the bot connects to.
- `keyManager` contains information like host, port, scheme, context, proxy... of the key
manager which manages the key token of the bot.
- `ssl` contains the path to a file of concatenated CA certificates in PEM format. As we are using python SSL library
  under the hood, you can check
  [ssl lib documentation on certificates](https://docs.python.org/3/library/ssl.html#certificates) for more information.

  To fetch the cert file in pem format, you can run the following openssl command: `openssl s_client -connect <host>:<port> -showcerts > host.cert`
- `bot` contains information about the bot like the username, the private key for authenticating the service account
  on pod.
- `app` contains information about the extension app that the bot will use like
the appId, the private key for authenticating the extension app.
- `datafeed` contains information about the datafeed service that the bot will use for the `DatafeedLoop` service.
If the version field is configured to `v2`, the datafeed service v2 will be used. Otherwise, the datafeed service v1 
will be used by default.