# Smart Account Module Security

Opinionated security and code quality standard for [smart account modules](https://blog.rhinestone.wtf/wtf-is-modular-account-abstraction-7f1621604812).

_Inspired by [Solcurity](https://github.com/transmissions11/solcurity)_

## General Review Approach

todo

## Checklist

### General

- Modules should not be upgradable
- Modules should not be ownable unless necessary
- Modules should not delegatecall to other contracts especially when the target is not predefined in bytecode

### Configuration

- `onInstall` and `onUninstall` could cause re-entrancy on the account
- `onInstall` should not be able to be re-initialized without clearning storage
- Needs to clear all account-related storage `onUninstall`
- `onUninstall` could DOS the account by using all gas or reverting and thus not being able to be uninstalled
- Other configuration functions should not be able to be used when a module is not initialised yet
- Configuration function access must be adequately scoped, ideally just using `msg.sender`

### Module Logic

- Validators
  - Validators should comply with `ERC-4337` rules
  - Validators should not make state changes during validation unless using `msg.sender` (ie not when using `userOp.sender`)
  - ERC-1271 must be able to be used with staticcall
- Executors
  - Executors should explicitly use specific executions, rather than just forwarding data whenever possible
  - Executors should not use delegatecall on the accoun and when they do, targets should be predefined or whitelisted
- Hooks
  - Hooks could be used to DOS the account
  - Hooks should be compatible with the hook multiplexing pattern
- Fallbacks

### Compliance to ERC-7579

- Needs to implement all mandatory `IModule` functions: `onInstall`, `onUninstall`, `isModuleType`
- A module can be multiple types at once
- If `moduleType` includes validator, needs to implement `validateUserOp`, `isValidSignatureWithSender`
- If `moduleType` includes executor, no further mandatory functions
- If `moduleType` includes hook, needs to implement `preCheck`, `postCheck`
- If `moduleType` includes fallback, no further mandatory functions

## Rhinestone Attestation Schema

| Category    | Variable                                                       | Data type |
| ----------- | -------------------------------------------------------------- | --------- |
| Module type | According to ERC-7579 and extensions                           | Uint[]    |
| All         | Is not upgradable                                              | Boolean   |
|             | Module owner cannot rug account                                | Boolean   |
|             | Is pausable                                                    | Boolean   |
|             | External dependencies list                                     | Uint[]    |
|             | Uses Rhinestone License Manager                                | Boolean   |
| Validator   | Is ERC-7562 compliant                                          | Boolean   |
|             | Subclassifications                                             | Uint[]    |
| Executor    | Handles user assets (tokens or native assets)                  | Boolean   |
|             | Does not execute delegatecall on the account                   | Boolean   |
|             | Subclassifications                                             | Uint[]    |
| Fallback    | Implements ERC-2771 to for access control and storage accesses | Boolean   |
|             | Is called by static call                                       | Boolean   |
|             | Implements its own access control                              | Boolean   |
|             | Subclassifications                                             | Uint[]    |
| Hook        | Subclassifications                                             | Uint[]    |

**Note: the index in the bullet list (starting from 0) corresponds to the enum value of the classification**

Validator subclassifications:

- Unscoped validator
- Scoped validator
- Recovery
- Multiplexer

Executor subclassifications:

- Triggered by the account
- Triggered by trusted relayer
- Deterministic executions

Fallback subclassifications:

- Compatibility fallback
- Callbacks

Hook subclassifications:

- Allow default hooks
- Deny default hooks
- Access control
- Module control
- User control

External dependencies classifications:

- Oracles
- Bridges
- DEXs
- Vaults
- Registry
- Lending
- Liquidity Provision
- Governance
- Privacy
- ZK Provers

## Resources

todo
