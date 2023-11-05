# vulnivore-upload

Scanning container image and push result to vulnivore server

## Example

```yaml
name: Build and Scan

on:
  push:
    branch:
      main

env:
  TAG_NAME: your-repo-name:${{ github.sha }}

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      id-token: write # Need to authenticate for vulnivore
    steps:
      - name: checkout
        uses: actions/checkout@v4
      - name: Set up Docker buildx
        uses: docker/setup-buildx-action@v2
      - name: Build Docker image
        run: docker build . -t ${{ env.TAG_NAME }}
      - name: Run Trivy
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: "image"
          image-ref: ${{ env.TAG_NAME }}
          format: "json"
          output: "trivy-results.json"

      - name: Upload Trivy results to Vulnivore
        uses: m-mizutani/vulnivore-upload@main
        with:
          filepath: trivy-results.json
          url: https://vulnivore-j47o6xodla-an.a.run.app/webhook/github/action/trivy
          installation_id: "XXXX" # Put your GitHub App Installation ID
```
