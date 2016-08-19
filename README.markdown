cassandra-ca-manager
====================

Easily create Java [keystores](https://en.wikipedia.org/wiki/Keystore) with a
self-signed CA trust chain, for [Apache Cassandra](http://cassandra.apache.org) (and other Java applications).

Usage
-----

### Step 1.
Create a YAML-formatted manifest describing the certificate authority and
machine certificates.

```yaml
    # The top-level working directory
    base_directory: /path/to/base/directory

    # The Certificate Authority
    authority:
      key:
        size: 2048
      cert:
        subject:
          organization: WMF
          country: US
          unit: Services
        valid: 365
      password: qwerty

    # Java keystores
    keystores:
      - name: restbase1001-a
        key:
          size: 2048
        cert:
          subject:
            organization: WMF
            country: US
            unit: Services
          valid: 365
        password: qwerty

      - name: restbase1001-b
        key:
          size: 2048
        cert:
          subject:
            organization: WMF
            country: US
            unit: Services
          valid: 365
        password: qwerty
    
      - name: restbase1002-a
        key:
          size: 2048
        cert:
          subject:
            organization: WMF
            country: US
            unit: Services
          valid: 365
        password: qwerty
```

### Step 2.
Run the script with the manifest as its only argument:
    
    $ cassandra-ca-manager manifest.yaml
    $ tree /path/to/base/directory
    /path/to/base/directory
    ├── restbase1001-a
    │   ├── restbase1001-a.crt
    │   └── restbase1001-a.csr
    │   └── restbase1001-a.kst
    ├── restbase1001-b
    │   ├── restbase1001-b.crt
    │   └── restbase1001-b.csr
    │   └── restbase1001-b.kst
    ├── restbase1002-a
    │   ├── restbase1002-a.crt
    │   └── restbase1002-a.csr
    │   └── restbase1002-a.kst
    ├── rootCa.crt
    ├── rootCa.key
    ├── rootCa.srl
    └── truststore

    3 directories, 13 files

*Note: `cassandra-ca-manager` is idempotent, it will not overwrite any existing
material in the base directory.  To create additional keystores later, simply
add their definitions to the manifest, and re-run the script.*

### Step 3.
Copy the resulting truststore and *.kst files to their respective
machines, and configure [server](http://cassandra.apache.org/doc/latest/configuration/cassandra_config_file.html#server-encryption-options)
and/or [client](http://cassandra.apache.org/doc/latest/configuration/cassandra_config_file.html#client-encryption-options) encryption accordingly.
