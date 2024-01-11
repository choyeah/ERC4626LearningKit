# ERC4626의 개념

디파이 서비스가 활발해지자 예치를하고 이자를 받아가거나 인출을 하는 경우가 많아졌다.
이러한 상황에서 erc20 토큰 자체에 예치/인출/이자분배 등의 기능이 제안되어 탄생되었다.

https://soliditydeveloper.com/erc-4626

기존의 디파이 서비스에서는 이자 계산과 분배 등의 플랫폼 별도의 컨트랙트를 통해 처리되어야 했다.
이는 복잡한 계산과 추가적인 거래가 필요하며, 이로 인해 트랜잭션 비용이 증가하고 처리 시간이 길어질 수 있다.
반면, ERC-4626 표준을 사용한 토큰을 활용하면, 이러한 과정이 훨씬 간소화된다. ERC-4626 토큰은 자체적으로 사용자의 예치금과 이자를 계산하고 관리할 수 있는 기능을 가지고 있다.
또한, ERC-4626 표준은 다른 디파이 서비스와의 호환성을 높여준다. 예를 들어, 사용자가 "ABC" 플랫폼에서 얻은 보유량 토큰을 다른 디파이 서비스 "XYZ"에 예치하여 추가적인 수익을 얻거나, 담보로 사용할 수도 있다.

ERC-4626 표준은 디파이 생태계에서 자산 관리를 더 효율적이고 유연하게 만들어준다. 사용자는 여러 디파이 플랫폼 간에 자산을 쉽게 이동하고 관리할 수 있으며, 플랫폼들은 이 표준을 통해 서로 호환되는 서비스를 제공할 수 있다.
<br><br><br>

# 용어

- 토큰을 보관하는 컨트렉트를 Vault 컨트랙트라고 부르며
- 예치한 erc20 토큰을 underlying token이라고 부른다.
- 예치한 토큰에 대한 대가로 받는 토큰을 Vault Token 또는 Share Token 등으로 부른다.
  > 이 보유량 토큰을 통해 자신의 자산과 이자를 쉽게 확인할 수 있다.

<br><br><br>

# 주요 함수들

## 1. 예치 및 인출 관련 함수

### deposit

```solidity
function deposit(uint256 assets, address receiver)
external
returns (uint256 shares);
```

> 사용자가 자산을 컨트랙트에 예치하고, 그에 상응하는 쉐어 토큰을 반환하는 역할을 합니다. 이 함수는 사용자가 언더라잉(원래의 자산)을 컨트랙트에 예치하고, 대신에 쉐어 토큰을 받아올 수 있는 메커니즘을 제공합니다. 이것은 사용자가 언더라잉 자산을 쉐어 토큰으로 교환하는 과정을 나타냅니다.

### mint

```solidity
function mint(uint256 shares, address receiver)
external
returns (uint256 assets);
```

> 사용자가 쉐어 토큰을 컨트랙트에 제출하면, 컨트랙트는 그에 상응하는 언더라잉 토큰을 반환하고, 쉐어 토큰은 소각되는 역할을 합니다. 이 함수는 쉐어 토큰을 사용하여 언더라잉 토큰을 획득하는 과정을 나타냅니다.

### redeem

```solidity
function redeem(
    uint256 shares,
    address receiver,
    address owner
) external returns (uint256 assets);
```

> 디포짓 했던 자산을 다시 받아오는 행위, 사용자가 4626 컨트랙트에 보유한 쉐어 토큰을 반환하여 그에 상응하는 언더라잉 토큰을 회수하는 역할을 합니다. 이 함수는 사용자가 쉐어 토큰을 언더라잉 토큰으로 교환하고, 이를 컨트랙트로부터 받아오는 과정을 나타냅니다. 쉐어 토큰은 이 과정에서 소각됩니다. redeem 함수는 이 과정을 처리하며, 내부적으로는 withdraw 함수를 호출하여 언더라잉 자산을 사용자에게 반환합니다.

