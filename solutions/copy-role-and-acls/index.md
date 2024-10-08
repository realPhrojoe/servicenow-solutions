# Copy Role and ACLs

### Background Script

### Creator: [@ben-meeker](https://github.com/ben-meeker)

This is an example of a background script that copies a role, and it's acls into a new role. This is helpful when wanting to create a copy of a role with additional or restricted privileges (e.g. itil, without access to assets). It is important to keep in mind the impact ACL's have on performance when making these changes.
  
## Sample Code
```javascript
var referenceRole = 'itil' // reference role for cloning e.g. itil

var newRole = 'itil_restricted'; // new role to be cloned from reference role

// create a new role
var newRoleObj = new GlideRecord('sys_user_role'); 
newRoleObj.initialize();
newRoleObj.name = newRole;
var newRoleSysID = newRoleObj.insert();

// get all the ACLs for reference role
var aclAndRole = new GlideRecord('sys_security_acl_role'); 
aclAndRole.addQuery('sys_user_role.name',referenceRole);
aclAndRole.addQuery('sys_security_acl.active',true);
aclAndRole.query();

while(aclAndRole.next()){
    // create new ACLs for new Role
    var newACLs = new GlideRecord('sys_security_acl');   
    newACLs.initialize();
    // Set ACL field values to match
    newACLs.name = aclAndRole.sys_security_acl.name;
    newACLs.operation = aclAndRole.sys_security_acl.operation;
    newACLs.advanced = aclAndRole.sys_security_acl.advanced;
    newACLs.condition = aclAndRole.sys_security_acl.condition;
    newACLs.script = aclAndRole.sys_security_acl.script;
    var newACLSysID = newACLs.insert();

    // build a relation between new ACL and new Role so that new Role appears in related section for ACL
    var newACLAndRole = new GlideRecord('sys_security_acl_role');   
    newACLAndRole.initialize(); 
    newACLAndRole.sys_security_acl = newACLSysID;
    newACLAndRole.sys_user_role = newRoleSysID;
    newACLAndRole.insert();
}
```         