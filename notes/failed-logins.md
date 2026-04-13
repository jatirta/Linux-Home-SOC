# Failed Login Attempts

## What I did
I generated failed login attempts by entering incorrect credentials using local authentication and/or SSH.

## What I observed
The `/var/log/auth.log` file recorded multiple failed authentication attempts, including:
- "Failed password" entries
- Targeted usernames
- Timestamps of each attempt

## Example command used
```bash
sudo grep "Failed password" /var/log/auth.log
