# Autologic Packages

Manage RHEL packages and repositories from a single role.

## Version

1.0.1

## Work flow

- Define any repositories you need, such as internal mirrors or custom package repositories;
- Setup your software stacksto match your estate's software requirements;
- Use the "applied" variables to set groups and or hosts to use specific software;
- Do a little dance

## Variables

To define a new repository, you can use a basic list of hashes: ```autologic_software_repositories```. This can be very basic, like so:

```yaml
autologic_software_repositories:
  - name: 'My Company Repository'
    repositoryid: 'production_apps'
    baseurl: 'http://internal/rhel/6/'
    gpgcheck: false
  - name: 'External Repository'
    repositoryid: 'external'
    baseurl: 'http://external.url.example/rhel/'
  - name: 'EPEL'
    rpm: 'https://url/epel.rpm'
```

The 'name:' value is used in the ```autologic_applied_repositories``` list as a way of enabling repositories for a specific group of hosts:

```yaml
autologic_applied_repositories:
  - 'My Company Repository'
  - 'External Repository'
```

If the repository item in the dictionary has an 'rpm' key, then the remote RPM will be downloaded and installed (by YUM) automatically for you.

You can also take advantage of practically every available option a Yum repository file has to offer. Here is a selection of what's in the ```Yum.conf``` man page, but you can add any key:value entries you want as they're simply printed to out by the templating system. **No validaton is done.**

```yaml
autologic_software_repositories:
  - name: 'Super Custom Repository'
    enabled: true
    repositoryid: ""
    baseurl: ""
    metalink: ""
    mirrorlist: ""
    gpgcheck: true
    repo_gpgcheck: true
    exclude: []
    includepkgs: []
    keepalive: true
    timeout: 30
    retries: 3
    throttle: 0
    bandwidth: 0
    sslcacert: ""
    sslverify: true
    sslclientcert: ""
    sslclientkey: ""
    ssl_check_cert_permissions: true
    proxy: ""
    proxy_username: ""
    proxy_password: ""
    username: ""
    password: ""
    cost: 1000
    skip_if_unavailable: false
```

Once you have your custom repositories in place, if any, then you can begin defining "software stacks", which are used to apply groups of packages to your groups of hosts. Here is an example set of stacks:

```yaml
autologic_software_stacks:
  - name: "Static Content Cache"
    packages:
      - name: 'varnish'
        state: present
        version: '0.1.0'
      - name: 'nginx'
        state: present
        version: '0.1.0'

  - name: "Centralised Logging"
    packages:
      - name: 'elasticsearch'
        state: present
      - name: 'logstash'
        state: present
      - name: 'kibana'
        state: present
```

Now you're ready to apply repositories and software to your hosts:

```yaml
# group_vars/static_cache.yml
autologic_applied_repositories:
  - 'My Company Repository'

autologic_applied_stacks:
  - 'Static Content Cache'
```

```yaml
# group_vars/central_logging.yml
autologic_applied_repositories:
  - 'External Repository'

autologic_applied_stacks:
  - 'Centralised Logging'
```

The relevant repositories will be installed on the target system, as will all applicable software. This preps your systems and makes them ready to receive software configuration and service startup commands.

## Tests

See the 'tests' directory for further details.

## License

MIT.

## Author Information

- Michael Crilly
- Autologic Technology Ltd
- http://www.mcrilly.me/
