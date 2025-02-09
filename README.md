# Local Https Devcontainer Example

This repo serves as an example of how to run your local web server over https by reverse proxying the requests through [Caddy](https://caddyserver.com/). By reverse proxying, the requests, this solution is agnostic of your tech stack. In this example, I'm running a nextjs server, but this approach could be used for any other web server as well.

This app was built in the following steps:
1. [Generating a base node devcontainer](https://github.com/willwill96/devcontainer-https-example/commit/0e420645997f46ef39abce932f2391cb01fdf5bc)
2. [Converting the devcontainer config to run through docker-compose](https://github.com/willwill96/devcontainer-https-example/commit/a18c8efc9fb77d7c4fc9a77e4b58e99e7a43995d)
3. [Generating a next.js app with npx create-next-app](https://github.com/willwill96/devcontainer-https-example/commit/0aa5bf041c3e31a4955a0bf11db45c49033dead1)
4. [Adding Caddy Server to reverse proxy over https](https://github.com/willwill96/devcontainer-https-example/commit/e9c2269b051bd49f304f4a81e6708be7da9d3ce4)

## Running 

The recommended way to run this project is via a devcontainer (either [vs code](https://code.visualstudio.com/docs/devcontainers/containers) or [intellij](https://www.jetbrains.com/help/idea/connect-to-devcontainer.html)).


Once the devcontainer is up and running, you should be able to visit the app on https://localhost:3000


# Taking it a step further by modifying your hosts file

You can further mirror your production environment by spoofing your production url to your local compouter's IP address. To do this you'll need to modify your computer's `hosts` file. On Unix systems, this can typically found at `/etc/hosts`. On Windows, it can typically be found at `C:\Windows\System32\drivers\etc\hosts`. Once you've found your hosts file, do the following:
1. Add an entry for your local machine like the following
```
127.0.0.1 example.dev
```
2. Modify the `HOST_URL` [env var passed to caddy](https://github.com/willwill96/devcontainer-https-example/blob/main/.devcontainer/docker-compose.yml#L19) to be `example.dev`
3. Add ports 80 & 443 to the [ports config for caddy](https://github.com/willwill96/devcontainer-https-example/blob/main/.devcontainer/docker-compose.yml#L17)

At this point you should be able to access the app at `https://example.dev`!

# Removing the SSL Cert Warning

You can resolve issues around the SSL Cert by installing the caddy docker image's cert on your local machine. To do this, follow these steps for your OS:

> Note: If using Windows & WSL, you will need to follow both the Windows (in a windows command prompt or Powershell) & Unix Instructions (in a WSL command prompt)

- **Unix**
```sh
$ docker cp caddy:/data/caddy/pki/authorities/local/root.crt /path/to/caddy.crt

$ sudo mv /path/to/caddy.crt /usr/local/share/ca-certificates

$ sudo update-ca-certificates
```

- **Windows**
```sh
$ docker cp caddy:/data/caddy/pki/authorities/local/root.crt C:\\path\\to\\caddy.crt

$ certutil -addstore -f "ROOT" C:\\path\\to\\caddy.crt
```

- **MacOS** 
```sh
$ docker cp caddy:/data/caddy/pki/authorities/local/root.crt /path/to/caddy.crt

$ sudo security add-certificates -k /Library/Keychains/System.keychain /path/to/caddy.crt
```