# doomsday-boshrelease - A certificate expiry early warning system

Doomsday is a server (and also a CLI) which can be configured to track
certificates from different storage backends (Vault, Credhub, Pivotal Ops
Manager, or actual websites) and provide a tidy view into when certificates will
expire. Doomsday provides no automation for renewal - Doomsday simply provides
the information required for maintainers to take action.

## How Do I Deploy It?

A sample manifest is available at `manifests/doomsday.yml`. It contains example
configuration for secret backends, the doomsday server, and notifications. For a
more thorough configuration example, see the docs [here](https://github.com/doomsday-project/doomsday/blob/master/docs/ddayconfig.yml) on the doomsday repo.


After setting up your configuration in the manifest, use BOSH to deploy doomsday. 
```
bosh -e <env> -d doomsday deploy manifests/doomsday.yml
```

Once doomsday is running, you can view the WebUI at the IP address assigned to
the box after deployment, or assign a static IP in the manifest.

```
$ bosh -d doomsday vms
Using environment <env> as user 'admin'

Deployment 'doomsday'

Instance                                       Process State  AZ  IPs            VM CID                                   VM Type  Active
doomsday/29fd5a45-12c0-42a9-9701-246324b7e886  running        z1  10.128.0.2  vm-2137fdce-9074-47bd-8a2d-d1ba84c0e4ca  default  true

1 vms
```

![WebUI](https://s3.amazonaws.com/dingo-s3-544e680a-902c-44c1-ab35-94b372e1b39e/2019/05/image-1.png)


In addition to the WebUI, the doomsday cli can also be configured as follows to
give you a TUI dashboard.

```
$ doomsday target lab https://10.128.0.2:443
Creating target... Successfully created target

+-------------+----------------------------+
| NAME        | lab                        |
+-------------+----------------------------+
| ADDRESS     | https://10.200.255.47:443  |
+-------------+----------------------------+
| SKIP VERIFY | false                      |
+-------------+----------------------------+

$ doomsday login
Username: admin
Password:

Successfully authenticated to `lab'
```

In the ideal case, after doomsday has checked the configured backends and there
are no certs expiring soon, you should see the following.
```
$ doomsday dashboard
Could not find any certs which expire soon
```
If you happen to have expired or expiring certs however, you'll get a dashboard
warning you which certs are too far gone, and which you can still save ahead of
time.

![CLI](https://s3.amazonaws.com/dingo-s3-544e680a-902c-44c1-ab35-94b372e1b39e/2019/05/image.png)