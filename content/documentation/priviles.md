---
title: 'Privileges'
weight: 140
---

## Privileges

No framework is complete without user management and privilege validation. The
basic form would be Users and Roles, and then validating that an authenticated
user has a given role. In Strolch we go a step further: A User has roles
assigned, and each role has a set of Privileges. The privileges can overlap, a
validation is performed to make sure that the one role doesn't deny and another
role allows a specific action.

As explained in
the [Privilege Configuration](/documentation/runtime-configuration.md) section,
users are defined in the `PrivilegeUsers.xml` file, and roles are defined in the
`PrivilegeRoles.xml` file.

Let's assume the following user and role definition:

```xml
<Users>
  <User userId="1" username="jill" password="$PBKDF2WithHmacSHA512,10000,256$61646d696e$cb69962946617da006a2f95776d78b49e5ec7941d2bdb2d25cdb05f957f64344">
    <Firstname>Jill</Firstname>
    <Lastname>Someone</Lastname>
    <State>ENABLED</State>
    <Locale>en-GB</Locale>
    <Roles>
      <Role>AppUser</Role>
    </Roles>
    <Properties>
      <Property name="realm" value="execution" />
    </Properties>
  </User>
</Users>
```

```xml
<Roles>
  <Role name="AppUser">
    <Privilege name="li.strolch.service.api.Service" policy="DefaultPrivilege">
      <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="li.strolch.model.query.StrolchQuery" policy="DefaultPrivilege">
      <AllAllowed>true</AllAllowed>
    </Privilege>
    <Privilege name="li.strolch.search.StrolchSearch" policy="DefaultPrivilege">
      <AllAllowed>true</AllAllowed>
    </Privilege>
  </Role>
</Roles>
```

This configuration contains one user and one role. The user `jill` has the role
`AppUser` and the role `AppUser` has three privileges assigned.

Note how the user's password is configured similar to a unix password
definition: Using the dollar sign & first the hashing algorithm is configured (
algorithm, iterations, key length), then the salt, followed by the password
hash.

{{% notice tip %}}
Note: The password can also still be saved using two separate fields: a salt and
password field. This configuration will be immediately changed to the unix form,
so won't be described further here.
{{% /notice %}}

Further a user always has a firstname and last name. Optionally a locale can be
set, otherwise the system locale is used. The user's state must be defined as
one of `NEW`, `ENABLED`, `DISABLED`, `EXPIRED` or `SYSTEM`. A user can only
authenticate/login with the state `ENABLED`. A user can have any number of
properties, which can then be used at runtime. A user can also reference any
number of roles, the assigned privilege can overlap, a global configuration mode
defines how duplicate privileges are handled.

Roles have a name and any number of `Privilege` definitions. A Privilege has a
name, which in many cases is the name of Java class/interface on which an action
is being invoked. The `policy` value defines which policy to use when evaluating
the privilege access. The privilege definition is defined in the
`PrivilegeConfig.xml` and is the name of a class to call for privilege validation.

Further the privilege definitions can have a `AllAllowed` boolean flag, or any
number of Allow or Deny values. How these values are interpreted is defined in
the policy implementation. A policy takes three input parameters:

* `PrivilegeContext` → supplied by privilege and gives access to the Certificate,
  thus identifying the user for which privilege access is to be validated.
* `IPrivilege` → Contains the privilege values: `AllAllowed`, `Allow` and `Deny`
* `Restrictable` → An interface from which the privilege name is retrieved, and
  the associated value. The value is an object, and is cast to the relevant
  input in the concrete privilege policy.

The following privilege policies are already implemented:

* `DefaultPrivilege` → simple policy where the passed `Restrictable` is expected to
  return a String value, which is compared with allow and deny values.
* Internal: `RoleAccessPrivilege` → policy used for the internal privileges
  `PrivilegeGetRole`, `PrivilegeAddRole`, `PrivilegeModifyRole` or `PrivilegeModifyRole`
* Internal: `UserAccessPrivilege` → policy used for the internal privileges
  `PrivilegeGetUser`, `PrivilegeAddUser`, `PrivilegeRemoveUser`, `PrivilegeModifyUser`,
  `PrivilegeAddRoleToUser`, `PrivilegeRemoveRoleFromUser`, `PrivilegeSetUserState`,
  `PrivilegeSetUserLocale` or `PrivilegeSetUserPassword`
* Internal: `UserAccessWithSameOrganisationPrivilege` → Same as the
  `UserAccessPrivilege` but expects the authenticated user to have a property
  `organisation` and validates that the user being modified is in the same
  organisation.
* Internal: `UsernameFromCertificatePrivilege` → This policy expects a
  `Restrictable` to return the `certificate` of another authenticated user and is
  used when modifying an authenticated user, i.e. killing a session, or
  modifying its current state, e.g. locale etc.
* Internal: `UsernameFromCertificateWithSameOrganisationPrivilege` → Same as
  `UsernameFromCertificatePrivilege` but expects the authenticated user to have a
  property `organisation` and validates that the user being modified is in the
  same organisation.

{{% notice tip %}}
Note: As a rule, the sequence is `AllAllowed → Allow → Deny → default deny`
{{% /notice %}}
