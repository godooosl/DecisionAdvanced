# ChoiceForge

**Privacy-preserving decision-making platform on Zama FHEVM**

ChoiceForge enables private decision processes where choices, preferences, and decision criteria remain encrypted during evaluation. Built on Zama's Fully Homomorphic Encryption Virtual Machine, the platform processes decision logic over encrypted inputs, revealing only final outcomes while keeping individual choices and reasoning private.

---

## Purpose

ChoiceForge addresses the challenge of collaborative decision-making where participants need to express preferences privately while ensuring fair, verifiable outcomes. Traditional decision systems expose individual preferences, leading to social pressure and strategic manipulation. ChoiceForge uses Zama FHEVM to enable homomorphic decision aggregation—computing outcomes over encrypted preferences without revealing individual choices.

**Core Objective**: Enable transparent, fair decisions while maintaining absolute privacy of individual preferences and reasoning.

---

## Decision Process

### Decision Initialization

**Setup:**
1. Decision creator defines:
   - Decision question/context
   - Available options
   - Decision criteria (optional)
   - Participant list
   - Decision deadline
2. Smart contract deployed
3. FHE keys generated and distributed
4. Decision opened for participation

### Preference Submission

**How Participants Submit:**
1. Participant reviews decision context
2. Evaluates options (privately)
3. Encrypts preferences using FHE public key
4. Submits encrypted preferences to contract
5. Receives confirmation receipt

**Privacy:**
- Preferences encrypted before submission
- Stored as encrypted ciphertexts
- Not visible to other participants
- Not visible to decision creator
- Not visible to validators

### Decision Aggregation

**Homomorphic Processing:**
1. Smart contract receives all encrypted preferences
2. Aggregates preferences using FHE operations:
   ```solidity
   euint64 totalScore = TFHE.add(
       preference1, preference2, preference3, ...
   );
   ```
3. Computes encrypted outcome scores
4. Determines winning option (encrypted comparison)
5. Generates cryptographic proof

**Operations:**
- Weighted preference aggregation
- Multi-criteria evaluation
- Ranking computation
- Outcome determination

### Result Revelation

**Outcome Publication:**
1. Decision deadline passes
2. Threshold key holders decrypt results
3. Final outcome published on-chain
4. Cryptographic proof of correctness available
5. Individual preferences remain encrypted (permanent)

---

## Decision Types

### Simple Selection

**Binary Choice:**
- Two options (Option A vs Option B)
- Participants encrypt preference (0 or 1)
- Homomorphic aggregation
- Winner determined by majority

**Example**: Choose between two proposals

### Multi-Option Ranking

**Preference Ranking:**
- Multiple options ranked
- Encrypted ranking vectors
- Borda count or similar (homomorphic)
- Winner determined by aggregate ranking

**Example**: Rank 5 product features by importance

### Weighted Scoring

**Criteria-Based Evaluation:**
- Multiple criteria weighted
- Each option scored per criterion
- Encrypted scores aggregated
- Weighted total computed homomorphically

**Example**: Evaluate vendors on price, quality, service

### Consensus Building

**Iterative Refinement:**
- Multiple rounds of preference submission
- Preferences aggregated each round
- Feedback provided (aggregate statistics)
- Final decision after convergence

**Example**: Iterative refinement of proposal

---

## Technical Foundation

### Homomorphic Decision Logic

**Preference Aggregation:**
```solidity
euint64 aggregatePreferences(
    euint64[] encryptedPreferences,
    euint64[] weights
) internal view returns (euint64) {
    euint64 weightedSum = 0;
    for (uint i = 0; i < encryptedPreferences.length; i++) {
        euint64 weighted = TFHE.mul(
            encryptedPreferences[i],
            weights[i]
        );
        weightedSum = TFHE.add(weightedSum, weighted);
    }
    return weightedSum;
}
```

**Winner Selection:**
```solidity
function selectWinner(
    euint64[] encryptedScores
) internal view returns (uint256 winner) {
    euint64 maxScore = encryptedScores[0];
    uint256 winnerIndex = 0;
    
    for (uint i = 1; i < encryptedScores.length; i++) {
        if (TFHE.gt(encryptedScores[i], maxScore)) {
            maxScore = encryptedScores[i];
            winnerIndex = i;
        }
    }
    
    return winnerIndex;
}
```

### Smart Contract Structure

