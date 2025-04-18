# How to identify required permissions for Jamf Pro API Roles and Clients.

---
### Option 1: Using Swagger Documentation
Jamf Pro's built-in Swagger documentation lists the required permissions for each endpoint clearly under the `x-required-privileges` field.

**Access Swagger Documentation:**
- Visit your instance’s API schema at:
  - Example: [https://overview.jamfcloud.com/api/doc](https://overview.jamfcloud.com/api/doc)

The permissions are located at the bottom of each API endpoint definition.

---

### Option 2: Automating Permissions Extraction
To automate identifying these permissions, use the following script:

🔗 [JAPI-Privilege-Checker.sh](https://github.com/paullstanley/JamfAPI-PrivilegesChecker/blob/main/JAPI-Privilege-Checker.sh)

**Script Functionality:**
- Fetches the API schema directly from your Jamf Pro instance.
- Parses endpoints for the `x-required-privileges` field.
- Outputs a consolidated list of endpoints and their required permissions.

---

### How to Use the Script

1. **Clone or download the script locally**:
    ```bash
    git clone https://github.com/paullstanley/JamfAPI-PrivilegesChecker.git
    ```

2. **Ensure required dependencies** (`curl`, `jq`, and `bash`) are installed:
    ```bash
    # For macOS (using Homebrew)
    brew install curl jq

    # For Linux (Debian/Ubuntu)
    sudo apt-get install curl jq
    ```

3. **Make the script executable**:
    ```bash
    chmod +x JAPI-Privilege-Checker.sh
    ```

4. **Run the script with your Jamf instance URL and API token**:
    ```bash
    ./JAPI-Privilege-Checker.sh -u https://yourcompany.jamfcloud.com -t your_api_token
    ```

5. **Review the output**:
   The script will generate a comprehensive list mapping each API endpoint to its required privileges.

---

### Additional Reference
Jamf maintains a complete permissions mapping reference for both the Classic and newer APIs here:

🔗 [Jamf Pro API Permissions Mapping](https://developer.jamf.com/jamf-pro/docs/classic-api-minimum-required-privileges-and-endpoint-mapping)

---

Use the above tools and references in tandem to verify access requirements and confidently configure your Jamf API Roles and Clients.
