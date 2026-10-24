# Forgejo Setup Instructions

## Creating a runner from the CLI

1. Clone this repository to the folder `forgejo`:

    ```sh
    git clone https://codeberg.org/JngoCreates/Forgejo-Setup.git forgejo
    ```

    1. Cloning to the default folder `forgejo-setup` will break the created network name under unless it is renamed in `config.yaml` under `container.network`
2. Create a `.env` file at the repository root:

    ```sh
    cd forgejo && vi .env
    ```

3. In the `.env` file, add the entry `DB_PASSWORD` with any value: `DB_PASSWORD=mypassword`
4. At the repository root, start the containers:

    ```sh
    docker compose up -d
    ```

    1. One or more services may fail, ignore this for now.
5. Open the generated server configuration file with the command

    ```sh
    docker exec -it forgejo-server vi /data/gitea/conf/app.ini
    ```

    1. If you want to use actions hosted on GitHub instead of Forgejo's own actions, add the following lines:

        ```ini
        [actions]
        DEFAULT_ACTIONS_URL = github
        ```

        otherwise you will need to explicitly prepend `https://github.com/` to every action name.

    2. If you also want to enable Packages, e.g. container registries, add the following lines to the `app.ini` file:

        ```ini
        [packages]
        ENABLED = true
        ```

    3. To get Forgejo's own `upload-artifact` action to work, change the `DOMAIN` and `ROOT_URL` entries to use a reachable hostname or IP address, such as your LAN address, e.g.:

        ```ini
        [server]
        DOMAIN = 192.168.50.1
        ROOT_URL = http://192.168.50.1:3000/
        ```

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
    docker compose down && docker compose up -d
    ```

13. Verify that the `forgejo-runner` Actions runner has a `Status` of `Idle` under `Site administration > Actions > Runners`
14. Do not forget to add your SSH key at `~/.ssh/id_ed25519.pub` to Forgejo by clicking on your profile and navigating to `Settings > SSH / GPG keys`
