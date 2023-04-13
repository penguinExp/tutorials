# Creating Redis Instance on GCP Compute Engine

This steps are for creating a Redis DB instance in GCP Compute Engine and connecting it via `ioredis`, `redis_cli` and `redisInsight`

### Creating an Instance

1. In the Cloud Console, go to the VM Instances page.
2. Click the Create instance button.
3. Set Name to _redis-tutorial_.
4. In the Boot disk section, click Change to begin configuring your boot disk.
5. In the OS images tab, choose **Debian GNU/Linux 11 (bullseye)** (Latest one available at the time of writing).
6. In the Boot disk type section, select **Standard persistent disk**.
7. Click Select.
8. Click the Create button to create the instance.

### Setting up Redis on Instance

1. In the list of virtual machine instances, click the SSH button in the row of the instance which we just created
2. In the SSH terminal, enter the following command:

```
sudo apt-get update
```

3. Install Redis:

```
sudo apt-get -y install redis-server
```

4. Verify that Redis is running:

```
ps -f -u redis
```

if you see something like fallowing, you're good to go

```
UID          PID    PPID  C STIME TTY          TIME CMD
redis       1331       1  0 13:31 ?        00:00:00 /usr/bin/redis-server 127.0.0.1:6379
```

Congratulations! 🎉🎉 Redis is running, but will only accept connections from 127.0.0.1, the local machine running the Redis server.

### Configure Redis for remote access

By default, Redis doesn't allow remote connections. So for allowing remote connections we need to modify it's settings.

1. In the SSH terminal window, paste the fallowing command to open `redis.conf` file

```
sudo nano /etc/redis/redis.conf
```

2. Scroll down to the line that begins with `bind 127.0.0.1`.

3. Replace 127.0.0.1 with

```
bind 0.0.0.0
```

`Note:` By doing these we are making the connection publicly available. If you are doing these for a publicly hosted service make sure to have a very strong password for the connection. Which can be done as fallows,

4. Scroll down to the line that begins with `# requirepass`.

5. Uncomment `# requirepass` and add a strong password

```
 requirepass [SOME_VERY_STRONG_PASSWORD]
```

6. Save the file and exit the editor.

7. Restart the database service by pasting the fallowing command

```
sudo service redis-server restart
```

8. Verify that Redis is listening to all traffic:

```
ps -f -u redis
```

You should now see something like the following:

```
UID        PID  PPID  C STIME TTY          TIME CMD
redis      950     1  0 21:07 ?        00:00:00 /usr/bin/redis-server 0.0.0.0:6379
```

Congratulations! Now our Redis DB is ready and accepting remote connections 🤝

### Open the network port (optional)

> By default, this Compute Engine instance is added to the default network in your project. The default network allows all TCP connections between its Compute Engine instances using the internal network. Redis accepts remote connections on TCP port 6379.

Follow these steps to add a firewall rule that enables traffic on this port.

1. In the Cloud Console, go to the Create a firewall rule page.

2. In the Network field, leave the network as default.

3. In the Name field, enter _redis-tutorial_.

4. In a separate window, navigate to ip4.me to get the IPv4 address of your local computer. If you want to make it public use `0.0.0.0`

5. Back in the Cloud Console, In Source IP Ranges, enter the IPv4 address from the previous step. Append /32 to the end of the IPv4 address to format in CIDR notation. For example: 1.2.3.4/32.

6.In Allowed protocols and ports, enter tcp:6379.

Click Create.

> Now in second module we need to edit our instance

1. Go on to VM Instance page and click on the VM we've created (By clicking on the vm name which is underlined)

2. Click on `edit` option in the top row and it opens the config page

3. Scroll down to `Network tags` section and add the name of firewall rule, our name was _redis-tutorial_

4. And hit save button and we're done!

### Connect to redis from CLI

Now we can connect to our Redis database from our computer. This tutorial uses redis-cli, an officially supported command-line tool to interact with a Redis server.

1. Install [Redis](https://redis.io/topics/quickstart#installing-redis) on your local computer. Here I am using Ubuntu shell in WSL2. Because Redis is not officially supported on Windows.

2. Go to the VM instances page and find the external IP address of our Compute Engine instance in the **External IP** column.

3. Now in order to `PING` the redis server paste the fallowing command in the terminal

```
redis-cli -h [EXTERNAL_IP_ADDRESS] -a '[YOUR_STRONG_PASSWORD]' ping
```

You should receive a response of `PONG` output to the terminal.

Congratulations! We've successfully connected to our Redis server, cheers 🍻!

### Connect to redis from RedisInsight

We can also connect to RedisInsight, install it from [here](https://redis.com/redis-enterprise/redis-insight/#insight-form)↗️

1. Open RedisInsight and click on **+ ADD REDIS DATABASE**

2. In the host field paste our instances external ip addr and keep the port to 6379

3. In the Database Alias field, paste our external ip fallowed by colan then fallowed by our port i.e. 6379

4. Keep the username empty and write your secure password in the password field and hit **ADD REDIS DATABASE**

and there we go, again cheers 🍻!

### Connect to our instance from RedisIo JS client library

We can also connect to our instance from Redis client libraries, we are using RedisIO js here, check [these page](https://redis.io/docs/clients/) for other clients available officially available

1. Install client using `npm i ioredis` in your JS/TS project

2. And paste fallowing code

```
import Redis from 'ioredis';

const redis = new Redis({
  host: Our_External_URL,
  port: 6379,
  password: OUR_PASSWORD,
  lazyConnect: true, // optional
});

// It's being called in a global scope for simplicity and not suggested to be done in production environments
redis.connect();

/**
 * Creates data in redis associated with our key
 */
async function writeToRedis() {
  await redis.set('key_name', 'value');
  // Our entry will expire in 10 mins and will be auto deleted from the DB
  await redis.expire('key_name', 600);
}

/**
 * Fetches data from redis db
 *
 * @returns value stored associated with our key
 */
async function getFromRedis() {
  return await redis.get('key_name');
}
```

Now you can call this functions in your app in order to test our connections, All the best ✌️
