# Forgejo Setup Instructions

## Creating a runner from the CLI

1. Clone this repository to the folder `forgejo`:

    ```sh
    git clone https://github.com/jngo102/Forgejo-Setup.git forgejo
    ```

    1. Cloning to the default folder `forgejo-setup` will break the created network name under unless it is renamed in `config.yaml` under `container.network`
2. Create a `.env` file at the repository root
3. In the `.env` file, add the entry `DB_PASSWORD` with any value: `DB_PASSWORD=mypassword`
4. At the repository root, start the containers:

    ```sh
    docker compose up -d
    ```

    1. One or more services may fail, ignore this for now.
5. In the generated file at [`data/server/gitea/conf/app.ini`](./data/server/gitea/conf/app.ini), add the entry:

    ```conf
    [actions]
    DEFAULT_ACTIONS_URL = github
    ```

    to set the actions source to be GitHub, otherwise workflows will not be able to fetch any Actions and fail
6. In a browser, go to [localhost:3000](http://localhost:3000/)
7. Create an Administrator account at the bottom of the page and click the `Install Forgejo` button
8. Click the top right avatar to open up a context menu and click on `Site administration`
9. Navigate to `Actions > Runners` in the sidebar to the left.
10. Click on `Show registration token` and copy its value.
11. In a CLI, register the runner:

    ```sh
    docker compose run --rm runner forgejo-runner register --config /config.yaml
    ```

    1. Enter `http://server:3000` for the Forgejo instance URL
    2. Enter the copied registration token for the runner token
    3. Enter `forgejo-runner` for the runner name
12. Re-run docker compose:

    ```sh
    docker compose down; docker compose up -d
    ```

13. Verify that the `forgejo-runner` Actions runner has a `Status` of `Idle` under `Site administration > Actions > Runners`
14. Do not forget to add your SSH key at `~/.ssh/id_ed25519.pub` to Forgejo by clicking on your profile and navigating to `Settings > SSH / GPG keys`
