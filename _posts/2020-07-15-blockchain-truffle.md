---
title:  "[Blockchain] 스마트 컨트렉트 제작 프레임 워크 Truffle"
categories:
  - Blockchain
tags:
  - blockchain
  - truffle
  - ethereum
header:
  teaser: /assets/images/truffle.jpg
  image: /assets/images/truffle.jpg
---  
이더리움 서버에서 동작하는 컨트랙트를 제작해보자

# Truffle

트러플은 이더리움 기반 디앱을 쉽게 개발할 수 있도록 도와주는 블록체인 프레임워크이다.

JavaScript기반 어플리케이션을 개발할 때 참조하게 될 사이트 목록

- [http://www.trufflesuite.com](http://www.trufflesuite.com)
- [https://web3js.readthedocs.io/en/1.0/index.html](https://web3js.readthedocs.io/en/1.0/index.html)
- [https://solidity.readthedocs.io/en/latest/](https://solidity.readthedocs.io/en/latest/)

# 설치

node 버전 확인 후 트러플은 개발도구이므로 글로벌하게 설치한다. 이후 트러플도 버전 확인

```bash
node -v

npm install -g truffle

truffle version
```

# 시작

예제 폴더를 생성한 뒤 초기화를 진행해준다. `truffle init`은 `npm init`과 같은 역할을 한다고 보면 된다.

```bash
mkdir dapp-example

cd dapp-example

truffle init
```

이후 생성되는 프로젝트의 형태는 다음과 같다

![dir1]({{ site.baseurl }}/assets/images/Blockchain_1/dir1.png)

# 비교

일반적인 자바 웹 개발과 비교하면 이렇게 볼 수 있다.


<table>
  <thead>
    <tr>
      <th>환경</th>
      <th>자바 웹 개발</th>
      <th>이더리움 컨트랙트 개발</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>언어</td>
      <td>Java</td>
      <td>Solidity</td>
    </tr>
    <tr>
      <td>Framework</td>
      <td>Spring MVC</td>
      <td>Truffle(solc-js)</td>
    </tr>
    <tr>
      <td>server</td>
      <td>Tomcat</td>
      <td>Ganache</td>
    </tr>
    <tr>
        <td>기타</td>
        <td>WAS, Database</td>
        <td>이더리움 : 분산공유 네트워크</td>
    </tr>
  </tbody>
</table>

블록체인 어플리케이션이라고 해서 다른 어플리케이션과 다른점은 거의 없다.

그저 블록체인과 인터페이스가 이루어진다는 것일 뿐

# Hello World!

contracts 폴더 내부에 `HelloWorld.sol` 파일을 생성한다.

```jsx
pragma solidity ^0.5.16;

contract HelloWorld {

}
```

solidity 컴파일러의 버전을 맞추려면 프로젝트 내부에 있는 `truffle-cofnig.js`파일에서 컴파일러 부분을 수정해야 한다.

```jsx
// Configure your compilers
  compilers: {
    solc: {
      version: "0.5.16",    // Fetch exact version from solc-bin (default: truffle's version)
      // docker: true,        // Use "0.5.1" you've installed locally with docker (default: false)
      // settings: {          // See the solidity docs for advice about optimization and evmVersion
      //  optimizer: {
      //    enabled: false,
      //    runs: 200
      //  },
      //  evmVersion: "byzantium"
      // }
    }
  }
```

이후 truffle 버전 확인을 하면 설정한 값으로 변경된 것을 확인할 수 있다.

이제 생성해둔 HelloWorld.sol 코드에 다음과 같은 내용을 넣는다.

```jsx
pragma solidity ^0.5.16;

contract HelloWorld {
    string public greeting;

    constructor(string memory _greeting) public{
        greeting = _greeting;
    }
    
    function setGreeting(string memory _greeting) public{
        greeting = _greeting;
        
    }
    
    function say() public view returns(string memory){
        return greeting;
    }

}
```

이후 컴파일은 `truffle compile` 명령어로 진행한다.

컴파일을 마치면 build 디렉터리가 생성되고 내부에는 json 파일이 존재한다.

![dir2]({{ site.baseurl }}/assets/images/Blockchain_1/dir2.png)

json 파일에는 ABI와 Bytecode가 있는데 Bytecode는 블록체인 내부에 올라가고 ABI는 웹 어플리케이션에서 사용된다.

# 배포 (Ganache)

컴파일한 코드의 배포는 가나쉬를 이용한다.

가나쉬는 로컬 가상 이더리움이다.

로컬에서 tomcat으로 웹서버를 배포하는 것 처럼 가나쉬를 이용해 컨트렉트를 배포할 수 있다.

배포시에는 migrations 디렉터리 아래에 `순번_파일이름.js` 형태의 배포 스크립트 파일을 생성해야 한다.

```jsx
// not file name, input contarct name
const helloWorld = artifacts.require("HelloWorld");

module.exports = function (deployer) {
    deployer.deploy(helloWorld);
}
```

이때 require 내부에는 파일 이름이 아닌 contract 이름이 들어가야 한다.

이후 어떤 서버에 배포할지 선택하여 이를 `truffle-config.js`에서 설정해야한다.

```jsx
networks: {
    // Useful for testing. The `development` name is special - truffle uses it by default
    // if it's defined here and no other network is specified at the command line.
    // You should run a client (like ganache-cli, geth or parity) in a separate terminal
    // tab if you use this network and you must also set the `host`, `port` and `network_id`
    // options below to some value.
    //

    development: {
     host: "127.0.0.1",     // Localhost (default: none)
     port: 7545,            // Standard Ethereum port (default: none)
     network_id: "5777",       // Any network (default: none)
    },

    // Another network with more advanced options...
    // advanced: {
      // port: 8777,             // Custom port
      // network_id: 1342,       // Custom network
      // gas: 8500000,           // Gas sent with each transaction (default: ~6700000)
      // gasPrice: 20000000000,  // 20 gwei (in wei) (default: 100 gwei)
      // from: <address>,        // Account to send txs from (default: accounts[0])
      // websockets: true        // Enable EventEmitter interface for web3 (default: false)
    // },

    // Useful for deploying to a public network.
    // NB: It's important to wrap the provider as a function.
    // ropsten: {
      // provider: () => new HDWalletProvider(mnemonic, `https://ropsten.infura.io/v3/YOUR-PROJECT-ID`),
      // network_id: 3,       // Ropsten's id
      // gas: 5500000,        // Ropsten has a lower block limit than mainnet
      // confirmations: 2,    // # of confs to wait between deployments. (default: 0)
      // timeoutBlocks: 200,  // # of blocks before a deployment times out  (minimum/default: 50)
      // skipDryRun: true     // Skip dry run before migrations? (default: false for public nets )
    // },

    // Useful for private networks
    // private: {
      // provider: () => new HDWalletProvider(mnemonic, `https://network.io`),
      // network_id: 2111,   // This network is yours, in the cloud.
      // production: true    // Treats this network as if it was a public net. (default: false)
    // }
  },
```

해당 파일에 입력되는 정보는 가나슈를 실행하고 나오는 정보를 입력하면 된다.

![ganachu]({{ site.baseurl }}/assets/images/Blockchain_1/ganachu.png)

설정을 마치면 배포 명령어인 `truffle migrate --network development`를 입력하면 된다. `--network` 옵션은 타겟 서버를 지정하는 옵션이며 다른 설정을 하고 해당 설정 이름을 넣으면 그에 맞는 서버로 배포가 진행된다.

그러나 이부분에서 에러가 발생하는데 에러 내용은 다음과 같다.

Error:  *** Deployment Failed ***

"HelloWorld" -- Invalid number of parameters for "undefined". Got 0 expected 1!.

이는 HelloWorld 컨트랙트의 생성자에서 string 형태의 매개변수를 받게 되어있는데 배포 스크립트에는 그에 대한 내용이 없기 때문이다.

매개변수의 전달은 아래와 같이 하면 된다.

```jsx
module.exports = function (deployer) {
    deployer.deploy(helloWorld, "hello world!"); // string 타입으로
}
```

이후 `truffle migrate --reset`명령으로 리셋 후 배포를 해준다.

# 배포된 컨트랙트 메서드 실행

`truffle networks` 명령어로 배포된 컨트렉트의 네트워크를 확인할 수 있다.

```bash
D:\github\blockchain\main\truffle\dapp-example>truffle networks

Network: development (id: 5777)
  HelloWorld: 0xF9b1d975E17d4D329faE3CE105Ea82B4bD6a5341
  Migrations: 0xc7046386cD581a7DDa8f65333a512e5B5f74C8BC
```

해당 네트워크와 연결하기 위해서는 `truffle console --networks 네트워크이름` 명령어를 사용하면 된다.

```bash
D:\github\blockchain\main\truffle\dapp-example>truffle console --network development
truffle(development)>
```

접속후에 컨트렉트의 인스턴스를 생성해야 한다.

생성한 인스턴스에서 메서드를 호출하면 동작이 잘 되는 것을 확인할 수 있다.

```bash
truffle(development)> let hello = await HelloWorld.at("0xF9b1d975E17d4D329faE3CE105Ea82B4bD6a5341")
undefined
truffle(development)> hello.say()
'hello world!'
```

하지만 이렇게 메서드를 하나씩 호출하는 것은 매우 비효율적이며 이것을 해결하기 위해 단위 테스트를 진행한다.

test디렉터리 내부에 스크립트 파일을 작성한다.

```jsx
// test_hello.js
const helloWorld = artifacts.require("HelloWorld");

contract("HelloWorld", function(accounts){

    before(async () =>{
        this.instance = await helloWorld.deployed();
    });

    // 단위 테스트 케이스
   it("should be initialized with correct value", async () => {
        const greeting = await this.instance.greeting();

        assert.equal(greeting, "hello world!", "Wrong initialized value!");
    });

});
```

작성을 마치면 `truffle test`명령어로 단위 테스트를 진행한다.

```bash
D:\github\blockchain\main\truffle\dapp-example>truffle test
Using network 'development'.

Compiling your contracts...
===========================
> Compiling .\contracts\HelloWorld.sol
> Compiling .\contracts\Migrations.sol
> Artifacts written to C:\Users\wjrmf\AppData\Local\Temp\test-2020614-22448-kgkmvs.b8iah
> Compiled successfully using:
   - solc: 0.5.16+commit.9c3226ce.Emscripten.clang

  Contract: HelloWorld
    √ should be initialized with correct value (51ms)

  1 passing (155ms)
```

만약 실패한다면 아래와 같은 결과가 나온다.

```bash
# 실패를 유도하기 위해 '!' 하나 추가
D:\github\blockchain\main\truffle\dapp-example>truffle test
Using network 'development'.

Compiling your contracts...
===========================
> Compiling .\contracts\HelloWorld.sol
> Compiling .\contracts\Migrations.sol
> Artifacts written to C:\Users\wjrmf\AppData\Local\Temp\test-2020614-13644-17jokth.2majf
> Compiled successfully using:
   - solc: 0.5.16+commit.9c3226ce.Emscripten.clang

  Contract: HelloWorld
    1) should be initialized with correct value
    > No events were emitted

  0 passing (144ms)
  1 failing

  1) Contract: HelloWorld
       should be initialized with correct value:

      Wrong initialized value!
      + expected - actual

      -hello world!
      +hello world!!

      at Context.<anonymous> (test\test_hello.js:13:16)
      at processTicksAndRejections (internal/process/task_queues.js:97:5)
