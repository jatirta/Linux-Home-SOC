# 📄 `sudo-activity.md`

```markdown
# Privilege Escalation (sudo Activity)

## What I did
I executed administrative commands using `sudo`, including creating a new user account.

## What I observed
The `/var/log/auth.log` file recorded:
- The user executing `sudo`
- The commands being run
- Timestamps of execution

## Example command used
```bash
sudo grep "sudo" /var/log/auth.log