### withdraw

```solidity
function withdraw(
    uint256 assets,
    address receiver,
    address owner
) external returns (uint256 shares);
```

> 사용자가 컨트랙트에서 특정 양의 자산을 인출하고자 할 때 사용됩니다. 이 함수는 쉐어 토큰을 컨트랙트로 제출하여 그에 상응하는 언더라잉 토큰을 받아오는 역할을 합니다. 이 함수는 예치한 자산에서 발생한 이자를 포함한 총액을 인출할 때 사용될 수 있으며, 이자와 원금을 분리하지 않고 한꺼번에 인출할 수 있습니다. withdraw는 redeem 함수에 의해 내부적으로 호출될 수 있습니다.

<br><br>

## 2. 최대치 조회 함수

### 최대 예치 가능 금액 조회 - maxDeposit

```solidity
function maxDeposit(address receiver) external view returns (uint256);
```

> 특정 주소가 예치할 수 있는 최대 자산 양을 알려준다.

### 최대 쉐어 토큰 발행 가능 금액 조회 - maxMint

```solidity
function maxMint(address receiver) external view returns (uint256);
```

> 특정 주소가 발행할 수 있는 최대 쉐어 토큰 양을 알려준다.

### 최대 인출 가능 금액 조회 - maxWithdraw

```solidity
function maxWithdraw(address owner) external view returns (uint256);
```

> 특정 주소가 인출할 수 있는 최대 자산 양을 알려준다.

### 최대 이자 지급 금액 조회 - maxRedeem

```solidity
function maxRedeem(address owner) external view returns (uint256);
```

> 특정 주소가 이자 지급으로 인출할 수 있는 최대 금액을 알려준다.

<br><br>

## 3. 자산 및 쉐어 토큰 정보 조회

### 자산 조회 - asset

```solidity
function asset() external view returns (address);
```

> underliying 토큰의 주소 리턴

### 총 자산 조회 - totalAssets

```solidity
function totalAssets() external view returns (uint256);
```

> 컨트랙트에 예치된 총 자산의 양을 반환한다. 이 값은 컨트랙트의 안정성과 규모를 이해하는 데 중요하다.

<br><br>

## 4. 변환 함수

자산 및 쉐어 토큰 변환

```solidity
function convertToShares(uint256 assets) external view returns (uint256);
function convertToAssets(uint256 shares) external view returns (uint256);
```

> `convertToShares`는 자산을 쉐어 토큰으로, `convertToAssets`는 쉐어 토큰을 자산으로 변환한다. 변환 비율은 자산 가치에 따라 달라진다.

<br><br>

## 5. 미리 계산해 보는 조회 함수들

### 예치량 미리 계산 - previewDeposit

```solidity
function previewDeposit(uint256 assets) external view returns (uint256);
```

> 예치할 자산 양을 입력하면 예상되는 쉐어 토큰 양을 미리 계산하여 반환.

### 발행량 미리 계산 - previewMint

```solidity
function previewMint(uint256 shares) external view returns (uint256);
```

> 이 함수는 발행할 쉐어 토큰 양을 입력하면 예상되는 자산 양을 미리 계산하여 반환한다.

### 인출량 미리 계산 - previewWithdraw

```solidity
function previewWithdraw(uint256 assets) external view returns (uint256);
```

> 인출할 자산 양을 입력하면 예상되는 쉐어 토큰 양을 미리 계산하여 반환한다.

### 이자 지급량 미리 계산 - previewRedeem

```solidity
function previewRedeem(uint256 shares) external view returns (uint256);
```

이 함수는 이자 지급으로 인출할 쉐어 토큰 양을 입력하면 예상되는 자산 양을 미리 계산하여 반환한다.

<br><br><br>

# 실습

## 배포

```solidity
npx hardhat run scripts/deploy.ts
```

## 테스트

```solidity
npx hardhat run test
```
