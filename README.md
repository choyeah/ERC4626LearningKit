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

## 1. 예치 및 발행 관련 함수

### 예치 - deposit

```solidity
function deposit(uint256 assets, address receiver)
external
returns (uint256 shares);
```

> 사용자가 자산을 컨트랙트에 예치하고, 그에 상응하는 쉐어 토큰을 반환, 반환되는 쉐어 토큰의 양은 예치한 자산의 가치에 따라 결정.

### 쉐어토큰 발행 - mint

```solidity
function mint(uint256 shares, address receiver)
external
returns (uint256 assets);
```

> 사용자가 컨트랙트에 자산을 예치할 때 호출되며, 예치한 자산에 상응하는 양의 쉐어 토큰을 발행한다.
>
> 예를 들어 ETH나 다른 ERC-20 토큰을 ERC-4626 컨트랙트에 예치하면, 컨트랙트는 사용자가 예치한 자산의 가치와 동일한 양의 쉐어 토큰을 생성한다. 이렇게 생성된 토큰은 사용자에게 발급되며, 사용자는 이 토큰을 보유함으로써 자신이 컨트랙트에 예치한 자산의 가치를 대표하는 증거를 가지게 된다.

<br><br>

## 2. 상환/인출 관련 함수

### 상환 - redeem

```solidity
function redeem(
    uint256 shares,
    address receiver,
    address owner
) external returns (uint256 assets);
```

> 쉐어 토큰을 상환하여 해당 토큰에 상응하는 언더라잉 자산을 받는 과정을 의미. 여기서 중요한 점은 투자자가 보유한 쉐어 토큰의 가치와 동일한 언더라잉 자산을 회수. 주로 투자 수익과 원금을 함께 회수하는 상황에 사용.

### 인출 - withdraw

```solidity
function withdraw(
    uint256 assets,
    address receiver,
    address owner
) external returns (uint256 shares);
```

> 특정 양의 언더라잉 자산을 직접 인출하는 것을 의미. 투자자는 특정 금액의 자산을 인출하고 그에 상응하는 쉐어 토큰이 차감. 주로 예치된 자산에서 발생한 이자를 포함한 총액을 인출하고자 할 때 사용.

<br><br>

## 3. 최대치 조회 함수

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

## 4. 자산 및 쉐어 토큰 정보 조회

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

## 5. 변환 함수

자산 및 쉐어 토큰 변환

```solidity
function convertToShares(uint256 assets) external view returns (uint256);
function convertToAssets(uint256 shares) external view returns (uint256);
```

> `convertToShares`는 자산을 쉐어 토큰으로, `convertToAssets`는 쉐어 토큰을 자산으로 변환한다. 변환 비율은 자산 가치에 따라 달라진다.

<br><br>

## 6. 미리 계산해 보는 조회 함수들

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
