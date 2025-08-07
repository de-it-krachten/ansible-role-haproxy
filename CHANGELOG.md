# [1.9.0](https://github.com/de-it-krachten/ansible-role-haproxy/compare/v1.8.0...v1.9.0) (2025-08-07)


### Bug Fixes

* Make it possible to disable firewall management ([445500f](https://github.com/de-it-krachten/ansible-role-haproxy/commit/445500fe7c83664d8c2a7ea5e7b93b48cdb54b87))


### Features

* Add support for AlmaLinux 10 ([f1aee7e](https://github.com/de-it-krachten/ansible-role-haproxy/commit/f1aee7e167c7063efdc0369219019f3ab4a31e98))
* Add support for Debian 13 (Trixie) ([9990930](https://github.com/de-it-krachten/ansible-role-haproxy/commit/9990930564f97a400457f2ab1901560dcaeb9c7b))
* Add support for OracleLinux 10 ([8b756ee](https://github.com/de-it-krachten/ansible-role-haproxy/commit/8b756eebeed244a7b3bc3e72dd62329644dd7c43))
* Add support for Red Hat Enterprise Linux 10 ([acbcdb9](https://github.com/de-it-krachten/ansible-role-haproxy/commit/acbcdb98114ffe8383e9ffbdaa241e5d44889c80))
* Add support for RockyLinux 10 ([1dfadc0](https://github.com/de-it-krachten/ansible-role-haproxy/commit/1dfadc0b0d034d2381a7a6bc2de2416603aa9c20))

# [1.8.0](https://github.com/de-it-krachten/ansible-role-haproxy/compare/v1.7.0...v1.8.0) (2024-12-29)


### Features

* Update supported platforms & CI ([44f0cd9](https://github.com/de-it-krachten/ansible-role-haproxy/commit/44f0cd9b5032e4dc02418d7914961e0030fd2751))

# [1.7.0](https://github.com/de-it-krachten/ansible-role-haproxy/compare/v1.6.0...v1.7.0) (2024-12-28)


### Features

* Implemented logging via rsyslog ([f126e73](https://github.com/de-it-krachten/ansible-role-haproxy/commit/f126e738eb2239da03f433182c87137643b02516))

# [1.6.0](https://github.com/de-it-krachten/ansible-role-haproxy/compare/v1.5.0...v1.6.0) (2024-10-16)


### Bug Fixes

* reload service instead of restart ([085d7cd](https://github.com/de-it-krachten/ansible-role-haproxy/commit/085d7cdd92bc79ab0a84c584767ad2ce20d5cb21))


### Features

* Add SELinux boolean to allow listening on all ports ([2bcec3f](https://github.com/de-it-krachten/ansible-role-haproxy/commit/2bcec3f625224a883c4cbc02ae8c8636395f7229))
* support custom template + config location ([f2be56c](https://github.com/de-it-krachten/ansible-role-haproxy/commit/f2be56cb784c126a381856156adf961faa338748))

# [1.5.0](https://github.com/de-it-krachten/ansible-role-haproxy/compare/v1.4.0...v1.5.0) (2024-06-03)


### Features

* Add support for Ubuntu 24.04 LTS + Fedora 40 ([2ebcf83](https://github.com/de-it-krachten/ansible-role-haproxy/commit/2ebcf83b9605ae9ef745426c93e6672279364817))

# [1.4.0](https://github.com/de-it-krachten/ansible-role-haproxy/compare/v1.3.0...v1.4.0) (2023-09-08)


### Bug Fixes

* Fix loop label to string ([d26f0c1](https://github.com/de-it-krachten/ansible-role-haproxy/commit/d26f0c16cbfe2c95e14db6ead051bd0ffe0f5714))
* Remove initial support for SLES/OpenSUSE ([c6ad48f](https://github.com/de-it-krachten/ansible-role-haproxy/commit/c6ad48f885bb51fcea87134a05194649dc8e5632))


### Features

* Update supported platforms & CI ([3af06cc](https://github.com/de-it-krachten/ansible-role-haproxy/commit/3af06cc4c61c352eba2c36f59876255fc38fc8e4))

# [1.3.0](https://github.com/de-it-krachten/ansible-role-haproxy/compare/v1.2.0...v1.3.0) (2023-05-06)


### Bug Fixes

* Refactor molecule.yml.j2 to use default template ([58d6752](https://github.com/de-it-krachten/ansible-role-haproxy/commit/58d67520ee5d016695e12623092b39f097e1bbd6))


### Features

* Move to namespaced role names ([2e5aed0](https://github.com/de-it-krachten/ansible-role-haproxy/commit/2e5aed06900692a40de31e46aebc6546f079b7ac))

# [1.2.0](https://github.com/de-it-krachten/ansible-role-haproxy/compare/v1.1.0...v1.2.0) (2022-10-12)


### Features

* Move to FQCN ([a476f9c](https://github.com/de-it-krachten/ansible-role-haproxy/commit/a476f9cff8fa8c126a7bcfb82ecf211422aacf8b))
* Update CI to latest standards ([467d12f](https://github.com/de-it-krachten/ansible-role-haproxy/commit/467d12f8d6043fb6f486ec2ab8d2550541b10f6d))

# [1.1.0](https://github.com/de-it-krachten/ansible-role-haproxy/compare/v1.0.0...v1.1.0) (2022-07-28)


### Features

* Implement ansible-lint v6 support ([445fe8e](https://github.com/de-it-krachten/ansible-role-haproxy/commit/445fe8eecabdc404ace1a025440b7135f9d548ba))

# 1.0.0 (2022-05-16)


### Features

* Initial release ([cf1493c](https://github.com/de-it-krachten/ansible-role-haproxy/commit/cf1493cea2ebe1200776b6f7dedbce39b50e1cee))
