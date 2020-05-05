# WebThings Gateway 



## Download and Build Gateway

* Clone the GitHub repository (or fork it first):

* Change into the gateway directory:


* If you have chosen to install nvm above, install and use an LTS version of node and then set the default version. The **`.nvmrc`** file will be used by nvm to determine which version of node to install.

    ```
    $ nvm install
    $ nvm use
    $ nvm alias default $(node -v)
    ```

* Verify that node and npm have been installed:

    ```
    $ node --version
    v10.19.0
    $ npm --version
    6.14.0
    ```

    Note: these versions might differ from the LTS version installed locally.

* Install dependencies:

    ```
    $ npm ci
    ```

* Add Firewall exceptions (Fedora Linux Only)

    ```
    $ sudo firewall-cmd --zone=public --add-port=4443/tcp --permanent
    $ sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
    $ sudo firewall-cmd --zone=public --add-port=5353/udp --permanent
    ```


* Set up domain:
    * If you plan to use Mozilla's provided tunneling service to set up a `*.mozilla-iot.org` domain:
        * Start the web server:

            ```
            $ npm start
            ```

        * Load `http://localhost:8080` in your web browser (or use the server's IP address if loading remotely). Then follow the instructions on the web page to set up domain and register. Once this is done you can load
`https://localhost:4443` in your web browser (or use the server's IP address if loading remotely).
    * If you plan to use your own SSL certificate:
        * The HTTPS server looks for `privatekey.pem` and `certificate.pem` in the `ssl` sub-directory of the `userProfile` directory specified in your config. You can use a real certificate or generate a self-signed one by following the steps below.

            ```
            $ MOZIOT_HOME="${MOZIOT_HOME:=${HOME}/.mozilla-iot}"
            $ SSL_DIR="${MOZIOT_HOME}/ssl"
            $ [ ! -d "${SSL_DIR}" ] && mkdir -p "${SSL_DIR}"
            $ openssl genrsa -out "${SSL_DIR}/privatekey.pem" 2048
            $ openssl req -new -sha256 -key "${SSL_DIR}/privatekey.pem" -out "${SSL_DIR}/csr.pem"
            $ openssl x509 -req -in "${SSL_DIR}/csr.pem" -signkey "${SSL_DIR}/privatekey.pem" -out "${SSL_DIR}/certificate.pem"
            ```

        * Start the web server:

            ```
            $ npm start
            ```

        * Load `https://localhost:4443` in your web browser (or use the server's IP address if loading remotely). Since you're using a self-signed certificate, you'll need to add a security exception in the browser.

## Browser Support

The Gateway only supports the following browsers, due to its use of the [`Fetch API`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) and [`WebSocket API`](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API):
* Firefox 52+
* Chrome 43+
* Edge 14+
* Safari 10.1+
* Opera 29+

## Debugging

If you are using VS Code, simply use the "launch" target. It will build the gateway in debugger mode.

If you are not using VS Code, run `npm run debug` and it will build the gateway and launch it with `--inspect`.

## Install additional dependencies for Test (Debian)

These steps are required on Debian
```
$ sudo apt install firefox openjdk-8-jre
```

## Running Tests
To run the linter and all tests:
```
$ npm test
```

To run a single test:
```
$ jest src/test/{test-name}.js
```
(assumes you have the `jest` command on your `PATH`, otherwise use `./node_modules/jest/bin/jest.js`)

To compare UI with parent branch:
```
$ npm run screenshots
$ npm test
```
(if you have the screenshots in the folder `./browser-test-screenshots`, `npm test` should compare UI with screenshots stored)

