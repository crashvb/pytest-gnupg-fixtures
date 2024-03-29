# pytest-gnupg-fixtures

[![pypi version](https://img.shields.io/pypi/v/pytest-gnupg-fixtures.svg)](https://pypi.org/project/pytest-gnupg-fixtures)
[![build status](https://github.com/crashvb/pytest-gnupg-fixtures/actions/workflows/main.yml/badge.svg)](https://github.com/crashvb/pytest-gnupg-fixtures/actions)
[![coverage status](https://coveralls.io/repos/github/crashvb/pytest-gnupg-fixtures/badge.svg)](https://coveralls.io/github/crashvb/pytest-gnupg-fixtures)
[![python versions](https://img.shields.io/pypi/pyversions/pytest-gnupg-fixtures.svg?logo=python&logoColor=FBE072)](https://pypi.org/project/pytest-gnupg-fixtures)
[![linting](https://img.shields.io/badge/linting-pylint-yellowgreen)](https://github.com/PyCQA/pylint)
[![code style](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)
[![license](https://img.shields.io/github/license/crashvb/pytest-gnupg-fixtures.svg)](https://github.com/crashvb/pytest-gnupg-fixtures/blob/master/LICENSE.md)

## Overview

Pytest fixtures to dynamically create [GnuPG](https://www.gnupg.org/) instances for testing.

## Getting Started

Update <tt>setup.py</tt> to include:

```python
from distutils.core import setup

setup(
	tests_require=["pytest-gnupg-fixtures"]
)
```

All fixtures should be automatically included via the <tt>pytest11</tt> entry point.
```python
from pytest_gnupg_fixtures import GnuPGKeypair  # Optional, for typing

def test_custom_signer(gnupg_keypair: GnuPGKeypair):
    custom_signer = CustomSigner(
        keyid=gnupg_keypair.fingerprints[1],
        passphrase=gnupg_keypair.passphrase,
        homedir=gnupg_keypair.gnupg_home,
    )
    assert "PGP SIGNATURE" in custom_signer.sign("my data")
```

## Installation
### From [pypi.org](https://pypi.org/project/pytest-gnupg-fixtures/)

```
$ pip install pytest_gnupg_fixtures
```

### From source code

```bash
$ git clone https://github.com/crashvb/pytest-gnupg-fixtures
$ cd pytest-gnupg-fixtures
$ virtualenv env
$ source env/bin/activate
$ python -m pip install --editable .[dev]
```

## Fixtures

### <a name="gnupg_email"></a> gnupg_email

Provides a generated email to use as part of the uid for the keypair.

### <a name="gnupg_gen_key_conf"></a> gnupg_gen_key_conf

Provides the path to a templated GnuPG script file that is used to generate a temporary keypair. If a user-defined script (<tt>tests/gnupg_gen_key.conf</tt>) can be located, it is used. Otherwise, an embedded script template is copied to temporary location and returned. This fixture is used by the [gnupg_keypair](#gnupg_keypair) fixture.

The`$GNUGP_EMAIL` and `$GNUPG_PASSPHRASE` variables will be populated within the template during generation of the keypair.

### <a name="gnupg_keypair"></a> gnupg_keypair

Provides a keypair within a temporary GnuPG trust store.

#### NamedTuple Fields

The following fields are defined in the tuple provided by this fixture:

* **email** - from [gnupg_email](#gnupg_email)
* **fingerprints** - A list of key fingerprints. Typically: pubkey, subkey...
* **gnupg_home** - from [gnupg_trust_store](#gnupg_trust_store)
* **keyid** - The public key id of the temporary keypair.
* **gen_key_conf** - from [gnupg_gen_key_conf](#gnupg_gen_key_conf)
* **passphrase** - from [gnupg_passphrase](#gnupg_passphrase)
* **script** - The path to the instantiated script from [gnupg_gen_key_conf](#gnupg_gen_key_conf)
* **uids** A list of uids existing within the trust store.

Typing is provided by `pytest_gnupg_fixtures.GnuPGKeypair`.

### <a name="gnupg_passphrase"></a> gnupg_passphrase

Provides a generated passphrase to use for the keypair.

### <a name="gnupg_trust_store"></a> gnupg_trust_store

Provides a temporary, initialized, GnuPG trust store that is contains no keys. This fixture is used by the [gnupg_keypair](#gnupg_keypair) fixture.

#### NamedTuple Fields

The following fields are defined in the tuple provided by this fixture:

* **gnupg_home** - The path to the temporary trust store.

Typing is provided by `pytest_gnupg_fixtures.GnuPGTrustStore`.

## <a name="limitations"></a>Limitations

1. This has been coded to work with gpg2.
2. The generated keypair is very simple. TBD if this will be expanded to support a more realistic configuration.

## Development

[Source Control](https://github.com/crashvb/pytest-gnupg-fixtures)
