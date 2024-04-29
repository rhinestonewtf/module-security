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

| Category    | Variable                                                                                                                                     | Data type        |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Module type | Validator Policy Signer Executor Hook Fallback Module types are not mutually exclusive What are the subclassifications for each module type? | Uint (module id) |
| Executor    | Is not upgradable                                                                                                                            | Boolean          |
|             | Is not owneable                                                                                                                              | Boolean          |
|             | Is pausable                                                                                                                                  | Boolean          |
|             | Does not execute delegatecall on the account                                                                                                 | Boolean          |
|             | Is triggered by the account                                                                                                                  | Boolean          |
|             | Has external dependencies Classifications of external dependencies? Oracles / DEXs / etc                                                     | Boolean          |
|             | Fee mechanism                                                                                                                                | Boolean          |
|             | Subclassifications                                                                                                                           |                  |
|             | [what else]                                                                                                                                  |                  |
| Validator   | Is not upgradable                                                                                                                            | Boolean          |
|             | Is not owneable                                                                                                                              | Boolean          |
|             | Is storage requirement 4337 compliant                                                                                                        | Boolean          |
|             | Can authorize anything                                                                                                                       | Boolean          |
|             | Has scoped authorization controls List of common access controls                                                                             |                  |
|             | Is recovery module                                                                                                                           | Boolean          |
|             | Subclassifications                                                                                                                           |                  |
|             | [what else]                                                                                                                                  |                  |
| Hook        | Is not upgradable                                                                                                                            | Boolean          |
|             | Is not owneable                                                                                                                              | Boolean          |
|             | Has external dependencies Classifications of external dependencies? Oracles / DEXs / etc                                                     | Boolean          |
|             | Subclassifications                                                                                                                           |                  |
|             | [what else]                                                                                                                                  |                  |
| Fallback    | Is not upgradable                                                                                                                            | Boolean          |
|             | Is not owneable                                                                                                                              | Boolean          |
|             | Implements ERC2771 authorization control                                                                                                     | Boolean          |
|             | Static call vs normal call                                                                                                                   | Boolean          |
|             | Has external dependencies Classifications of external dependencies? Oracles / DEXs / etc                                                     | Boolean          |
|             | Subclassifications                                                                                                                           |                  |
|             | [what else]                                                                                                                                  |                  |

## Resources

todo
