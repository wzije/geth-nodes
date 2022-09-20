# Geth

adalah 

## Kebutuhan Dev (Mac Os)
1. Geth
   
   - `brew tap ethereum/ethereum`
   - `brew install ethereum`
   - cek versi 
       ```vim 
       geth version
       puppeth version
       ```
2. VSCode 
3. Google Chrome
4. Internet
5. Metamask (optional)
6. Git (optional)

## Membuat & Konfigurasi Node
### yang akan dilakukan
1. buat node1
2. buat node2
3. buat boot node 
4. menghubungkan semua node
5. membangun jaringan (build network)
6. test network

### Tahapan 
1. Membuat Node, berikut tahapannya: 
   
   - buat direktori `node1` dan `node2` dalam workspace mu
   - masuk dalam direktori node1 dan jalankan perintah berikut 
      ```vim
      geth --datadir "./data" account new
      ```

      Hasil
       ```vim
       INFO [09-19|18:44:51.790] Maximum peer count                       ETH=50 LES=0 total=50
       Your new account is locked with a password. Please give a password. Do not forget this password.
       Password: 
       Repeat password: 

       Your new key was generated

       Public address of the key:   0x60a8c0F8657802D5C2961A9d997f4145BDCE82e6
       Path of the secret key file: data/keystore/UTC--2022-09-19T11-44-58.541319000Z--60a8c0f8657802d5c2961a9d997f4145bdce82e6

       - You can share your public address with anyone. Others need it to interact with you.
       - You must NEVER share the secret key with anyone! The key controls access to your funds!
       - You must BACKUP your key file! Without the key, it's impossible to access account funds!
       - You must REMEMBER your password! Without the password, it's impossible to decrypt the key! 
       ```
   -  jangan lupa simpan `public key` dan password  
   - lakukan hal yang sama dengan point 2 dan 3 pada node2

2. Setup Genesis Block
   
   Membuat genesis file -> merupakan file yang berisi semua informasi network. Disini kita akan gunakan perintah `puppeth`. puppeth adalah ethereum private network manager yang menyediakan fitur pembuatan genesis block, bootnode, miner, ethstats.
   
   - Jalankan perintah `puppeth` dalam command line, dan ikuti tahapannya.
        ```vim
        ❯ puppeth
        +-----------------------------------------------------------+
        | Welcome to puppeth, your Ethereum private network manager |
        |                                                           |
        | This tool lets you create a new Ethereum network down to  |
        | the genesis block, bootnodes, miners and ethstats servers |
        | without the hassle that it would normally entail.         |
        |                                                           |
        | Puppeth uses SSH to dial in to remote servers, and builds |
        | its network components out of Docker containers using the |
        | docker-compose toolset.                                   |
        +-----------------------------------------------------------+

        Please specify a network name to administer (no spaces, hyphens or capital letters please)
        > jethnodes

        Sweet, you can set this via --network=jethnodes next time!

        INFO [09-19|19:12:03.562] Administering Ethereum network           name=jethnodes
        WARN [09-19|19:12:03.562] No previous configurations found         path=/Users/jee/.puppeth/jethnodes

        What would you like to do? (default = stats)
        1. Show network stats
        2. Configure new genesis
        3. Track new remote server
        4. Deploy network components
        > 2

        What would you like to do? (default = create)
        1. Create new genesis from scratch
        2. Import already existing genesis
        > 1

        Which consensus engine to use? (default = clique)
        1. Ethash - proof-of-work
        2. Clique - proof-of-authority
        > 2

        How many seconds should blocks take? (default = 15)
        > 5

        Which accounts are allowed to seal? (mandatory at least one)
        > 0x60a8c0F8657802D5C2961A9d997f4145BDCE82e6
        > 0x

        Which accounts should be pre-funded? (advisable at least one)
        > 0xFe1860B0513cd400aEd1486550e771aC0295f927
        > 0x

        Should the precompile-addresses (0x1 .. 0xff) be pre-funded with 1 wei? (advisable yes)
        > yes

        Specify your chain/network ID if you want an explicit one (default = random)
        > 14333
        INFO [09-19|19:12:57.858] Configured new genesis block 

        What would you like to do? (default = stats)
        1. Show network stats
        2. Manage existing genesis
        3. Track new remote server
        4. Deploy network components
        > 2

        1. Modify existing configurations
        2. Export genesis configurations
        3. Remove genesis configuration
        > 2

        Which folder to save the genesis spec into? (default = current)
        Will create jethnodes.json
        > jethnodes
        INFO [09-19|19:14:54.493] Saved native genesis chain spec          path=jethnodes/jethnodes.json

        What would you like to do? (default = stats)
        1. Show network stats
        2. Manage existing genesis
        3. Track new remote server
        4. Deploy network components
        > 
       ```
    - Jika sudah maka kamu akan melihat folder jethnodes yang berisis genesis block file bernama `genesis.json`