```

단위테스트를 하나 더 추가해보자.

```jsx
//test_hello.js
const helloWorld = artifacts.require("HelloWorld");

contract("HelloWorld", function(accounts){

    before(async () =>{
        this.instance = await helloWorld.deployed();
    });

    // 단위 테스트 케이스
    it("should be initialized with correct value", async () => {
        const greeting = await this.instance.greeting();

        assert.equal(greeting, "hello world!", "Wrong initialized value!");
    });

		// 추가된 단위 테스트
    it("should change the greeting", async () => {
        const val = "Hello, JSH!";

        // 상태를 바꾸는 함수는 계정이 필요하다
        await this.instance.setGreeting(val, {from: accounts[0]});
        const greeting = await this.instance.say();

        assert.equal(greeting, val, "dose not change the value!");
    });

});
```

테스트 결과는 다음과 같다.

```bash
D:\github\blockchain\main\truffle\dapp-example>truffle test
Using network 'development'.

Compiling your contracts...
===========================
> Compiling .\contracts\HelloWorld.sol
> Compiling .\contracts\Migrations.sol
> Artifacts written to C:\Users\wjrmf\AppData\Local\Temp\test-2020614-16896-aj6nds.ye72p
> Compiled successfully using:
   - solc: 0.5.16+commit.9c3226ce.Emscripten.clang

  Contract: HelloWorld
    √ should be initialized with correct value (54ms)
    √ should change the greeting (191ms)

  2 passing (331ms)
```

# 참고

[블록체인 Dapp 개발에 트러플 활용하기_기본편](https://www.inflearn.com/course/Dapp-Truffle-blockchain-basic/lecture/22016)

[Sweet Tools for Smart Contracts](https://www.trufflesuite.com/)