```solidity
contract ChoiceForge {
    struct Decision {
        string question;
        string[] options;
        euint64[] encryptedScores;
        euint64[] encryptedPreferences;
        uint256 deadline;
        bool resolved;
        address creator;
    }
    
    function createDecision(
        string memory question,
        string[] memory options,
        uint256 deadline
    ) external returns (uint256 decisionId);
    
    function submitPreference(
        uint256 decisionId,
        bytes calldata encryptedPreference
    ) external;
    
    function aggregateDecision(uint256 decisionId) external;
    
    function revealOutcome(
        uint256 decisionId,
        bytes calldata decryptionKey
    ) external;
    
    function getOutcome(uint256 decisionId)
        external
        view
        returns (uint256 winningOption);
}
```

---

## Privacy & Transparency

### Privacy Guarantees

**Individual Privacy:**
- Preferences encrypted end-to-end
- Never decrypted during processing
- Not visible to other participants
- Permanent privacy protection

**Decision Transparency:**
- Decision context public
- Final outcome public and verifiable
- Cryptographic proofs available
- Immutable decision records

### Transparency Properties

**Verifiable Fairness:**
- Decision process cryptographically provable
- Outcome correctness verifiable
- No manipulation possible
- Transparent aggregation logic

**Auditability:**
- All operations recorded on-chain
- Decision history immutable
- Verification proofs available
- Complete audit trail

---

## Use Cases

### Organizational Decision-Making

**Scenario**: Team selects technology stack  
**Challenge**: Members hesitant to express honest preferences  
**Solution**: Encrypted preference submission ensures candid input  
**Benefit**: Better decisions through honest participation

### Community Governance

**Scenario**: DAO votes on treasury allocation  
**Challenge**: Members influenced by visible preferences  
**Solution**: Private voting with verifiable outcomes  
**Benefit**: True consensus without social pressure

### Product Development

**Scenario**: Prioritize product features  
**Challenge**: Stakeholder influence on preferences  
**Solution**: Encrypted preference aggregation  
**Benefit**: Objective prioritization

### Hiring Decisions

**Scenario**: Selection committee evaluates candidates  
**Challenge**: Bias and social influence  
**Solution**: Private evaluation with fair aggregation  
**Benefit**: Merit-based selection

---

## Decision Mechanisms

### Voting Mechanisms

**Majority Rule:**
- Simple majority wins
- Encrypted vote aggregation
- Binary choice only
- Fast resolution

**Plurality:**
- Most votes wins
- Multiple options
- Encrypted aggregation
- Simple to understand

### Consensus Mechanisms

**Unanimity:**
- All must agree
- Encrypted preference checking
- Requires consensus
- High threshold

**Super-Majority:**
- Threshold-based (e.g., 66%)
- Encrypted threshold checking
- Flexible threshold
- Balanced approach

### Scoring Mechanisms

**Weighted Average:**
- Scores aggregated with weights
- Homomorphic weighted sum
- Flexible weighting
- Nuanced evaluation

**Borda Count:**
- Ranking-based scoring
- Encrypted ranking aggregation
- Fair for multiple options
- Preference expression

---

## Security Model

### Confidentiality

**Preference Privacy:**
- Encrypted before submission
- Processed over encrypted data
- Never decrypted individually
- Permanent encryption (optional)

**Reasoning Privacy:**
- Evaluation criteria encrypted (optional)
- Private decision logic
- No exposure of reasoning
- Complete privacy

### Integrity

**Decision Correctness:**
- Cryptographic proofs of aggregation
- Verifiable outcome computation
- Immutable preference records
- Tamper-proof process

**Fairness Guarantees:**
- Equal weight for all (or configurable)
- No manipulation possible
- Transparent aggregation
- Verifiable fairness

---

## Performance

### Gas Costs

| Operation | Gas | Notes |
|-----------|-----|-------|
| Create decision | ~300,000 | Setup cost |
| Submit preference | ~120,000 | Per participant |
| Aggregate (10 participants) | ~500,000 | Aggregation |
| Aggregate (100 participants) | ~2,000,000 | Large groups |
| Reveal outcome | ~150,000 | Result decryption |

### Optimization

**Batch Operations:**
- Aggregate multiple preferences in one transaction
- Reduce per-participant costs
- Efficient encrypted aggregation

**Scalability:**
- Layer 2 for large decisions
- Off-chain aggregation with on-chain verification
- Parallel processing for multiple decisions

---

## Getting Started

### Setup

```bash
git clone https://github.com/yourusername/choiceforge.git
cd choiceforge
npm install
```

