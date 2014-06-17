Bitcoin Java Daemon Wrapper
===========================
Type-safe, open source Java - bitcoind wrapper.

noo4j-daemon calls the bitcoind Json-Rpc service (Noocoin-Qt v0.8.6) using:
* [Bitcoin Java Core Components (org.noo4j.core)] (http://noo4j.github.io/noo4j-core)
* [Apache HTTP Components (org.apache.http)] (http://hc.apache.org)
* [Java API for Json Processing (javax.json)] (http://www.oracle.com/technetwork/articles/java/json-1973242.html)
  * The Json runtime used for this project is the Glassfish reference implementation.

noo4j-daemon implements all 61 commands in bitcoind API as defined in org.noo4j.core.BtcApi. It also contains a notification component to process alerts, and block and wallet events received by bitcoind.

Development status: Currently working on v0.0.4.

Using noo4j-daemon
------------------
noo4j-daemon is free software under [The MIT License (MIT)](http://opensource.org/licenses/MIT/ "The MIT License (MIT)").

Maven pom.xml dependency:
```xml
<dependency>
	<groupId>org.noo4j</groupId>
	<artifactId>noo4j-daemon</artifactId>
	<version>0.0.3</version>
</dependency>
```
or, download artifacts directly from https://github.com/noo4j/noo4j-daemon/releases.

Connect to a bitcoind process:
```java
// bitcoind URL, rpc user/account, rpc password
BtcDaemon daemon = new BtcDaemon(new URL("https://127.0.0.1:18332"),
					"rpcuser", "GBxDyFeDMYEHucz6XFRpXDDB2woCU4wi96KD9widEmsj");
BtcInfo info = daemon.getInfo();
String address = daemon.getAccountAddress("rpcuser");
daemon.walletPassphrase("GBxDyFeDMYEHucz6XFRpXDDB2woCU4wi96KD9widEmsj");
daemon.sendToAddress("mm48fadf1wJVF341ArWmtwZZGV8s34UGWD", BigDecimal.valueOf(0.72)); 
daemon.walletLock();
daemon.stop(); // will stop bitcoind, not required
```
or, with notifications enabled:
```java
// bitcoind URL, rpc user/account, rpc password, notification ports 
BtcDaemon daemon = new BtcDaemon(new URL("http://127.0.0.1:18332"),
					"user", "password", 18334, 18335, 18336);
daemon.getWalletListener().addObserver(new Observer() {
	@Override
	public void update(Observable o, Object obj) {
		if (obj instanceof BtcTransaction) {
			BtcTransaction transaction = (BtcTransaction) obj;
			System.out.println("received wallet event: " + transaction);
		}
	}
});
BigDecimal amount = daemon.getReceivedByAccount("user");
Map<String, BtcAccount> accounts = daemon.listAccounts();
daemon.backupWallet(new File("wallet.dat"));
daemon.stopListening(); // stops the listeners if notifications enabled
String stop = daemon.stop(); // will stop bitcoind
```
For notifications to work, bitcoind has to be started with the notification args:
```bash
./bitcoind -testnet -rpcuser=user -rpcpassword=password
			-alertnotify="java -cp noo4j-daemon-0.0.3-SNAPSHOT.jar org.noo4j.daemon.BtcDaemonNotifier 127.0.0.1 18334 %s"
			-blocknotify="java -cp noo4j-daemon-0.0.3-SNAPSHOT.jar org.noo4j.daemon.BtcDaemonNotifier 127.0.0.1 18335 %s"
			-walletnotify="java -cp noo4j-daemon-0.0.3-SNAPSHOT.jar org.noo4j.daemon.BtcDaemonNotifier 127.0.0.1 18336 %s"
```
`org.noo4j.daemon.BtcDaemonNotifier` is a simple utility that sends a line of text to a port on a given host.
* usage: `java BitcoinDaemonNotifier <host> <port> <message>`
* OS command: you could use an OS command instead such as `netcat` or `nc`

For more examples see `BtcDaemonTest.java`.

Issues
------
Report bugs, issues, or suggestions: https://github.com/noo4j/noo4j-daemon/issues.

Contributions
-------------
Project location: https://github.com/noo4j/noo4j-daemon.
* git: `git@github.com:noo4j/noo4j-daemon.git`
* svn: `https://github.com/noo4j/noo4j-daemon`

Donations
---------
Copyright &copy; 2013, 2014 by ggbusto@gmx.com.
Copyright &copy; 2014 by Noocoin Developers