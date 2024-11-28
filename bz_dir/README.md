# Cross-Chain Gasless Token Bridge

A secure and efficient smart contract solution for gasless token transfers between blockchain networks, implemented in Clarity for the Stacks blockchain.

## Overview

The Cross-Chain Gasless Token Bridge enables users to transfer tokens between different blockchain networks without requiring them to pay gas fees in the destination chain's native currency. This is achieved through a meta-transaction pattern where a relayer executes the transfer on behalf of users.

## Features

- **Gasless Transfers**: Users can initiate cross-chain transfers without holding the destination chain's native currency
- **Meta-Transaction Support**: Implements EIP-712 style structured data signing
- **Replay Protection**: Secure nonce-based system to prevent transaction replay attacks
- **Token Registry**: Flexible system for registering and managing different token types
- **Balance Tracking**: Accurate tracking of token balances for all users
- **Owner Controls**: Protected administrative functions for token registration and fee management

## Contract Architecture

### Core Components

1. **Storage Maps**
   - `UsedNonces`: Tracks used nonces to prevent replay attacks
   - `BridgeTokens`: Manages token balances for each user
   - `TokenInfo`: Stores token metadata (name, symbol, decimals)

2. **Constants**
   - `CONTRACT-OWNER`: Address of the contract administrator
   - Error codes for various failure conditions

3. **Key Functions**
   - `register-token`: Register new tokens for bridging
   - `execute-gasless-transfer`: Process cross-chain transfers
   - `verify-signature`: Validate transaction signatures
   - `pay-relay-fee`: Handle relay fee payments
   - `get-token-balance`: Query token balances

## Function Details

### Public Functions

#### `register-token`
```clarity
(define-public (register-token 
  (token-id uint)
  (name (string-ascii 32))
  (symbol (string-ascii 10))
  (decimals uint)
))
```
Registers a new token for bridging. Only callable by contract owner.

#### `execute-gasless-transfer`
```clarity
(define-public (execute-gasless-transfer
  (token-id uint)
  (amount uint)
  (recipient principal)
  (nonce uint)
  (signature (buff 65))
))
```
Executes a gasless token transfer with signature verification.

#### `update-relay-fee`
```clarity
(define-public (update-relay-fee (new-fee uint))
```
Updates the relay fee. Only callable by contract owner.

### Read-Only Functions

#### `get-token-balance`
```clarity
(define-read-only (get-token-balance (token-id uint) (owner principal))
```
Returns the token balance for a specific owner and token ID.

## Security Considerations

1. **Signature Verification**
   - Implements secure message composition and verification
   - Uses nonce-based replay protection
   - NOTE: Current signature verification is a placeholder and needs proper secp256k1 implementation

2. **Access Control**
   - Administrative functions restricted to contract owner
   - Balance checks prevent unauthorized token transfers

3. **Error Handling**
   - Comprehensive error codes for different failure scenarios
   - Proper validation of all input parameters

## Usage Guide

### Registering a Token

1. Only the contract owner can register new tokens
2. Provide token details: ID, name, symbol, and decimals
3. Token registration is permanent and cannot be modified

### Executing a Transfer

1. User signs a transfer message containing:
   - Token ID
   - Amount
   - Recipient address
   - Nonce
2. Relayer submits the signed transaction to the contract
3. Contract verifies signature and processes transfer
4. Relay fee is collected from the sender

### Querying Balances

Use `get-token-balance` to check token balances at any time:
```clarity
(contract-call? .bridge get-token-balance token-id owner-address)
```

## Development Setup

1. Install Clarinet
2. Clone the repository
3. Run tests:
   ```bash
   clarinet test
   ```

## Implementation Notes

- Signature verification is currently a placeholder and needs to be implemented with proper secp256k1 verification
- Relay fee mechanism can be customized based on specific requirements
- Token registration could be extended to include additional metadata

## Future Improvements

1. **Enhanced Security**
   - Implement complete secp256k1 signature verification
   - Add pause mechanism for emergencies
   - Implement token allowance system

2. **Feature Additions**
   - Multi-signature support for administrative actions
   - Batch transfer functionality
   - Token locking/unlocking mechanisms

3. **Optimizations**
   - Gas optimization for complex operations
   - Enhanced error reporting
   - Better event logging
