# dmkk-OpenVPN

## Requirements

1. EC2 instance with open port 3000
2. DNS record created for the EC2 instance
3. Install Docker

## Steps to follow - Using Docker Compose

### 1. Setup OpenVPN

git clone https://github.com/kylemanna/docker-openvpn.git && cd docker-openvpn

docker build -t dmkk-openvpn .

mkdir /vpn-data && touch /vpn-data/vars

docker run -v /vpn-data:/etc/openvpn --rm dmkk-openvpn ovpn_genconfig -u udp://vpn2.dmkk.cloudns.nz:3000

docker run -v /vpn-data:/etc/openvpn --rm -it dmkk-openvpn ovpn_initpki

### 2. Start OpenVPN Docker Container using docker-compose

2.1 Create the following docker-compose.yaml file

```
    version: "3.9"
    services:
      dmkk-vpn:
        restart: always
        image: "dmkk-openvpn"
        ports:
          - "3000:1194/udp"
        volumes:
          - "/vpn-data:/etc/openvpn"
          - "/tmp:/tmp"
        cap_add:
          - "NET_ADMIN"
```

2.2 Start OpenVPN container

```docker-compose up -d```


## Pre-Configured OpenVPN Settings

Pre-configurations
```
git clone https://github.com/kylemanna/docker-openvpn.git && cd docker-openvpn

docker build -t dmkk-openvpn .

git clone https://github.com/kolithakasun/dmkk-OpenVPN.git 
```
Copy Files
```
cp -r dmkk-OpenVPN/vpn.dmkk.cloudns.nz/vpn-data /vpn-data
```
OR
```
cp -r dmkk-OpenVPN/vpn2.dmkk.cloudns.nz/vpn-data /vpn-data
```
Start Service

```docker-compose up -d```

# Create Users
```
docker run -v /vpn-data:/etc/openvpn --rm -it dmkk-openvpn easyrsa build-client-full user1 nopass

docker run -v /vpn-data:/etc/openvpn --rm dmkk-openvpn ovpn_getclient user1 > user1.ovpn
```
```
docker exec -it open-vpn_dmkk-vpn_1  easyrsa build-client-full dmkk nopass

docker exec -it open-vpn_dmkk-vpn_1  ovpn_getclient user1 > /tmp/dmkk.ovpn
```
