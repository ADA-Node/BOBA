## **BOBA** Stakepool RPi Config Notes!

Welcome to the BOBA Stakepool.  The BOBA pool is a stakepool on the Cardano network. This repo is intended to help others configure and setup their own stakepools.  My infrastructure is a combination of bare metal server and low cost/low power Raspberry Pi nodes.  The end goal is to migrate the entire BOBA Stakepool to low power processing.  To help others build their own low cost/low power setups, I've documented this guide.

If you decide you just want to stake your ADA rather than run a stakepool, please consider BOBA as your partner for staking your ADA.

## Prerequisites
- Raspberry Pi 4 with 8GB RAM
- High endurance SLC microSD card (i.e. SanDisk SDSDQED-032G-XI)
- Ubuntu Server 20.04.1 for ARM based achitectures

## 1. Setup Ubuntu
- Download the Ubuntu Server from here
  - https://cdimage.ubuntu.com/releases/20.04/release/ubuntu-20.04.1-live-server-arm64.iso
- Install the image onto the microSD card.  There are lots of ways to accomplish this.   to change the .iso file extension to .img
  - I use Win32 Disk Imager: https://sourceforge.net/projects/win32diskimager/
  - Keep in mind you need to change the file extention name from .iso to .img.  Select the image and drive location, then select write.
- Once complete place the microSD into your Raspberry Pi and power it up.

## 2. Update and upgrade Ubuntu:

```
sudo apt update -y
sudo apt upgrade -y
```

## 3. Install dependencies and setup folders:

```
sudo apt install libsodium-dev build-essential pkg-config libffi-dev libgmp-dev libssl-dev libtinfo-dev libsystemd-dev zlib1g-dev make g++ tmux git jq wget libncursesw5 llvm haskell-platform -y
mkdir -p $HOME/.local/bin
mkdir $HOME/git
echo PATH="$HOME/.local/bin:$PATH" >> $HOME/.bashrc
echo export LD_LIBRARY_PATH="/usr/local/lib:$LD_LIBRARY_PATH" >> $HOME/.bashrc
```

## 4. Install ghc-8.6.5:

```
wget https://downloads.haskell.org/~ghc/8.6.5/ghc-8.6.5-aarch64-ubuntu18.04-linux.tar.xz
tar -xf ghc-8.6.5-aarch64-ubuntu18.04-linux.tar.xz
rm ghc-8.6.5-aarch64-ubuntu18.04-linux.tar.xz
cd ghc-8.6.5
./configure
sudo make install
rm -r ghc-8.6.5
```

## 5. Build Cabal 3.2.0 (beware this takes a long time)

```
wget http://hackage.haskell.org/package/cabal-install-3.2.0.0/cabal-install-3.2.0.0.tar.gz
tar -xf cabal-install-3.2.0.0.tar.gz
rm cabal-install-3.2.0.0.tar.gz
cd cabal-install-3.2.0.0
cabal update
cabal install --installdir=$HOME/.local/bin
rm -r cabal-install-3.2.0.0
```

## 6. Install cardano-node and cardano-cli from source (make sure to update cabal twice)

```
cd $HOME/git
git clone https://github.com/input-output-hk/cardano-node.git
cd cardano-node
git fetch --all
git checkout tags/1.20.0
cabal clean
cabal udpate
cabal update
sed -i $HOME/.cabal/config -e "s/overwrite-policy:/overwrite-policy: always/g"
cabal build all
sudo cp $(find $HOME/git/cardano-node/dist-newstyle/build -type f -name "cardano-cli") ~/.local/bin/cardano-cli
sudo cp $(find $HOME/git/cardano-node/dist-newstyle/build -type f -name "cardano-node") ~/.local/bin/cardano-node
cardano-node --version
cardano-cli --version
```

## 7. More to come...

