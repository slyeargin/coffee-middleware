## Installation

    sudo npm install coffee-middleware

## Options

<table>
    <thead>
        <tr>
            <th>Option</th>
            <th>Description</th>
            <th>Default</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th><code>force</code></th>
            <td>Always re-compile coffee-script files on each request.</td>
            <td><code>false</code></td>
        </tr>
        <tr>
            <th><code>once</code></th>
            <td>Only check for need to recompile once after each server restart. Useful for reducing disk i/o on production.</td>
            <td><code>false</code></td>
        </tr>
        <tr>
            <th><code>debug</code></th>
            <td>Output any debugging messages to the console.</td>
            <td><code>false</code></td>
        </tr>
        <tr>
            <th><code>src</code></th>
            <td>Source directory containing the <code>.coffee</code> files. <strong>Required.</strong></td>
            <td></td>
        </tr>
        <tr>
            <th><code>dest</code></th>
            <td>Desitnation directory to output the compiled <code>.js</code> files.</td>
            <td><code>&lt;src&gt;</code></td>
        </tr>
        <tr>
            <th><code>prefix</code></th>
            <td>Path which should be stripped from the public <code>pathname</code>.</td>
            <td></td>
        </tr>
        <tr>
            <th><code>compress</code></th>
            <td>Compress the output being written to the <code>*.js</code> files. When set to <code>'auto'</code> compression will only happen when the js file ends with <code>.min.js</code> or <code>-min.js</code>.</td>
            <td><code>auto</code></td>
        </tr>
    </tbody>
</table>

## Examples

### Connect

    var coffeeMiddleware = require('coffee-middleware');

    var server = connect.createServer(
        coffeeMiddleware({
            src: __dirname + '/public',
            compress: true
        }),
        connect.staticProvider(__dirname + '/public')
    );

### Express

    var coffeeMiddleware = require('coffee-middleware');

    var app = express.createServer();

    app.configure(function () {
        // Other configuration here...

        app.use(coffeeMiddleware({
            src: __dirname + '/public',
            compress: true
        }));

        app.use(express.static(__dirname + '/public'));
    });

### Express - Different `src` and `dest`

When using a different `src` and `dest` you can use the `prefix` option to make the directory structure cleaner.

Requests for static assets (like javascripts) made to the express server use a `pathname` to look up the file. So if the request is for `http://localhost/js/main.js` the `pathname` will be `/js/main.js`.

The middleware uses that path to determine where to look for coffee-script files. In the original example it looks for the `.coffee` file at `/public/js/main.coffee` and compiles it to `/public/js/main.js`.

If you are using a different `src` and `dest` options it causes for more complex directories structures unless you use the `prefix` option. For example, without the `prefix`, and with a `src` of `/src/coffee` and a `dest` of `/public` it would look for the less file at `/src/coffee/js/main.coffee` and compile it to `/public/js/main.js`. To make it cleaner you can use the `prefix` option:

    var coffeeMiddleware = require('coffee-middleware');

    var app = express.createServer();

    app.configure(function () {
        // Other configuration here...

        app.use(coffeeMiddleware({
            dest: __dirname + '/public/js',
            src: __dirname + '/src/coffee',
            prefix: '/js',
            compress: true
        }));

        app.use(express.static(__dirname + '/public'));
    });

Using the `prefix` it changes the `pathname` from `/js/main.js` to `/main.coffee`. With that prefix removed from the `pathname` it makes things cleaner. With the `prefix` removed it would look for the less file at `/src/coffee/main.coffee` and compile it to `/public/js/main.js`.