3. Inisialisasi Geth
   
   - Masuk ke direktori node1 dan jalankan perintah `geth --datadir ./data init ../genesis.json`
   - Jalankan hal yang sama pada node2

4. Membuat Bootnode
   Digunakan untuk mengkoneksikan semua node
   - buat directori untuk boot node dalam project direktori sebutlah `bnode`
   - buat boot.key dengan masuk dalam direktori `bnode`, kemudian jalankan perintah `bootnode -genkey boot.key`
   - jalankan boot node, `bootnode -nodekey boot.key`, to specific ip and port `bootnode -nodekey "boot.key" -verbosity 7 -addr "127.0.0.1:30301"`

5. koneksi semua node
   - simpan tiap password dalam file `password.txt` kemudian letakkan dalam direktori node masing-masing.
   - jalankan perintah berikut dalam tiap node
     
     Node 1
     ```vim
     geth --networkid 14333 --datadir "./data" --bootnodes enode://b3f21f844e904a57224cacef15882cd757695dffcbe56e3e20fd4829d2d7de8a6db3c6e724e315e5303e8f7f2c0f7ca8ac3d920295bf80e864c3be918795337f@127.0.0.1:30301 --port 30303 --ipcdisable --syncmode full --http  --allow-insecure-unlock --http.corsdomain "*" --http.port 8545 --unlock 0x60a8c0F8657802D5C2961A9d997f4145BDCE82e6 --password password.txt --mine --authrpc.port 8551 console
     ```

     Node2
       ```vim
     geth --networkid 14333 --datadir "./data" --bootnodes enode://b3f21f844e904a57224cacef15882cd757695dffcbe56e3e20fd4829d2d7de8a6db3c6e724e315e5303e8f7f2c0f7ca8ac3d920295bf80e864c3be918795337f@127.0.0.1:30301 --port 30304 --ipcdisable --syncmode full --http  --allow-insecure-unlock --http.corsdomain "*" --http.port 8546 --unlock 0xFe1860B0513cd400aEd1486550e771aC0295f927 --password password.txt --authrpc.port 8552 console  
     ```

     Bnode
     ```vim
     bootnode -nodekey boot.key -verbosity 7 -addr "127.0.0.1:30301"
     ```
   
     *catatan : pastikan replace enode jika enode saat jalankan perintah bnode berubah, hasilnya harusnya muncul perubahan / stream network pada bnode

6. Deploy Smart Contract
   - Buka remix editor https://remix.ethereum.org/
   - buat script berikut
 
     ```sol
     // SPDX-License-Identifier: MIT

      pragma solidity ^0.8  ;


      contract SimpleStorage{
         uint storedData;

         function set(uint x) public {
               storedData = x;
         }

         function get() public view returns (uint){
            return storedData;
         }
      }
     ```

   - Klik button `solidity compiler` pada sidebar
   - Kilk button `Compile SimpleStorage.sol`
   - Klik button `Deploy & Run` pada sidebar
   - Pilih environment `External Http Provider`
   - Pilih Existing Account
   - Klik button Deploy
   - Selesai 
     - pada bagian log remix ide akan nampak transaksi kita
     - pada bagian terminal node1 tampak ada contract submit

## Tambahan
Jika akun balance anda 0, maka anda bisa modifikasi file genesis.json (setelah dibuat) dengan mengisi balance value dibawah attribute alloc, misal sbb:
```json
"alloc": {
    "0000000000000000000000000000000000000000": {
      "balance": "0x1000000000000000000000000000"
    },
    "0000000000000000000000000000000000000001": {
      "balance": "0x1000000000000000000000000000"
    },
    "0000000000000000000000000000000000000002": {
      "balance": "0x1000000000000000000000000000"
    },
```
Seletah itu baru menjalankan `geth init` seperti pada point ke-3 (Inisiasi Geth). Jika sudah anda bisa cek account balance pada console geth dengan perintah :

```bash
# Check existing account
eth.accounts

# check balance
eth.getBalance(eth.accounts[0])

```

### Perintah terkait
1. Membuat Akun `personal.newAccount()`
2. Melihat Akun yang ada `eth.accounts`
3. Melihat akun balance `eth.getBalance(eth.accounts[0])`
4. Melihan akun balance sesuai satuannya `web3.fromWei(eth.getBalance(eth.accounts[0]))`
5. Melihat informasi node `admin.nodeInfo`
6. Melihat informasi peer `admin.peers`
7. Melihat total block `eth.blockNumber`
8. Melihat block tertentu `eth.getBlock(22)`
