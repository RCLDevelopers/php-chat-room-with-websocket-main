# PHP Chat Room
This is a Basic Chat Room Application.
Used PHP websocket no used any kind of database (MySQL). This app using per to per communication to client browser in realtime 

## How to setup
1. first make folder 📁 `Chat Room`
2. install websocket `composer require cboden/ratchet`
3. create a `src` folder 📁
4. make a file `Chat.php` in src folder
5. you also read full Documentation of [Ratchet WebSocket for php](https://socketo.me/docs/hello-world) library.

#### chat.php

```
<?php
namespace MyApp;
use Ratchet\MessageComponentInterface;
use Ratchet\ConnectionInterface;

class Chat implements MessageComponentInterface {
    protected $clients;

    public function __construct() {
        $this->clients = new \SplObjectStorage;
    }

    public function onOpen(ConnectionInterface $conn) {
        // Store the new connection to send messages to later
        $this->clients->attach($conn);

        echo "New connection! ({$conn->resourceId})\n";
    }

    public function onMessage(ConnectionInterface $from, $msg) {
        $numRecv = count($this->clients) - 1;
        echo sprintf('Connection %d sending message "%s" to %d other connection%s' . "\n"
            , $from->resourceId, $msg, $numRecv, $numRecv == 1 ? '' : 's');

        foreach ($this->clients as $client) {
            if ($from !== $client) {
                // The sender is not the receiver, send to each client connected
                $client->send($msg);
            }
        }
    }

    public function onClose(ConnectionInterface $conn) {
        // The connection is closed, remove it, as we can no longer send it messages
        $this->clients->detach($conn);

        echo "Connection {$conn->resourceId} has disconnected\n";
    }

    public function onError(ConnectionInterface $conn, \Exception $e) {
        echo "An error has occurred: {$e->getMessage()}\n";

        $conn->close();
    }
}

```
5. create another folder 📂 `bin`
6. make another file in bin folder 📂 `Server.php`

#### Server.php

```
<?php
use Ratchet\Server\IoServer;
use Ratchet\Http\HttpServer;
use Ratchet\WebSocket\WsServer;
use MyApp\Chat;

    require dirname(__DIR__) . '/vendor/autoload.php';

    $server = IoServer::factory(
        new HttpServer(
            new WsServer(
                new Chat()
            )
        ),
        8080
    );

    $server->run();

```

7. add a JavaScript file in index file.

```<script>
var conn = new WebSocket('ws://localhost:8080');
conn.onopen = function(e) {
    console.log("Connection established!");
};

conn.onmessage = function(e) {
    console.log(e.data);
};

```

## Running it
1. `~bin/php Server.php`
2. open a browser (liked Chrome)
3. pressed `F5` or open console.
4. write a code in your console
`conn.send("Hello World");` enter hit.
5. share the link you chat room webapp your friends circle.
6. let's chat with your friends

## After Downloading project
1. run it `composer install` and `composer update`
2. goto bin folder and run php cli `php Server.php`
3. goto you root folder run it `php -S localhost:8000`
4. Open chrome browser type this [url http://localhost:8000](http://localhost:8000)
5. share the this link 🔗 your friends and chating with your friends.
