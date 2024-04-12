# Python client for Teal API

1. Download open api generator client - https://openapi-generator.tech/docs/installation
2. Run following from docs root (client may change depending on installation used). This will generate the api client and model:

    `openapi-generator generate -i api-reference/openapi.yml -g python -o <path-to-teal-docs-root>/generator/python `


3. Navigate to `<path-to-teal-docs-root>/generator/python` and install dependencies:
    ```
    python3 -m venv venv
    . venv/bin/activate
    pip3 install -r requirements.txt 
    ```

    This will start a virtual env.

4. Change `client_api_key` and `client_name` in `populate_sandbox_for_client.py`. Alter the host depending on needs.

5. Run `python3 populate_sandbox_for_client.py`. The created identifiers will be printed in the response.