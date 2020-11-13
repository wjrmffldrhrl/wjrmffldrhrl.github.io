---
title:  "[Blockchain] ERC20 Documentation"
categories:
  - Blockchain
tags:
  - token
  - ethereum
header:
  teaser: /assets/images/ethereum.jpg
  image: /assets/images/ethereum.jpg
---  


# ERC20

이더리움 토큰의 표준인 ERC-20

2015-11-19에 생성되었다.

OpenZeppelin Contracts는 ERC20과 관련된 contract를 제공해준다.

우리 소유의 ERC20 토큰을 생성하기 위해서는 아래와 같은 형태로 Contract를 제작하면 된다.

```jsx
// contracts/GLDToken.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract GLDToken is ERC20 {
    constructor(uint256 initialSupply) public ERC20("Gold", "GLD") {
        _mint(msg.sender, initialSupply);
    }
}
```

ERC20을 상속받아서 원하는 이름과 심볼을 가진 토큰을 생성하고 나아가 확장 기능 또한 구현할 수 있다.

ERC20이 어떤 기능을 가지고 있는지 살펴보자

# IERC20

IERC20은 ERC20의 인터페이스이며 [EIP](http://wiki.hash.kr/index.php/EIP)에 정의되어있다.

## totalSupply() → uint256

존재하는 모든 토큰의 양을 반환한다.

## balanceOf(address account) → uint256

account가 소유하고 있는 토큰의 양을 반환한다.

account에는 계정 주소값이 들어갈 것이다.

## transfer(address recipient, uint256 amount) → bool

`amount` 만큼의 토큰을 함수를 호출한 계정으로부터 `recipient` 계정으로 전송한다.

전송의 성공 여부에 따라 bool 값을 반환한다.

Transfer 이벤트를 발생시킨다. 

## allowance(address owner, address spender) → uint256

기본적으로는 0의 값을 가진다.

`spender` 가 `owner`대신 사용 가능한 토큰의 남은 갯수를 반환한다.

이 값은 `approve`나 `transferFrom`이 호출될 때 변경된다. 

## approve(address spender, uint256 amount) → bool

함수를 호출한 사용자의 토큰에서 `spender`가 사용 가능한 양을 설정한다.

성공 여부에 따라 bool값을 반환한다.

Approval 이벤트를 발생시킨다.

이 함수를 사용해서 허용량을 설정할 때 이전 설정값과 현재 설정값을 동시에 사용하려는 경우가 생길 수 있으므로 주의해야 한다. 

## transferFrom(address sender, address recipient, uint256 amount) → bool

`sender`의 토큰을 `recipient`에게 `amount` 만큼 전송한다.

이때 전송되는 토큰의 양만큼 함수 호출자의 허용량에서 제외한다.

성공 여부에 따라 bool 값을 반환한다.

## Transfer(address from, address to, uint256 value)

이 함수는 `value`만큼의 토큰이 이동될 때 (`from` → `to`) 호출된다.

`value`는 0일 수 있다

## Approval(address owner, address spender, uint256 value)

이 함수는 `approve` 함수에 의해 `sender`의 (`spedner`소유의 토큰)허용량이 설정될 때 호출된다.

`value`는 새로운 설정 값이다.

# ERC20

IERC20 인터페이스를 구현한 것이다.

ERC20에서는 토큰 허용량 설정 함수를 호출시 관련된 이벤트가 발생되는데, 이 이벤트를 잘 사용한다면 모든 계정에 대한 허용을 재구성할 수 있다. EIP의 다른 구현체들은 이러한 기능을 제공하지 않는 점에서 차별성을 가지고 있다.

## constructor(string name, string symbol)

토큰의 이름(`name`)과 심볼(`symbol`)을 설정해주는 생성자이다. 기본값으로 `decimals`는 18의 값을 가진다. 

여기서 `decimals`는 소수점값을 의미한다. 예를들어 505의 값에 `decimals`이 2라면 5.05로 표현될 것이다. → 505/10**2

만약 decimals 값을 변경하고 싶다면 `_setupDecimals`를  사용하면 된다.

여기서 설정되는 값은 모두 변경 불가능하다. 첫 호출 때 해당 함수 내에서 설정을 마쳐야 한다.

## name(), symbol(), decimals()

각각의 값들을 반환해준다.

`name()`은 토큰의 이름을, `symbol()`은 토큰의 심볼을, 그리고 `decimals()`는 deciamls값을 반환해준다.

## increaseAllowance(address spender, uint256 addedValue) → bool

허용량을 늘려주는 함수이다.

`spender`에게 함수를 호출한 사용자가 제공하는 것이다.

이 함수는 `approve` 함수의 문제점을 보완할 대안이다. `approve`함수와 마찬가지로 `Approval`이벤트를 발생시킨다.

- `spender`값으로 zero address를 입력할 수 없다.

## decreaseAllowance(address spender, uint256 subtractedValue) → bool

허용량을 감소시켜주는 함수이다.

`increaseAllowance`함수와 같은 개념이지만 반대로 감소시키는 역할을 한다.

- `spender`값으로 zero address를 입력할 수 없다.
- `spender`는 `subtractedValue`보다 많은 허용량을 가지고 있어야 한다.

## _transfer(address sender, address recipient, uint256 amount)

`amount`만큼의 토큰을 `sender`로부터 `recipient`에게 전송한다.

이 함수는 `transfer`함수와 동일한 기능을 가진다.

## _mint(address account, uint256 amount)

`amount`만큼의 토큰을 생성하고 `account`에게 할당해준다.

전체 제공량을 증가시킨다.

이 함수는 `transfer`이벤트를 발생시키고 해당 이벤트의 `from`에는 zero address가 입력된다.

- `account`는 zero address가 될 수 없다.

## _burn(address account, uint256 amount)

`account`가 소유한 토큰을 `amount` 만큼 제거한다.

`transfer` 이벤트를 발생시키며 해당 이벤트에는 zero address가 입력된다.

- `account`는 zero address가 될 수 없다.
- `account`는 `amount`이상의 토큰을 가지고 있어야 한다.

## _approve(address owner, address spender, uint256 amount)

`spender`에게 `owner`의 토큰을 `amount`만큼 허가해준다.

이 함수는 `approve`와 동일한 기능을 가지며 `Approval` 이벤트를 발생시킨다.

- `owner`는 zero address가 입력될 수 없다.
- `spender`는 zero address가 입력될 수 없다.

## _*setupDecimals(uint8 decimals*)

기본값으로 18을 가지는 `decimals`의 값을 다른 값으로 설정한다.

해당 함수는 오직 `constructor`에서만 호출할 수 있다.

## _beforeTokenTransfer(address from, address to, uint256 amount)

토큰의 이동이 발생하기 전에 이 함수를 호출시킬 수 있다.

여기에는 minting과 burning도 포함된다.

`from`과 `to`의 값에 따라 상황이 다르다.

- `from`과 `to`가 둘다 non-zero인 경우 →  `amount`만큼의 토큰이 `from`으로부터 `to`에게 전송될 때
- `from`만 zero인 경우 → `amount`만큼의 토큰이 `to`에게 mint될 때
- `to`만 zero인 경우 → `from`소유의 토큰이 `amount`만큼 burn될 때
- `from`과 `to`가 동시에 zero인 경우는 없다.

자세한 내용은 아래 주소를 참고하자

[Extending Contracts](https://docs.openzeppelin.com/contracts/3.x/extending-contracts#using-hooks)

# IERC20에서 상속받은 함수들

- totalSupply() → uint256
- balanceOf(address account) → uint256
- transfer(address recipient, uint256 amount) → bool
- allowance(address owner, address spender) → uint256
- approve(address spender, uint256 amount) → bool

# Reference

- [https://eips.ethereum.org/EIPS/eip-20](https://eips.ethereum.org/EIPS/eip-20)
- [https://docs.openzeppelin.com/contracts/3.x/erc20](https://docs.openzeppelin.com/contracts/3.x/erc20)
- [https://docs.openzeppelin.com/contracts/3.x/api/token/erc20](https://docs.openzeppelin.com/contracts/3.x/api/token/erc20)