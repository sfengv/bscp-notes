
These labs deals with privilege escalation so it's in stage 2.

### Identify Vulnerabilities in Exam
#### Stage 2
- Decoded `session` cookie = `0:4` = PHP serialized object
	- See `b:0` in serialized object
		- **Solution:** change `b:0` to `b:1`
		- [[Deserialization#3.1. Lab Modifying serialized objects ⭕️]]
	- See `"access_token"` in serialized object
		- **Solution:** `O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";i:0;}`
		- [[Deserialization#3.2. Lab Modifying serialized data types ⭕️]]


