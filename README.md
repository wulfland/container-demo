# Creating and publishing a docker image to `ghcr.io`

1. Create a new repsitory `container-demo` and add a new file namend Dockerfile (without extension). Add the following content:

    ```
    FROM alpine
    CMD ["echo", "Hello World!"]
    ```
    
    If you want to test this locally, clone your repository and change directory to it. Build and run the image. The output is `Hello World!`:
    
    ```
    $ docker build -t container-demo .
    $ docker run --rm container-demo
    > Hello World!
    ```
2. Create a workflow file `.github/workflows/release-container.yml` with the following content and commit/push it to your repository:

    ```
    name: Publish Docker image

    on:
      release:
        types: [published]

    env:
      REGISTRY: ghcr.io
      IMAGE_NAME: ${{ github.repository }}

    jobs:
      build-and-push-image:
        runs-on: ubuntu-latest
        permissions:
          contents: read
          packages: write

        steps:
          - name: Checkout repository
            uses: actions/checkout@v2

          - name: Log in to the Container registry
            uses: docker/login-action@f054a8b539a109f9f41c372932f1ae047eff08c9
            with:
              registry: ${{ env.REGISTRY }}
              username: ${{ github.actor }}
              password: ${{ secrets.GITHUB_TOKEN }}

          - name: Extract metadata (tags, labels) for Docker
            id: meta
            uses: docker/metadata-action@98669ae865ea3cffbcbaa878cf57c20bbf1c6c38
            with:
              images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}

          - name: Build and push Docker image
            uses: docker/build-push-action@ad44023a93711e3deb337508980b4b5e9bcdc5dc
            with:
              context: .
              push: true
              tags: ${{ steps.meta.outputs.tags }}
              labels: ${{ steps.meta.outputs.labels }}
    ```
    
3.     
    
