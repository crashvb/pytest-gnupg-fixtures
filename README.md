# pytest-gnupg-fixtures

## Overview

Pytest fixtures to dynamically create [GnuPG](https://www.gnupg.org/) instances for testing.

## Getting Started

Update <tt>setup.py</tt> to include:

```python
setup(
	tests_require=["pytest-gnupg-fixtures"]
)
```

All fixtures should be automatically included via the <tt>pytest11</tt> entry point.
```python
import pytest
from pytest_docker_registry_fixtures import DockerRegistryInsecure, DockerRegistrySecure  # Optional, for typing

@pytest.mark.push_image("busybox:1.30.1", "alpine")
def test_docker_registry_secure(docker_registry_secure: DockerRegistrySecure):
    response = requests.head(f"https://{docker_registry_secure.endpoint}/v2/",
        headers=docker_registry_secure.auth_header,
        verify=str(docker_registry_secure.cacerts),
    )
    assert response.status_code == 200

def test_docker_registry_insecure(docker_registry_insecure: DockerRegistryInsecure):
    response = requests.head(f"http://{docker_registry_insecure.endpoint}/v2/")
    assert response.status_code == 200
```

The `push_image` mark can optionally be added to stage images in the registry prior to testing. See [Markers](#markers) for details.
## Compatibility

* Tested with python 3.8

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

### <a name="docker_compose_insecure"></a> docker_compose_insecure

This fixtures uses the `docker_compose_files` fixture to locate a user-defined docker-compose configuration file (typically <tt>tests/docker-compose.yml</tt>) that contains the <tt>pytest-docker-registry-insecure</tt> service. If one cannot be located, an embedded configuration is copied to a temporary location and returned. This fixtures is used to instantiate the insecure docker registry service.

#### NamedTuple Fields

The following fields are defined in the tuple provided by this fixture:

* **docker_client** - from [docker_client](#docker_client)
* **docker_compose** - Path to the fully instantiated docker-compose configuration.
* **endpoint** - Endpoint of the insecure docker registry service.
* **images** - List of images that were replicated into the insecure docker registry service.
* **service_name** - Name of the service within the docker-compose configuration.

Typing is provided by `pytest_docker_registry_fixtures.DockerRegistryInsecure`.

## <a name="limitations"></a>Limitations

1. All of the fixtures provided by this package are <tt>session</tt> scoped; and will only be executed once per test execution. This allows for a maximum of two docker registry services: one insecure instance and one secure instance.
2. The `push_image` marker is processed as part of the `docker_registry_insecure` and `docker_registry_secure` fixtures. As such:
  * _all_ markers will be aggregated during initialization of the session, and processed prior test execution.
  * Pushed images will be replicated to both the insecure and secure docker registries, if both are instantiated.
3. A working docker client is required to push images.

## Changelog

### 0.1.0 (2021-01-05)

* Initial release.

## Development

[Source Control](https://github.com/crashvb/pytest-gnupg-fixtures)
