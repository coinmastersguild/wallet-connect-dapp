# WalletConnect to Reown Migration Plan

## Project: wallet-connect-dapp
## Timeline: Immediate - Must complete before February 17, 2025
## Risk Level: 🔴 HIGH - Deprecated packages will lose support

---

## 📋 Migration Checklist

### Phase 1: Preparation (Today)
- [x] Audit current packages and dependencies
- [x] Create migration plan
- [ ] Backup current working state (git branch)
- [ ] Document current functionality for testing

### Phase 2: Package Updates
- [ ] Remove deprecated `@walletconnect/web3wallet`
- [ ] Install `@reown/walletkit`
- [ ] Update core WalletConnect packages
- [ ] Verify package installation

### Phase 3: Code Migration
- [ ] Update WalletConnectUtil.ts
- [ ] Update type definitions
- [ ] Update initialization code
- [ ] Update event handlers
- [ ] Fix TypeScript errors

### Phase 4: Testing
- [ ] Test KeepKey device connection
- [ ] Test WalletConnect pairing
- [ ] Test signing operations
- [ ] Test transaction broadcasting
- [ ] Test session management

### Phase 5: Documentation
- [ ] Update CLAUDE.md
- [ ] Update README.md
- [ ] Document breaking changes
- [ ] Update environment variables (if needed)

---

## 🎯 Migration Strategy

### Step 1: Create Safe Working Branch
```bash
git checkout -b feature/reown-migration
git add .
git commit -m "chore: checkpoint before WalletConnect to Reown migration"
```

### Step 2: Update Dependencies

#### Remove and Add Packages
```bash
# Remove deprecated package
npm uninstall @walletconnect/web3wallet

# Add Reown WalletKit
npm install @reown/walletkit@^1.2.10

# Update existing WalletConnect packages
npm install @walletconnect/core@^2.21.8 @walletconnect/utils@^2.21.0 @walletconnect/types@^2.21.0
```

### Step 3: Code Changes Required

#### Files to Modify:
1. **`src/utils/WalletConnectUtil.ts`** (Primary)
2. **`src/pages/_app.tsx`** 
3. **`src/hooks/useWalletConnectEventsManager.ts`**
4. **`src/hooks/useKeepKey.ts`**
5. Any other files importing from WalletConnectUtil

#### Key Changes:

**1. WalletConnectUtil.ts**
```typescript
// BEFORE
import { Web3Wallet, IWeb3Wallet } from '@walletconnect/web3wallet'
export let web3wallet: IWeb3Wallet

// AFTER
import { WalletKit, IWalletKit } from '@reown/walletkit'
export let web3wallet: IWalletKit  // Keep variable name for compatibility

// BEFORE (in createWeb3Wallet function)
web3wallet = await Web3Wallet.init({...})

// AFTER
web3wallet = await WalletKit.init({...})
```

**2. Type Updates**
- Replace all `IWeb3Wallet` references with `IWalletKit`
- Update any Web3Wallet-specific types to WalletKit equivalents

### Step 4: Verification Points

#### Critical Functionality Tests:
1. **KeepKey Connection**
   - Device connects on port 1646
   - API key stored/retrieved from localStorage
   - Address and balance fetching works

2. **WalletConnect Core**
   - QR code scanning works
   - URI connection works
   - Session proposals display
   - Session management (disconnect/update)

3. **Signing Operations**
   - personal_sign
   - eth_sign
   - eth_signTypedData (v3/v4)
   - eth_sendTransaction
   - eth_signTransaction

4. **Event Handling**
   - Session events fire correctly
   - Chain switching works
   - Account changes propagate

---

## 🚨 Potential Issues & Solutions

### Issue 1: Breaking API Changes
**Risk**: WalletKit may have different method signatures
**Solution**: Check Reown docs for API differences, update calls accordingly

### Issue 2: Event Handler Changes
**Risk**: Event names or payloads might differ
**Solution**: Test all event handlers thoroughly, update as needed

### Issue 3: TypeScript Errors
**Risk**: Type mismatches after migration
**Solution**: Use TypeScript compiler to identify issues, fix incrementally

### Issue 4: KeepKey Integration
**Risk**: KeepKey SDK might have compatibility issues
**Solution**: Test thoroughly, may need to update KeepKey packages

---

## 📊 Success Metrics

- [ ] Zero TypeScript compilation errors
- [ ] All existing tests pass (if any)
- [ ] KeepKey device connects successfully
- [ ] Can pair with WalletConnect dApps
- [ ] Can sign messages and transactions
- [ ] No console errors in browser
- [ ] Session persistence works

---

## 🔄 Rollback Plan

If migration fails:
1. `git stash` or `git reset --hard`
2. `git checkout main` (or previous branch)
3. `npm install` to restore packages
4. Document specific failure points for retry

---

## 📝 Post-Migration Tasks

1. **Optional but Recommended**:
   - Update React to v18
   - Update Next.js to v14
   - Update other outdated dependencies
   - Add automated tests

2. **Documentation**:
   - Update README with new requirements
   - Document any breaking changes
   - Update environment variable docs

3. **Cleanup**:
   - Remove any deprecated code
   - Remove unused dependencies
   - Run `npm audit fix`

---

## 🎬 Let's Begin!

Ready to start? The migration should take approximately 1-2 hours.

### First Action:
```bash
# Create a safe working branch
git checkout -b feature/reown-migration
```

Then we'll proceed with package updates and code changes systematically.