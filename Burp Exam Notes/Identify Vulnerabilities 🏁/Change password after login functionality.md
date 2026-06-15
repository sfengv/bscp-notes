## _**Change password after login functionality**_
- Weak isolation on dual-use endpoint 
	- (the current password functionality depends on the param being present, when it is not present the server doesn't check for the current pass when changing the password, the user whose password is changed is determined by the username parameter.)
- Password brute-force via password change


