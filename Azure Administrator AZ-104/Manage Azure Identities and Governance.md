==Azure AD==: Azure **Active Directory**

## Generics
Azure AD is multi-tenant
- All customers (tenants) are in one huge directory
- Your tenant is private to you
Azure AD is primarily and *identity management directory*
- Mostly used for user login authentication
- Azure resources are secured using Azure AD accounts
Azure AD is a *trusted provider*
- Uses internet security protocols: OpenID, OAuth, SAML
- Can simplify single sign-on (SSO) for cloud apps
Can synchronize with on-prem Active Directory Domain Services (**AD DS**)


### Azure AD Custom Domains
Before adding users, add Custom Domains:
- these are domains you own
- you must add an entry to DNS zone to **prove ownership**
User IDs can the end in that domain:
- Default domain is `jacklab.onmicrosoft.com` but since I own `jacklab.dev` I can use something like e.g `giacomino@jacklab.dev`
### Azure AD Users
Add **User** account for employees
- Choose a domain suffix from custom domains
- Set properties as desired
Add **Guest** accounts for *non-employees*
- They are sent an email  invitation
- Guests can belong to any security role
### Azure AD Groups
Add **Group** account to organize users by department, job title, etc.
- Users and Guests can be in multiple groups
- Resource access can be assigned to Users and Groups
- Each Group has an owner who can change the membership to a group (for Security Groups)

**Group Type**
- ==Security Groups== - used to secure resources (you can add users, groups, devices as *members*)
	- *assignment* can be **static** or **dynamic**
- ==Microsoft 365 Groups== - used for shared mailbox, calendar and SharePoint collection, etc.

### Group Membership Types
1. **Assigned** - admins specifically add users and groups (static)
2. **Dynamic User** or **Dynamic Device** - requires Azure AD Premium P1