# Developer Guide to deploying Datahub on GKE

Guide referenced from
    
  * https://datahubproject.io/docs/deploy/gcp/#setup-datahub-using-helm
  * https://cloud.google.com/artifact-registry/docs/helm/store-helm-charts

## Creating and connecting to GKE cluster

1. Create GKE cluster

2. Connect to cluster

    ```bash
    gcloud container clusters get-credentials CLUSTER_NAME
    ```

## Authenticating local Helm with Artifact Registry

1. Create a repository for charts on Artifact Registry

2. Choose one of four methods to authenticate - through gcloud credential helper, Standalone Docker credential helper, Access token or a JSON key file
    * Following steps are for authenticating through 'gcloud credential helper' method

3. Create a Service account and key on 'IAM & Admin'
    * Key comes in the form of a `.json` file downloaded to your local desktop

4. Configure authentication with service account credentials
    * Credentials are saved in `$HOME/.docker/config.json`

    ```
    gcloud auth activate-service-account --key-file=/home/tohce/Documents/git-repos/datahub-helm/gke-keys/datahub-poc-367105-7007132e79fe.json

    gcloud auth configure-docker asia-southeast1-docker.pkg.dev

    gcloud auth print-access-token | docker login -u oauth2accesstoken --password-stdin https://asia-southeast1-docker.pkg.dev
    ```

## Storing Helm charts in Artifact Registry


1. cd to `/charts` directory

2. Package charts into archives

    ```bash
    # helm package <FILEPATH TO DIR WITH Chart.yaml>
    helm package datahub-prerequisites
    helm package datahub
    ```

3. Login to Artifact Registry

    ```bash
    gcloud auth print-access-token | docker login -u oauth2accesstoken --password-stdin https://asia-southeast1-docker.pkg.dev
    ```

4. Push charts to registry
    * This can take a while to run.

    ```bash
    helm push datahub-prerequisites-0.0.10.tgz oci://asia-southeast1-docker.pkg.dev/datahub-poc-367105/gke-datahub-poc
    helm push datahub-0.2.110.tgz oci://asia-southeast1-docker.pkg.dev/datahub-poc-367105/gke-datahub-poc
    ```

5. Verify that charts have been pushed to registry

    ```bash
    gcloud artifacts docker images list asia-southeast1-docker.pkg.dev/datahub-poc-367105/gke-datahub-poc
    ```

# Other

Common commands:

```
gcloud auth login
```