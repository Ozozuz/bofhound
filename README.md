# Inherited ACL parsing fix

This modification fixes missing BloodHound permission edges when an LDAP dump includes security descriptors but omits AD schema objects.

Previously, an inherited ACE restricted to a class such as `User` could be silently skipped because BOFHound could not resolve its `InheritedObjectType` GUID from the empty schema map. Valid `ForceChangePassword`, `WriteDacl`, and `GenericWrite` edges could therefore disappear from the output.

The parser now uses standard AD class GUIDs as a fallback for User, Computer, Group, OU, Domain, GPO, and Container. Collected schema mappings take precedence, class mismatches remain excluded, and unknown classes still require schema data. The fix also excludes `INHERIT_ONLY` ACEs from permissions on the current object, whether the ACE is inherited or explicit.

Validation: 23 synthetic regression cases and 90 passing tests in the full local suite. The three regression cases for missing user-class schema mappings fail against the original parser and pass with the fix.

Upstream pull request: [coffeegist/bofhound #65](https://github.com/coffeegist/bofhound/pull/65).
