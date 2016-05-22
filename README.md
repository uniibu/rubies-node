
## This is not a full Rubies client. This is just a nodejs module that you can use to connect your nodejs apps to a running rubiesd daemon via JSON-RPC.
The API is equivalent to the API document [here](https://en.bitcoin.it/wiki/Original_Bitcoin_client/API_Calls_list), with some exceptions. Check your rubies client for additional API calls.

The methods are exposed as lower camelcase methods on the `rubies.Client`
object, or you may call the API directly using the `cmd` method.

### Install
```
clone this repo, then just run
npm install
```
## Examples

### Create client
```js
// all config options are optional
var client = new rubies.Client({
  host: 'localhost',
  port: 8423,
  user: 'username',
  pass: 'password',
  timeout: 30000
});
```

### Get balance across all accounts with minimum confirmations of 6

```js
client.getBalance('*', 6, function(err, balance, resHeaders) {
  if (err) return console.log(err);
  console.log('Balance:', balance);
});
```
### Getting the balance directly using `cmd`

```js
client.cmd('getbalance', '*', 6, function(err, balance, resHeaders){
  if (err) return console.log(err);
  console.log('Balance:', balance);
});
```

### Batch multiple RPC calls into single HTTP request

```js
var batch = [];
for (var i = 0; i < 10; ++i) {
  batch.push({
    method: 'getnewaddress',
    params: ['myaccount']
  });
}
client.cmd(batch, function(err, address, resHeaders) {
  if (err) return console.log(err);
  console.log('Address:', address);
});
```

## SSL
See [Enabling SSL on original client](https://en.bitcoin.it/wiki/Enabling_SSL_on_original_client_daemon).

If you're using this to connect to bitcoind across a network it is highly
recommended to enable `ssl`, otherwise an attacker may intercept your RPC credentials
resulting in theft of your rubies.

When enabling `ssl` by setting the configuration option to `true`, the `sslStrict`
option (verifies the server certificate) will also be enabled by default. It is
highly recommended to specify the `sslCa` as well, even if your rubiesd has
a certificate signed by an actual CA, to ensure you are connecting
to your own rubiesd.

```js
var client = new rubies.Client({
  host: 'localhost',
  port: 8423,
  user: 'username',
  pass: 'password',
  ssl: true,
  sslStrict: true,
  sslCa: fs.readFileSync(__dirname + '/myca.cert')
});
```
## This is a fork of the original node-bitcoin. https://github.com/freewil/node-bitcoin
