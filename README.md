# Bridge API

The bridge API is a REST API for fetching proofs from Avail's Kate RPC and Succinct API to submit on Ethereum or
any off-chain proof verification.

## Deploying the bridge API

* Create an `.env` file according to the `.env.example`
* To build the service:

```bash
# for developing, make a debug build
cargo build
# and run it!
cargo run
```

* Or instead, make release builds for production:

```bash
cargo run --release
# you can use maxperf to optimize for runtime performance:
cargo run --profile maxperf
# you can use RUSTFLAGS to use CPU-native optimizations:
RUSTFLAGS="-C target-cpu=native" cargo run --profile maxperf
```

## Usage

* The bridge API operates on the 8080 port by default (can be configured).

### Liveness of the server

* To verify that the API is live, you can query the root like:

    * Request

      `GET /`

      ```bash
      # curl <endpoint URL>
      curl http://localhost:8080
      ```

    * Response

      ```json
      {"name":"Avail Bridge API"}
      ```

### Get current Ethereum head

* To get the latest Ethereum block number, query:
    * Request
      `GET /eth/head`

      ```bash
      # curl <endpoint URL>/eth/head
      curl http://localhost:8080/eth/head
      ```
        * Response

      ```json
      {"slot":4454752,"timestamp":1709191840,"timestampDiff":1716}
      ```

### Get current Avail head

* To get the latest Avail block number, query:
    * Request
      `GET /avl/head`

      ```bash
      # curl <endpoint URL>/avl/head
      curl http://localhost:8080/avl/head
      ```
        * Response

      ```json
      {"data":{"end":512738,"start":488581}}
      ```

### Generate Merkle Proof

* To generate a proof, simply query the `eth/proof` endpoint with the block hash and extrinsic index like (both are
  required):

    * Request

      `GET /eth/proof/:blockhash?index=`

      ```bash
      # curl "<endpoint URL>/eth/proof/<blockhash>?index=<tx_index>"
      curl "http://localhost:8080/eth/proof/0xf53613fa06b6b7f9dc5e4cf5f2849affc94e19d8a9e8999207ece01175c988ed?index=1"
      ```

    * Response

      ```json
      {
          "blobRoot": "0xaa4246713da25ab816ee6a00f3a523f48ce7b711120a7c3cf00e8851b9c99df2",
          "blockHash": "0xf53613fa06b6b7f9dc5e4cf5f2849affc94e19d8a9e8999207ece01175c988ed",
          "bridgeRoot": "0x0000000000000000000000000000000000000000000000000000000000000000",
          "dataRoot": "0x19b8a7551400209233c46f6d98abbd88c08e2d3f53a4cb9dfd5c6f3746ff53e5",
          "dataRootCommitment": "0x2ced6477d2b72909f36c9e88e32d33e33fb14526c0e3ed8c3a30a195f637e739",
          "dataRootIndex": 174,
          "dataRootProof": [
              "0xad3228b676f7d3cd4284a5443f17f1962b36e491b30a40b2405849e597ba5fb5",
              "0x51f84e7279cdf6acb81af77aec64f618f71029b7d9c6d37c035c37134e517af2",
              "0x69c8458dd62d27ea9abd40586ce53e5220d43b626c27f76468a57e94347f0d6b",
              "0x5a021e65ea5c6b76469b68db28c7a390836e22c21c6f95cdef4d3408eb6b8050",
              "0xf1f603a14a615fa262f91fa788be910a2347429cba6a39d9d2781190a92cd3bb",
              "0x83aeb54660d9c6158085a50947e76e4ac01c95fd9b30e6d3bc865810ba6e73c4",
              "0xd88ddfeed400a8755596b21942c1497e114c302e6118290f91e6772976041fa1",
              "0x7b9b465d4b6271ac97a54fcd2b74423c9150463e6c90b6b609500d696b9ae394"
          ],
          "leaf": "0x6aaf64fab0bd05b12cd95b298ce0bf1bdda0f385b81578b60f8242cdb5d1983e",
          "leafIndex": 0,
          "leafProof": [],
          "rangeHash": "0x4b49a4b090404b1e83797cb77286f91a728a4d9ace03f2855025ccf7523ed720"
      }
      ```

