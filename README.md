# Run the main playbook against all of the rpis

```
ansible-playbook playbook-update-pis.yaml -i rpis --ask-become-pass
```

# Run a command against all rpis

Uncomment and amend the the 'Run an arbitrary shell command' task in tasks/main.yml and then run:

```
ansible-playbook playbook-update-pis.yaml -i rpis --ask-become-pass -t arb_command
```

