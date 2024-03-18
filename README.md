# Smart Account Module Security

Opinionated security and code quality standard for [smart account modules](https://blog.rhinestone.wtf/wtf-is-modular-account-abstraction-7f1621604812).

_Inspired by [Solcurity](https://github.com/transmissions11/solcurity)_

## General Review Approach

todo

## Checklist

### Configuration

- `onInstall` and `onUninstall` could cause re-entrancy on the account
- `onInstall` should not be able to be re-initialized without clearning storage
- Needs to clear all account-related storage `onUninstall`
- `onUninstall` could DOS the account by using all gas or reverting and thus not being able to be uninstalled

### Module Logic

- Validators should comply with `ERC-4337` rules

### Compliance to ERC-7579

- Needs to implement all mandatory `IModule` functions: `onInstall`, `onUninstall`, `isModuleType`
- A module can be multiple functions at once
- If `moduleType` includes validator, needs to implement `validateUserOp`, `isValidSignatureWithSender`
- If `moduleType` includes executor, no further mandatory functions
- If `moduleType` includes hook, needs to implement `preCheck`, `postCheck`
- If `moduleType` includes fallback, no further mandatory functions

### Inheritance

todo

## Resources

todo
