# SignServerStats
A PocketMine plugin which can display player count and MODT on a sign from any server with query enabled.

[![](https://poggit.pmmp.io/shield.api/SignServerStats)](https://poggit.pmmp.io/p/SignServerStats)

## Usage:
Anyone with the permission `SSS.signs` can create a sign with the following content:
```
[SSS]
serverIP/Hostname (can go over two lines with a - at the end of the first line)
serverPort
```

The plugin will recognize that sign and fill it with colorful stats!
Players who tap the sign and have the permission `SSS.servertransfer` will be transferred to the Server the sign is displaying.

*Note: Due to 1.1+ not telling the server when the sign is finished, you now need to tap the sign once to activate it after setting it up.*

## API:
**This plugin can also be used as a query API. You might want to look into SignServerStats.php, because all the API functions are in there.**

Example plugins are provided in /examples/:
- DumpInfo.php - Dumps all available info about a server.

Because the following two examples may also be useful for users, they are also provided as phars in every release:
- StatusList/ - Lists online status and player count of multiple servers in a List. [![](https://poggit.pmmp.io/shield.api/StatusList)](https://poggit.pmmp.io/p/StatusList)
- WarnOffline/ - Warns if a server has gone offline. Depends on StatusList. [![](https://poggit.pmmp.io/shield.api/WarnOffline)](https://poggit.pmmp.io/p/WarnOffline)
- LinkPlayerCount/ - Links the player count of multiple servers. (InDev)

_You should always check if your plugin is compatible with the version of StatusList present on the current server with the help of the isCompatible function_

Example:
```php
/** @var robske_110\SSS\SignServerStats $signServerStats */
if(!$signServerStats->isCompatible("1.1.0")){
   	$this->getLogger()->critical("Your version of SignServerStats is not compatible with this plugin.");
	$this->getServer()->getPluginManager()->disablePlugin($this);
	return;
}
```

#### If you prefer just a quick introduction, here is one for getting the the online status of the server `example.com:1234`:

Initial, for example onEnable:
```php
/** @var $sss robske_110\SSS\SignServerStats */
$sss->addServer("example.com", 1234);
```
This tells SSS that it should query that server in its next query.

**IMPORTANT: The info might not be available in the next SSSasyncUpdateEvent!**

To check if the server is online simply listen to the SSSasyncUpdateEvent and check the array:
```php
public function onSSSasyncUpdate(robske_110\SSS\event\SSSasyncUpdateEvent $event){
	$serverOnlineArray = $event->getPlugin()->getServerOnline();
	if(isset($serverOnlineArray["example.com"."@".1234])){
		$isOnline = $serverOnlineArray["example.com"."@".1234];
	    //isOnline is now a bool (true/false) that reflects the online state of the server (if the server is online and this says false, it probably doesn't have query enabled.)
	    //You can now also get additional data with getMODTs() and getPlayerData() in the same way.
	    //There is also the function getFullData() with which you can get allthedata a server sent over the query. Do a var_dump on it for a headstart.
	}else{
	    //The information is going to be here in the next event!
	}
}
```

## TODO:

- [ ] FTPs? NCPs?

- [x] Multi-line server hostnames

- [x] Create custom event onAsyncUpdate for easier API use

- [x] API should be able to also get other data (playernamelist, pluginlist)

- [x] Sign style config

- [x] Tap sign to transfer
