# Ansible Role Veloticy

Ansible role to install and configure a Veloticy Minecraft proxy on Debian-based systems.

![ansible-lint](https://github.com/sleepy-nols/ansible-velocity/actions/workflows/ansible-lint.yml/badge.svg)
![push-galaxy](https://github.com/sleepy-nols/ansible-velocity/actions/workflows/ansible-galaxy-push-role.yml/badge.svg)

---
### Install

Install via Ansible Galaxy or clone the Repo.
```
ansible-galaxy role install sleepy-nols.veloticy

git clone git@github.com:sleepy-nols/ansible-velocity.git
```

---
### Variables and Defaults

The default config can be found [here](https://github.com/PaperMC/Velocity/blob/dev/3.0.0/proxy/src/main/resources/default-velocity.toml).
Config documentation can be found [here](https://docs.papermc.io/velocity/configuration).

---
#### Mandatory:

```yml
velocity_servers:
  lobby: "127.0.0.1:30066"
  factions: "127.0.0.1:30067"
  minigames: "127.0.0.1:30068"
```
Configure your servers here. Each key represents the server's name, and the value represents the IP address of the server to connect to.

```yml
velocity_servers_try:
  - "lobby"
  - "minigames"
```
In what order we should try servers when a player logs in or is kicked from a server.

#### Optional:

```yml
velocity_forced_hosts:
  "lobby.example.com":
    - "lobby"
  "factions.example.com":
    - "fractions"
```
Bind specific domains to servers.


**Defaults are taken from the [current](https://github.com/PaperMC/Velocity/blob/dev/3.0.0/proxy/src/main/resources/default-velocity.toml) default config, but are not set until defined to allow new versions to set updated defaults. They may need to be updated here sometime.**

---
##### Velocity config: #####

```yml
velocity_config_version: "2.6"
```
This is the current config version used by Velocity. You should not alter this setting.

```yml
velocity_config_bind: "0.0.0.0:25577"
```
This tells the proxy to accept connections on a specific IP. By default, Velocity will listen for connections on all IP addresses on the computer on port 25577.


```yml
velocity_config_motd: "<#09add3>A Velocity Server"
```
This allows you to change the message shown to players when they add your server to their server list. You can use [MiniMessage](https://docs.advntr.dev/minimessage/format.html) format.

```yml
velocity_config_show_max_players: 500
```
This allows you to customize the number of "maximum" players in the player's server list. Note that Velocity doesn't have a maximum number of players it supports.

```yml
velocity_config_online_mode: True
```
Should we authenticate players with Mojang?

```yml
velocity_config_force_key_authentication: True
```
Should the proxy enforce the new public key security standard?

```yml
velocity_config_prevent_client_proxy_connections: False
```
If client's ISP/AS sent from this proxy is different from the one from Mojang's authentication server, the player is kicked. This disallows some VPN and proxy connections but is a weak form of protection.

```yml
velocity_config_player_info_forwarding_mode: "NONE"
```
Should we forward IP addresses and other data to backend servers?

Available options:
- "none": No forwarding will be done. All players will appear to be connecting from the proxy and will have offline-mode UUIDs.
- "legacy": Forward player IPs and UUIDs in a BungeeCord-compatible format. Use this if you run servers using Minecraft 1.12 or lower.
- "bungeeguard": Forward player IPs and UUIDs in a format supported by the BungeeGuard plugin. Use this if you run servers using Minecraft 1.12 or lower, and are unable to implement network level firewalling (on a shared host).
- "modern": Forward player IPs and UUIDs as part of the login process using Velocity's native forwarding. Only applicable for Minecraft 1.13 or higher.

[more info](https://docs.papermc.io/velocity/player-information-forwarding)

```yml
velocity_config_forwarding_secret_file: "forwarding.secret"
```
The name of the file in which the forwarding secret is stored. This secret is used to ensure that player info forwarded by Velocity comes from your proxy and not from someone pretending to run Velocity. See the "Player info forwarding" section for more info.

```yml
velocity_config_announce_forge: False
```
This setting determines whether Velocity should present itself as a Forge/FML-compatible server.

```yml
velocity_config_kick_existing_players: False
```
Allows restoring the vanilla behavior of kicking users on the proxy if they try to reconnect (e.g. lost internet connection briefly).

```yml
velocity_config_ping_passthrough: "DISABLED"
```
Should Velocity pass server list ping requests to a backend server?

Available options:
- "disabled": No pass-through will be done. The velocity_toml and server-icon.png will determine the initial server list ping response.
- "mods": Passes only the mod list from your backend server into the response. The first server in your try list (or forced host) with a mod list will be used. If no backend servers can be contacted, Velocity won't display any mod information.
- "description": Uses the description and mod list from the backend server. The first server in the try (or forced host) list that responds is used for the description and mod list.
- "all: Uses the backend server's response as the proxy response. The Velocity configuration is used if no servers could be contacted.

```yml
velocity_config_enable_player_address_logging: True
```
If disabled, player IP addresses will be replaced by <ip address withheld> in logs.


---
##### Velocity advanced config:
```yml
velocity_config_advanced_compression_threshold: 256
```
This is the minimum size (in bytes) that a packet must be before the proxy compresses it.

```yml
velocity_config_advanced_compression_level: -1
```
This setting indicates what zlib compression level (0-9) the proxy should use to compress packets. -1 uses the default zlib level.

```yml
velocity_config_advanced_login_ratelimit: 3000
```
This setting determines the minimum amount of time (in milliseconds) that must pass before a connection from the same IP address will be accepted by the proxy. A value of 0 disables the rate limit.

```yml
velocity_config_advanced_connection_timeout: 5000
```
This setting determines how long the proxy will wait to connect to a server before timing out.

```yml
velocity_config_advanced_read_timeout: 30000
```
This setting determines how long the proxy will wait to receive data from the server before timing out.

```yml
velocity_config_advanced_haproxy_protocol: False
```
This setting determines whether or not Velocity should receive HAProxy PROXY messages. If you don't use HAProxy, leave this setting off.

```yml
velocity_config_advanced_tcp_fast_open: False
```
This setting allows you to enable TCP Fast Open support in Velocity. Your proxy must run on Linux kernel >=4.14 for this setting to apply.

```yml
velocity_config_advanced_bungee_plugin_message_channel: True
```
This setting allows you to enable or disable support for the BungeeCord plugin messaging channel.

```yml
velocity_config_advanced_show_ping_requests: False
```
This setting allows you to log ping requests sent by clients to the proxy.

```yml
velocity_config_advanced_failover_on_unexpected_server_disconnect: True
```
This setting allows you to determine if the proxy should failover or disconnect the user in the event of an unclean disconnect.

```yml
velocity_config_advanced_announce_proxy_commands: True
```
This setting allows you to enable or disable explicitly sending proxy commands to the client (for Minecraft 1.13+ tab completion).

```yml
velocity_config_advanced_log_command_executions: False
```
Determines whether or not the proxy should log all commands run by the user.

```yml
velocity_config_advanced_log_player_connections: True
```
Enables logging of player connections when connecting to the proxy, switching servers and disconnecting from the proxy.


---
##### Velocity query config:
```yml
velocity_config_query_enabled: False
```
Whether or not Velocity should reply to Minecraft query protocol requests. You can usually leave this false.
```yml
velocity_config_query_port: 25577
```
Specifies which port that Velocity should listen on for GameSpy 4 (Minecraft query protocol) requests.

```yml
velocity_config_query_map: "Velocity"
```
Specifies the map name to be shown to clients.

```yml
velocity_config_query_show_plugins: False
```
Whether or not Velocity plugins are included in the query responses.

---
### Example Playbook

```yml
- hosts: minecraft-proxies
  roles:
    - sleepy-nols.velocity
  vars:
    velocity_servers:
      lobby: "127.0.0.1:30066"

    velocity_servers_try:
      - "lobby"
```

---
### Contributing

Contributions and bug reports are welcome :). Please point merge requests at devel.

---
### License

GPLv3