### Configuration

```bash
cp .env.example .env
# Edit .env with your settings
```

### Deployment

```bash
npx hardhat compile
npx hardhat run scripts/deploy.js --network sepolia
```

### Usage

1. **Create Decision**: Define question and options
2. **Invite Participants**: Share decision link
3. **Collect Preferences**: Participants submit encrypted preferences
4. **Aggregate**: Process preferences homomorphically
5. **Reveal**: Publish outcome with proofs
6. **Verify**: Anyone can verify decision correctness

---

## API Reference

### Smart Contract Methods

```solidity
// Create decision
function createDecision(
    string memory question,
    string[] memory options,
    uint256 deadline
) external returns (uint256);

// Submit encrypted preference
function submitPreference(
    uint256 decisionId,
    bytes calldata encryptedPreference
) external;

// Aggregate preferences
function aggregateDecision(uint256 decisionId) external;

// Reveal outcome
function revealOutcome(
    uint256 decisionId,
    bytes calldata decryptionKey
) external;
```

### JavaScript SDK

```typescript
import { ChoiceForge } from '@choiceforge/sdk';

const client = new ChoiceForge({
  provider: window.ethereum,
  contractAddress: '0x...',
});

// Create decision
const decisionId = await client.createDecision({
  question: 'Which feature should we build next?',
  options: ['Feature A', 'Feature B', 'Feature C'],
  deadline: Date.now() + 7 * 24 * 60 * 60 * 1000,
});

// Submit preference
const encrypted = await client.encryptPreference([2, 1, 3]); // Ranking
await client.submitPreference(decisionId, encrypted);

// Get outcome
const outcome = await client.getOutcome(decisionId);
```

---

## Roadmap

### Q1 2025
- ✅ Core decision functionality
- ✅ Encrypted preference submission
- ✅ Homomorphic aggregation
- 🔄 Performance optimization

### Q2 2025
- 📋 Advanced decision mechanisms
- 📋 Multi-criteria evaluation
- 📋 Mobile application
- 📋 Analytics dashboard

### Q3 2025
- 📋 Consensus mechanisms
- 📋 Iterative refinement
- 📋 Enterprise features
- 📋 API improvements

### Q4 2025
- 📋 Zero-knowledge enhancements
- 📋 Decentralized governance
- 📋 Cross-chain support
- 📋 Post-quantum FHE support

---

## Contributing

We welcome contributions! Priority areas:

- FHE optimization for decision aggregation
- Gas cost reduction
- Security audits
- Additional decision mechanisms
- UI/UX improvements
- Documentation

**How to contribute:**
1. Fork the repository
2. Create a feature branch
3. Implement your changes
4. Add tests
5. Submit a pull request

---

## FAQ

**Q: How private are my preferences if they're stored on-chain?**  
A: Preferences are encrypted with FHE before submission. Only encrypted ciphertexts are stored, and aggregation occurs over encrypted data. Your preferences are never revealed.

**Q: Can decision outcomes be manipulated?**  
A: No. Outcomes are computed using homomorphic operations that are cryptographically provable. The aggregation process is verifiable, preventing manipulation.

**Q: What if I change my mind?**  
A: Depending on decision settings, you may be able to update your preference before the deadline. Once the deadline passes, preferences are locked.

**Q: How do I verify decision fairness?**  
A: ChoiceForge generates cryptographic proofs that anyone can verify, ensuring the outcome correctly reflects encrypted preferences without revealing individual choices.

**Q: What happens if not everyone participates?**  
A: Decisions can proceed with partial participation, depending on configuration. Minimum participation thresholds can be set.

---

## License

MIT License - see [LICENSE](LICENSE) file for details.

---

## Acknowledgments

ChoiceForge is built on:

- **[Zama FHEVM](https://www.zama.ai/fhevm)**: Fully Homomorphic Encryption Virtual Machine
- **[Zama](https://www.zama.ai/)**: FHE research and development
- **Ethereum Foundation**: Blockchain infrastructure

Built with support from the privacy-preserving decision-making community.

---

## Links

- **Repository**: [GitHub](https://github.com/yourusername/choiceforge)
- **Documentation**: [Full Docs](https://docs.choiceforge.io)
- **Discord**: [Community](https://discord.gg/choiceforge)
- **Twitter**: [@ChoiceForge](https://twitter.com/choiceforge)

---

**ChoiceForge** - Make decisions privately, verify outcomes publicly.

_Powered by Zama FHEVM_

