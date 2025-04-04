# API Based Deployment Toolkit With Ansible

A modular Ansible solution for authenticating and executing API requests with JSON payloads to deploy/configure various saas solutions.

## Project Structure

```text
deployment-configs/
├── playbook.yml               # Main playbook
├── variables.json             # API configuration
├── payloads/                 
│   └── endpoints          # Sample directory for JSON payloads for "create_endpoints" action
│       └── *.json         # API request payloads
├── authenticate.yml       # Authentication tasks
├── execute-api.yml        # API execution tasks
└── load-payloads.yml      # Payload loading tasks
```

## Key Features

- 🔒 JWT authentication flow
- 📦 Dynamic payload processing from JSON files
- 🔄 Modular task structure for easy maintenance
- 📊 Response validation and logging

## Quick Start

### Prerequisites
- Ansible 2.9+
- Python 3.6+

### 1. Clone the Repository
```bash
git clone https://github.com/mtrp12/bulk-deployment-with-ansible.git deployment-configs
cd deployment-configs
```

### 2. Configure API Settings
Edit `variables.json` with your API details:
```json
{
    "api_base_url": "https://example.com:8080",
    "api": {
        "authenticate": {
            "path": "/auth-url",
            "method": "POST",
            "headers": {
                "Content-Type": "application/json"
            },
            "body_format": "json",
            "success_codes": [200, 201],
            "username": "user1",
            "password": "pass1"
        }
    }
}
```

### 3. Add Payloads
Place your JSON payloads in a separate directory. Here in the `payloads/` directory, all the json payloads are placed in logically separated folders. For example, `payloads/endpoints/` directory contains payloads for `create_endpoints` action.

### 4. Run the Playbook
```bash
ansible-playbook playbook.yml \
  -e "@variables.json" \
  -e "payload_dir=payloads/endpoints/" \
  -e "action=create_endpoints"
```

## Available Actions

| Action            | Description                          | Example Command                     |
|-------------------|--------------------------------------|-------------------------------------|
| `create_endpoints` | Creates application endpoints       | `-e "action=create_endpoints"`      |
| `update_endpoints` | Updates existing endpoints          | `-e "action=update_endpoints"`      |
| `delete_endpoints` | Removes endpoints                   | `-e "action=delete_endpoints"`      |
| `list_endpoints`   | Retrieves endpoint listings         | `-e "action=list_endpoints"`        |

## Adding New Actions

1. Add the action configuration to `variables.json`:
```json
"api": {
  "new_action": {
    "path": "/new-endpoint",
    "method": "POST",
    "headers": {
      "Content-Type": "application/json"
    },
    "success_codes": [200, 201]
  }
}
```

2. Update the validation in `playbook.yml`:
```yaml
- name: validate_action
  assert:
    that: action in ['create_endpoints', 'new_action']  # Add new action here
```

## Configuration Reference

### variables.json
```json
{
  "api_base_url": "Base API URL",
  "api": {
    "[action_name]": {
      "path": "Endpoint path",
      "method": "HTTP method",
      "headers": {},
      "body_format": "json",
      "success_codes": []
    }
  }
}
```

### Environment Variables
| Variable         | Description                     | Required |
|------------------|---------------------------------|----------|
| `payload_dir`    | Path to JSON payload directory  | Yes      |
| `action`         | API action to execute           | Yes      |

## Best Practices

1. **Secret Management**: Use Ansible Vault for credentials
   ```bash
   ansible-vault encrypt variables.json
   ansible-playbook playbook.yml --ask-vault-pass
   ```

2. **Payload Organization**:
   ```
   payloads/endpoints/
   ├── create/
   ├── update/
   └── delete/
   ```

3. **Debugging**: Uncomment in `playbook.yml`:
   ```yaml
   - name: Show all variables
     debug:
       var: vars
   ```

## Troubleshooting

| Error | Solution |
|-------|----------|
| Authentication failed | Verify credentials in `variables.json` |
| Invalid status code | Check `success_codes` for the action |
| Missing payload | Ensure JSON files exist in `payload_dir` |

## License
MIT

## Key Features of This README:

1. **Future-Proof Action System**: The table format makes it easy to add new actions by:
   - Adding to the `variables.json` config
   - Updating the validation list in `playbook.yml`

2. **Modular Design**: Clear separation between:
   - Authentication
   - Payload loading
   - API execution

3. **Extensibility Guidance**: Includes instructions for:
   - Adding new actions
   - Organizing payloads
   - Secret management

4. **Comprehensive Reference**: Documents all:
   - Configuration options
   - Command syntax
   - Error scenarios

The structure allows you to easily add future actions like `update_endpoints` or `list_resources` by following the established pattern.
