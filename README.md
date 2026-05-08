# anubis_ddocs
Anubis rules to block and challenge some bots

## Apache2 bot policies

Edit the file:
```bash
sudo nano /etc/apache2/botPolicies.yaml
```

Restart Anubis:
```bash
sudo systemctl restart anubis@apache2
```

Restart Apache2:
```bash
sudo systemctl restart apache2
```



Verify the configuration:
```bash
sudo anubisctl verify
```
````
python3 -c 'import yaml, sys; yaml.safe_load(sys.stdin)' < botPolicies.yaml
```