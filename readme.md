p2p on the web

James Halliday
https://substack.neocities.org

follow along:

https://github.com/substack/oscal-2016-slides

please get out your laptop and try things out!

---
# the web

clients request data from servers

---
# client/server architecture on the web

clients are completely dependent on servers:

* to fetch data
* to deliver upgrades
* to mediate communication between users

servers have a lot more power than the users!

---
# how p2p differs from client/server

peers talk to each other, directly
instead of a server

---
# new browser features

* webrtc - direct connections between browsers
* indexeddb - persistent storage

plus: webrtc is end-to-end encrypted by default!

---
# cooperative networks

clients provide services to each other on an equal basis

---
# unique advantages of p2p

* infinite scaling for free!
* offline/marginal networks
* no more server maintenance

---
# limitations of p2p

* availability of unpopular content
* DNS-style naming systems are very hard

---
# hybrid architectures

a few non-p2p components to fill in the gaps can go a long way:

* webrtc signaling
* naming systems

---
# p2p opportunities

* services that nobody can own
* services that nobody can turn off or shut down
* infinite scaling for free!

---
# with p2p, we could build:

* a youtube that has no region restrictions and no DMCA takedowns
* services that can't be shut down (RIP geocities)
* maps that infinitely scale and edit offline
* seekable, auto-archiving live streaming to peers
* p2p file sync and backup, like dropbox

---
# open source and p2p

we can provide services at massive scale for no cost

run free and open source software
participate in a cooperative network
all by visiting a URL in a browser!

---
# thinking in the client

EVERYTHING must happen on the client.

---
# some p2p techniques

* dht
* kappa architecture
* gossip protocols

---
# dht

(D)istributed (H)ash (T)able

how do I find peers?

---
# dht

how do I find peers?

* bootstrapping list
* ask each peer for a peer that is closest to the key you're looking for

---
# kappa architecture

how do I replicate data feeds with peers?

---
# kappa architecture

how do I replicate data feeds with peers?

* append-only log is the source of truth
* materialized views provide fast lookups of log data

---
# log replication

concatenate!

---
# merkle DAGs

updates point at the documents they replace by their cryptographic hash

like git!

---
# merkle DAG

demo!

---
# gossip protocols

when you learn something, tell your peers!

messages propagate like a virus

---
# logs and blobs

logs are good for small pieces of metadata

blob storage like webtorrent/dat/ipfs is better for big files: photos, video

---
# user accounts without servers

* generate a cryptographic keypair
* your user id is your public key
* sign messages

---
# the web

browsers run javascript!

---
# p2p web demos

* https://substack.neocities.org/chatwizard/#oscal
* osm-p2p
* webtorrent / instant.io

---
# npm packages

* chloride
* level
* level-browserify
* hyperlog
* hypercore
* webtorrent

---
# running npm packages in the browser

``` sh
$ npm install -g browserify
$ browserify main.js > bundle.js
```

``` html
<script src="bundle.js"></script>
```

---
# leveldb

a simple key/value database

* level - for node.js
* level-browserify - for the browser using IndexedDB

* modular database!
* same interface in node.js and the browser!

npm install level level-browserify

---
# leveldb

node.js:

```
var level = require('level')
var db = level('./whatever.db')
```

browser js:

```
var level = require('level-browserify')
var db = level('whatever')
```

---
# leveldb

demo!

---
# hyperlog

npm install hyperlog

* append-only log using leveldb
* very easy replication!

---
# hyperlog

demo!

---
# chloride

nacl/sodium (djb) ed25519 elliptic curve cryptography

* `sodium.crypto_sign_keypair()`
* `sodium.crypto_sign_detached(msg, secretKey)`
* `sodium.crypto_sign_verify_detached(sig, msg, publicKey)`

npm install chloride

---
# user accounts with chloride

demo!

---
# simple-peer

simple API for webrtc connections

* node.js stream API: `.pipe()`, like `|` in the shell

---
# simple-peer

initiator:

```
var Peer = require('simple-peer')
var p = new Peer({ initiator: true })
p.on('signal', function (data) {
  console.log(data) // send this value to the other peer
})

// p is a full duplex stream, we can .pipe() to or .pipe() from
```

---
# simple-peer

receiver:

```
var Peer = require('simple-peer')
var p = new Peer()
p.signal(remoteData) // data comes from the initiator

p.on('signal', function (data) {
  console.log(data) // send this value back to the initiator
})

// p is a full duplex stream, we can .pipe() to or .pipe() from
```

---
# signalhub

* like bittorrent trackers, can use it to hook up webrtc connections
* it's a server, but it doesn't do much

to run your own signalhub:

```
$ npm install -g signalhub
$ signalhub listen -p 8000
```

---
# webrtc-swarm

create a swarm of peers interested in a topic

```
var wswarm = require('webrtc-swarm')
var signalhub = require('signalhub')

var swarm = wswarm(signalhub('whatever', ['https://signalhub.mafintosh.com']))
swarm.on('peer', function (peer, id) {
  // peer is a full duplex stream
  // id is a unique session identifier
})
```

---
# webrtc-swarm

webrtc-swarm demo

---
# webtorrent

bittorrent protocol implemented over webrtc

---
# webtorrent seeding

```
var webtorrent = require('webtorrent')
var client = webtorrent()
var files = [Buffer('HELLO TIRANA')]

client.seed(files, { name: 'msg.txt' }, function (torrent) {
  console.log(torrent.magnetURI)
})
```

---
# webtorrent downloading

```
var webtorrent = require('webtorrent')
var client = webtorrent()

client.add(magnetURI, function (torrent) {
  torrent.files.forEach(function (file) {
    file.createReadStream()
      .on('data', function (buf) {
        console.log('BUF=', buf.toString())
      })
  })
})
```

---
# webtorrent

demo

---
# hypercore / hyperdrive

http://dat-data.com/

hypercore/hyperdrive lets the publisher update files to peers
after the files have been sent

You can use hypercore to make a p2p live streaming app!

---
# hypercore

```
var hypercore = require('hypercore')
var level = require('level')
var db = level('test.db')
var core = hypercore(db)
```

* `core.createWriteStream()`
* `core.createReadStream()`
* `core.replicate()`

---

EOF
