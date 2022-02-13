Okay, install wireguard and wireguard-tools on your laptop.

Create a key pair:

    cd /etc
    mkdir wireguard
    cd wireguard
    wg genkey | tee privatekey | wg pubkey > publickey


On Gate see the README file and configure interface wg1.conf with something like:

    #
    # Wireguard wg1: Chris Dew laptop
    #

    [Interface]
    Address = 172.16.0.6/30
    ListenPort = 2269
    PrivateKey = <hidden for security>

    [Peer]
    #No fixed end-point (road warrior)
    #Endpoint = gate.tubby.org:2269
    PublicKey = <Public key from Ubuntu>
    AllowedIPs = 172.16.0.4/30


On your client configure wg0.conf with something like:

    #
    # Wireguard wg0: Link to Thorcom
    #

    [Interface]
    Address = 172.16.0.5/30
    ListenPort = 2269
    PrivateKey = <Ubuntu private key>

    [Peer]
    Endpoint = gate.thorcom.com:2269
    PublicKey = <Gate public key>
    AllowedIPs = 192.168.0.0/24, 192.168.1.0/24, 172.27.0.0/16



Start Wireguard interface on gate:

    wg-quick up wg1

on Ubuntu:

    wg-quick up wg0


Test