### Get Account/Storage proofs

* To get a proof, simply query the `/avl/proof/:message_id` endpoint with the message id:

* Request

  `GET /avl/proof/:messageId`

    ```bash
    # curl "<endpoint URL>/avl/proof/<messageId>"
    curl "http://localhost:8080/avl/proof/1"
    ```

    * Response

    ```json
    {
      "accountProof": [
        "0xf90211a0d71128955385a996e69dc034c49faa1a5ea16c3e9b49c75e3190fa7064368bf3a08b643fd3188594193a31af8852229d8b3d99f0044233b0f5fef2cb24a2034612a0d34e4a13c57e2b48c45893c4d74605d4875be1042416ec39bfeb0baef9a55bc2a00717485f8c420c6dda510df64e7cfaf850a06a42cdecf0cac5081307669d5dada0dd6f6a770d8db561aaf01874dbe218bb293ce7ac5d31e92c5ce441e24b0d9b5ca0c7adb6999b7a578a8859e91f17b1c9d5de1e6701e04d327438af5a72d636b86aa058e513e2cc186ac8182aa9c0b3fd0449704f6f7cf8143310e818321a704ffd88a05b60127bb0b82a45730180c07c82d2fe9cb97539e6ea8f39e18859a30a24b3cca024f59ca8820068f177980eadab1b902122d9406ab1d637bbfab668caa376ad02a0244994e81bcf4994fb6e5bb10e5971b5f40b34a7809d3ecf6bb65f93c449e463a0b660ec6605b80f257887c086d45dfb9ed0c6f1476725c1c484b202e23b59f85da072c20edc507ac36f7c092770a45c710cb678ed307569482700a8f7bb01149d09a0beb134ebf0f2a6999f66896d54ae03690a661b7931acebf83acf46d0f58f7af6a0bedd781f98b11996167ebd7a55f6d6860603cde37ca4cccc18e54907b2ab7227a016821791d1dcd70f4c041856dc9f98427b2f667433cda5d9ba2f328686c74334a003a42e9a646f5ae41a669ca79eb2dcedb0c642a3e179cca9075117aec62ec54980",
        "0xf90211a011db1d6200d4693fd1286eed061eb7da6486c95436458d585e5bcdd10fe6c2b4a085ff32ff8c62614a08df478d496959bdc624d5c865fc70e35437debbfa5f500aa09b30be94eb5bf434640817df16034b76384e487232b55edcb2ac35ae3ba69721a048b694b684e4c3e7272864cfb7eae4e65d67c17934bbb2d07b536b5edbf487dea00783663c68cbd05877c2b5f57aff3da672e25b37a70c815faafc96679eaaafcda05f991c76a7aa6d1fc5fd921ca6702b5734f33ddb183812b0a4b50261311c1824a05036b93b9cef1e8472d292fa5dad443f8c5d2d9f313e89921d7972f089702344a04693c40dcc8e789892926f266538f8ca5a64bdc1a1cd5df4fa9c38397f4fe933a03650a3cca2182e05a8b9e8a3acd287958fc8f94747b279e58fcf4f3ed52207b9a07cc490d6b284735776c75cc965038ece8ab86c9541a0515bcfdea6ebe644e252a0419b90f546dbce34359261f5dc9758dc4a07ff70910195ea8e6d698eeefdd98ea04740c3529d5db08d87949681534ad7f0d47e88d69930762f4d691ca926a6d57ea04d4ab6b44335560cb3c99d7b094e034e26b806fa976b9cabe46b5d201a0e6675a0c757b0a786f7733cf2c27ffb87f410903f41f39f490c464e74ee52db8eedb5e8a01148e8b3133ca1bb4f0864e25368a5457d42b082f861522a26502c7d495f1d2ba0821e19579f54cd679e9e9343fc12daf3d7e914eeac6aafd9d4537aae6c72e5c180",
        "0xf90211a0bcc9cb508af5f1ece305f25b646033e2836f00d64e4d8d99ba2e0ead3a3c142aa033016b7a6e32ef23580e936f606517c9890c94780ed3803bca113562511b2725a0015c152d93a275a868ab1de77982a772bb17b9c31f8e3c6535920e27b2ad691ea0e3772a969fa342f8e575ac4133ab64dd44c2f95334b467734d8ebb5911ee4623a07b47dc5a9531aad63fea49e101ebb35081b9146a3c382cc320070a71908e456ea020f0624d5075eea63ad13b1eb9fe5d36312a45315da9ff7b007ef2b994977ad9a034238bf9346eb5437042832a8afa97b50451f1083161340188ef2c483d941b4ba02a626328a031090870d7959e5b40d9d903ecdc9bad390bd4974ab9113ff518f3a0893c5babe5b86cb7cb32a7712439f925ea10571786d70865e3cc334472593864a06fc90c294f2dac25e6bfb5e9b101e1281c97c8229a3e291b539d760026f895b9a09d911728e92446ac2efb2cd31a683431e876a4955b4c22c907f71af28a9d6fc8a0514bf1fdae988ca731ab397b67c85fe049c776c1185e23e25846644057f1aaeca025a52dbeff1ce02625f46b97849a9040c3666301676c0d5bc6a21889e9a648c9a0456509e4fcac6ff02ed91d071771a007785b17ca6f60cc40421e667714a3ad80a06a6c8bf77f8a2e62b81cc2033ca3bfa5c60490e7f0f43ca1a6f293b47032f74ca0e1298dcc8c1a811793766eb7e7106b97f7ff978957d73cab0d5406bff79e88ba80",
        "0xf90211a0453a58b9b230737ee8aa629ee5aced2f04027e874074eacc354bd5727bdac050a0a8a2cebeeb6b63f05c790cac0e1eb767503b6b0bb952d1f941ea4d967bc031e0a0e06404847fd9f8eea8b478850cda2b41c9228f1da1b791315b4e9255a7183869a0e1c65c5fa5ff313849b6c7d7f538c78f15c21a036452c2ec15890652463f8184a0cc9e649eab801e4e6a96c8f3a94c5570c8050953cf0d6c9815b9f14d5761c716a0d5e083579fb0f00730ddca7718850573aa1078524e8d2d605c8444c8638bc1fda076099e62259f3a72d58b3403dc011fc700900c937f2c44da7e378d829f7834b0a00083f8e2da8f6ac4de7f6b49ff807bca69db05061447ee9436b6b422c3eec564a0375e94ee653abb2062e704e953c1ba26edc569f901725bb8b32bbb20c725b1a9a05096be20c017e7830d39cb26e672f41a0ad4ef0e502935983191783cb1da2f4da0c493f6b1b2c6e11c7c6b580f3fd723a8dd6ebe99adacbcb379649520b6db9bc1a0892670cf61716e3d2177c7fe715a6b7bb9fd77533c1a237d31842738709bdd15a04dff26f34637ec85d5c87d7110f21bd86eb41078ac66f1aec8e57f7ccca6e3daa01adb37fb6e266de1e407160490597db51dd5e35deffb8b61b3cbf98b03e8ddeca0e07addd5c6b4482270ea4dded91213b974db9e8df29ea2ee49d7c575749a9289a073551d91f2a2ecb9de694d59ceccd112d6205cc822d84a6fa9863e416e0873f880",
        "0xf90211a02994eee918b83f44b0ef8fd984687d8238981f2dd0acf2f1748e7387c4cc1ad8a0eb0813f93256145fef162b9dd3facfe66e4dc8ab6af06e40aa09223e96c24075a04b96866754918f8cbb6d56f1ecc491ad10d805de0f2907240f79b9d8eb1f3020a062d2f1c3a188a0a9864d408eacae4948c0303a698f7010d848d6f63fdc20be37a03ea56e5e77062a3d674863086d66ab9d1ac10c91437deeab7bcf7fb0af2c2ef1a00f19e169093d4cdf6c77576e20261e3635e72f091fba237c60bee95133e9529ba06439fd86d88c693fad88439c3cb9781786a60a9b4897f14ac97d959dcc145fdba03d4bbdd9b6be519cbe684df64fc11c38fcb572c16f8de92676bab7113bf89109a08947a5210863f90607e66529624c15ce4a0564b3b7702d85f2fdd6a1c12f6b0ca0cab289c7db03b8fd7b613bef1c9e50f929f63a180d8e9fa7f28780be5058b89aa02f4b38922e15321e94ccb241f8e7cb4ab5d74eab9a8c07e48249f9d83c7d6f2da07ecf190a86d417734d31c9bd655112f01cda22cca43e8343b773fa197c3007f5a09acc17f9d7260602cf51aaa8d2d8bc9ee3f699b34d03f51ccb34548d61b51e5ea0a34f826e57c7fbce32b47e87cc3b2a52d548a388e1a908d9ecfeabcd5935cc5ca05578757fc6ea40bdddef901823bbf46fb357e825e208bb83bec85bae7d88d8f5a08110a321fe60d2ad02283f9dd5878b3b6ad101483f18cb6b6021ca359f34699e80",
        "0xf901b180a012d4abe8e0be9f1ca3024c8c734d5201d4312415207baa191632aaaa22f877a780a0d7e0b129d1581066c1ad75df317f8f362bccbb9de3bfff562bba3e8797eb5538a0398b1d9a44c778bd0f658f430097347d57b9f64ce5943e06c8930d060445e666a0c9e721ebdeeefe03348a6dba6c789b25ad60c7e6d739e143bc0101bd888519bca0c49cb31cd553fc7d54c9e2e8335465a7607377c5844c424790cfdfb82060cf7aa0c1bacefdab5cd03d4e1ed4e829ad092d9b6a920f6f4e90a64a9692cc880e4cfea0044c45cabab3dc0d4669326119b78dca51316bf704837ac712739c73464831c8a0855ec46da10e0b04fcc94f2b54795b35f71891802ef5599afb92e3d2909a0ea5a004c86802b3e15cf748062ace9ce5e02acd0131ed86e30f12e6edf9fe0ab4fd6fa010c903eabf207c4f03cd97b3377ca35127e657bedd80991ed3015ca6d13c3553a00d3a77a656914bbddfa583e3841296c1a65a6977b6c4c0cda6bc34fc8bdbdb9580a0b04bed9588468743c5271a0a2ee90116911db7297e3165fa9513d8da9edd3b40a09fec2225beb9bacdcb4dfb5a186e412b7017c5bc41aefb2bc7604018b46c5c7280",
        "0xf851808080808080808080a0e505cae6797de2705a3a65c77ea7c9948f0f8930c14cf7b8e5679504de51062fa038a3cecdf51a92be5c24f16c7776211bf79a7a3bab9d0b9178b00eca71ddd76a808080808080",
        "0xf8669d3cdcf5995402ae9b130ce74b48fec0dff98226c7106c2618b3923cf8c4b846f8440201a07f9340d34eb810527d82b795d1ce74ee9fc8a727f5dbab7ad1399ff2ffe8fc87a0c95cf5b2451d3c325fc0b238248ce5d59c59374241561d45b7042b3189e4c413"
      ],
      "storageProof": [
        "0xf901318080a006a2e0d8e156dfbe4422b119dedc75f489184d9016e2608b4a5311416cdb51c080a09ddd70915eb71e1c868c88a5e19e1b60b8f7c12727c5db3829b5e38d770661aba0b15cf01f62cbd104e15831a1d5a4ee37093c493251983f17be2c773ec03760af80a053d7827419966e58ff537e3188131e441342bed0f491cbce461183c2cc94b507a01e2f406ae757e5ad8854495c415a87688cb69fbe6560af8e14fa31fc2dcd44b88080a09943ce5c17ce69fe140a111cc8436dd434034352c4dbefa7623d48593eae2987a013674f92f711a51bfd038a63208ef702497ccbf468405a9ad9378a57e03ab67ba005af7977817c87942f8cd44f542cf831671b8bc88763dc3fd3efb10c41497b02a079866ac4ff54c3062d8fbd4fa347961e9a905b4114a2ed9785e22a5c03f4ffb88080"
      ]
    }
   ```

### Map slot to Ethereum block number

* To map Ethereum slot to a block number:

    * Request

      `GET /beacon/slot/:slot_number`

      ```bash
      # curl <endpoint URL>/beacon/slot/<slotNumber>
      curl http://localhost:8080/beacon/slot/4448512
      ```

    * Response

      ```json
      {"blockNumber":5380093}
      ```
