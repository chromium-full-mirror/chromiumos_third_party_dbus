# dbus security

## Reporting security vulnerabilities

If you find a security vulnerability that is not known to the public,
please report it privately to <dbus-security@lists.freedesktop.org>,
or by reporting a Gitlab issue at
<https://gitlab.freedesktop.org/dbus/dbus-python/-/work_items/new>
and marking it as "confidential".

If you are unsure whether a bug is a security vulnerability or not,
please report it as confidential.

Proposed fixes can be attached to the issue using the output of
`git format-patch`.

**Please do not create a merge request for undisclosed security vulnerabilities,
or push proposed changes fixing an undisclosed security vulnerability
to any public location.**

## CVE IDs

If you allocate a CVE ID or similar unique identifier for a vulnerability,
please leave a comment with the CVE ID on the relevant issue report,
or create a new issue report if necessary
(marked as confidential if the vulnerability is not yet known to the public).
The dbus maintainers cannot provide support for CVE IDs that are allocated
without their knowledge.

## Scope

### Operating systems

From a security point of view,
dbus is primarily maintained for GNU/Linux.

dbus aims to be portable to other Unix operating systems such as FreeBSD,
but maintainers do not routinely test on non-Linux operating systems and
OS-specific issues for non-Linux operating systems are likely to be treated
as a lower priority.

dbus is also portable to Windows,
but we do not currently consider dbus on Windows to be security-supported.

### Authentication mechanisms

Contexts where dbus is acting as a security boundary should use the
`EXTERNAL` authentication mechanism.

The `DBUS_COOKIE_SHA1` authentication mechanism is not recommended.

The `ANONYMOUS` authentication mechanism does not provide any security
and cannot be used where a security boundary is expected.

`dbus-daemon` instances where a security boundary is required should use
`<auth>EXTERNAL</auth>` to disable use of any other authentication mechanism.
`DBusServer` instances where a security boundary is required should call:

```c
const char *only_external[] = { "EXTERNAL", NULL };
dbus_server_set_auth_mechanisms(server, only_external);
```

### TCP transports

The `tcp:` and `nonce-tcp:` transports are based on TCP streams,
without any integrity or confidentiality protection:
they should be assumed to have security characteristics similar to
unencrypted HTTP.

The TCP-based transports are provided for compatibility with older
versions of Windows that did not implement `AF_UNIX` sockets.
They should not be used on Unix systems.

These TCP-based transports do not defend against denial of service
by local users, and therefore should not be used on multi-user systems.
