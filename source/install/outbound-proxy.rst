..  _outbound_proxy:

Using an Outbound Proxy
=======================

In some scenarios, you may wish to use Mattermost behind a proxy. This can be used to do things such as monitoring outbound traffic from Mattermost or controlling which websites can appear in link previews and other embedded content.

Configuration
-------------

Mattermost's use of a proxy is configured using the ``HTTP_PROXY``, ``HTTPS_PROXY`` and ``NO_PROXY`` environment variables.

``HTTP_PROXY`` and ``HTTPS_PROXY``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``HTTP_PROXY`` and ``HTTPS_PROXY`` environment variables store the address of the proxy server for HTTP and HTTPS requests respectively. This value should include the protocol and port of the proxy such as ``http://192.168.4.5:3128``.

If you wish to have Mattermost authenticate with your proxy, it supports HTTP basic authentication by specifying the address of the proxy such as ``http://mattermost:password@192.168.4.5:3128``.

Note that when proxying HTTPS resources, you will need to configure your Mattermost server with the root certificate of the proxy. Otherwise, Mattermost will refuse any response from the proxy as it will detect that its connection has been intercepted.

``NO_PROXY``
~~~~~~~~~~~~

The ``NO_PROXY`` environment variable can be set to prevent certain requests from going through the proxy, such as to an SSO provider (such as GitLab or SAML) or to intranet sites that should be accessible in link previews. It can be configured as a set of comma-separted IP addresses (e.g. 1.2.3.4), IP address ranges specified in CIDR notation (e.g. 1.2.3.4/8), or domain names. An IP address or domain name can also include a port number.

When specifying a domain name, that domain name also matches its sub domains as well. A domain name with a leading ``.`` matches only sub domains. For example, ``example.com`` matches both ``example.com`` as well as ``sub.example.com`` while ``.example.com`` only matches the latter.

Sample Configuration
--------------------

To set these environment variables while running the Mattermost server via ``systemd``, modify the ``mattermost.service`` like this:

  .. note::
    Be sure to replace `127.0.0.1:3128` with the correct values for your proxy servers.

  .. code-block:: none

    [Unit]
    Description=Mattermost
    After=network.target
    After=postgresql.service
    Requires=postgresql.service

    [Service]
    Type=notify
    ExecStart=/opt/mattermost/bin/mattermost
    TimeoutStartSec=3600
    Restart=always
    RestartSec=10
    WorkingDirectory=/opt/mattermost
    User=mattermost
    Group=mattermost
    LimitNOFILE=49152
    Environment=HTTP_PROXY=http://mattermost:password@127.0.0.1:3128
    Environment=HTTPS_PROXY=https://mattermost:password@127.0.0.1:3128
    Environment=NO_PROXY=1.2.3.4:567,.internal.example.com,login.example.com

    [Install]
    WantedBy=postgresql.service
    