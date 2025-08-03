# 🏛️ OpenFundDAO - Financiamento Descentralizado Open Source

## 🎯 Objetivo de Aprendizado
DAO (Organização Autônoma Descentralizada) desenvolvida para estudar **governança descentralizada** e **financiamento colaborativo**. Implementa sistema de votação e funding para projetos open-source, aplicando conceitos de **blockchain governance**, **tokenomics** e **community management**.

## 🛠️ Tecnologias Utilizadas
- **Blockchain:** Ethereum, Polygon
- **Smart Contracts:** Solidity
- **Governança:** Snapshot (off-chain voting)
- **Frontend:** React, Web3.js
- **Token Standard:** ERC-20 (governance token)
- **Communication:** Discord, Twitter
- **Development:** Hardhat, OpenZeppelin

## 🚀 Demonstração
```solidity
// Contrato de Governança Simplificado
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract OpenFundGovernanceToken is ERC20, Ownable {
    mapping(address => uint256) public contributions;
    mapping(uint256 => Proposal) public proposals;
    uint256 public proposalCount;
    
    struct Proposal {
        string title;
        string description;
        uint256 fundingAmount;
        address payable recipient;
        uint256 votesFor;
        uint256 votesAgainst;
        bool executed;
        uint256 deadline;
    }
    
    function createProposal(
        string memory _title,
        string memory _description,
        uint256 _fundingAmount,
        address payable _recipient
    ) external {
        require(balanceOf(msg.sender) >= 100 * 10**18, "Insufficient tokens");
        
        proposals[proposalCount] = Proposal({
            title: _title,
            description: _description,
            fundingAmount: _fundingAmount,
            recipient: _recipient,
            votesFor: 0,
            votesAgainst: 0,
            executed: false,
            deadline: block.timestamp + 7 days
        });
        
        proposalCount++;
    }
}
```

## 📁 Estrutura da DAO
```
OpenFundDAO/
├── governance/                   # Governança e votação
│   ├── proposals/               # Propostas de financiamento
│   ├── voting-mechanisms/       # Mecanismos de votação
│   └── token-distribution/      # Distribuição de tokens
├── smart-contracts/             # Contratos inteligentes
│   ├── GovernanceToken.sol     # Token de governança
│   ├── Treasury.sol            # Tesouraria da DAO
│   └── ProposalManager.sol     # Gerenciador de propostas
├── frontend/                    # Interface web
│   ├── components/             # Componentes React
│   ├── pages/                  # Páginas da aplicação
│   └── web3/                   # Integração Web3
├── community/                   # Gestão da comunidade
│   ├── discord-bot/            # Bot do Discord
│   ├── guidelines/             # Diretrizes da comunidade
│   └── onboarding/             # Processo de onboarding
└── docs/                       # Documentação
```

## 💡 Principais Aprendizados

### 🏛️ Decentralized Governance
- **Voting mechanisms:** Mecanismos de votação descentralizada
- **Proposal lifecycle:** Ciclo de vida de propostas
- **Quorum requirements:** Requisitos de quórum
- **Token-weighted voting:** Votação ponderada por tokens
- **Delegation systems:** Sistemas de delegação

### 💰 Tokenomics Design
- **Token distribution:** Distribuição inicial de tokens
- **Incentive alignment:** Alinhamento de incentivos
- **Vesting schedules:** Cronogramas de liberação
- **Reward mechanisms:** Mecanismos de recompensa
- **Economic sustainability:** Sustentabilidade econômica

### 🤝 Community Management
- **Stakeholder engagement:** Engajamento de stakeholders
- **Conflict resolution:** Resolução de conflitos
- **Communication channels:** Canais de comunicação
- **Onboarding processes:** Processos de integração
- **Culture building:** Construção de cultura

## 🧠 Conceitos Técnicos Estudados

### 1. **Governance Token Implementation**
```solidity
// OpenFundToken.sol
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Votes.sol";
import "@openzeppelin/contracts/access/AccessControl.sol";

contract OpenFundToken is ERC20Votes, AccessControl {
    bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
    bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");
    
    uint256 public constant MAX_SUPPLY = 1000000 * 10**18; // 1M tokens
    
    constructor() ERC20("OpenFund DAO Token", "OFD") ERC20Permit("OpenFund DAO Token") {
        _grantRole(DEFAULT_ADMIN_ROLE, msg.sender);
        _grantRole(MINTER_ROLE, msg.sender);
        
        // Initial distribution
        _mint(msg.sender, 100000 * 10**18); // 10% for initial team
    }
    
    function mint(address to, uint256 amount) external onlyRole(MINTER_ROLE) {
        require(totalSupply() + amount <= MAX_SUPPLY, "Exceeds max supply");
        _mint(to, amount);
    }
    
    function burn(uint256 amount) external onlyRole(BURNER_ROLE) {
        _burn(msg.sender, amount);
    }
    
    // Reward contributors
    function rewardContributor(address contributor, uint256 amount) external onlyRole(MINTER_ROLE) {
        require(totalSupply() + amount <= MAX_SUPPLY, "Exceeds max supply");
        _mint(contributor, amount);
        
        emit ContributorRewarded(contributor, amount);
    }
    
    event ContributorRewarded(address indexed contributor, uint256 amount);
}
```

### 2. **Proposal Management System**
```javascript
// Frontend - Proposal Creation
import { ethers } from 'ethers';
import { useState } from 'react';

const ProposalCreation = () => {
  const [proposal, setProposal] = useState({
    title: '',
    description: '',
    fundingAmount: '',
    recipientAddress: '',
    milestones: []
  });

  const createProposal = async () => {
    try {
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      const signer = provider.getSigner();
      const contract = new ethers.Contract(contractAddress, abi, signer);
      
      const tx = await contract.createProposal(
        proposal.title,
        proposal.description,
        ethers.utils.parseEther(proposal.fundingAmount),
        proposal.recipientAddress
      );
      
      await tx.wait();
      
      // Notify community
      await notifyDiscord({
        title: `Nova Proposta: ${proposal.title}`,
        description: proposal.description,
        amount: proposal.fundingAmount,
        proposer: await signer.getAddress()
      });
      
    } catch (error) {
      console.error('Error creating proposal:', error);
    }
  };

  return (
    <div className="proposal-form">
      <h2>Criar Nova Proposta</h2>
      <form onSubmit={createProposal}>
        <input
          type="text"
          placeholder="Título do Projeto"
          value={proposal.title}
          onChange={(e) => setProposal({...proposal, title: e.target.value})}
        />
        <textarea
          placeholder="Descrição detalhada"
          value={proposal.description}
          onChange={(e) => setProposal({...proposal, description: e.target.value})}
        />
        <input
          type="number"
          placeholder="Valor solicitado (ETH)"
          value={proposal.fundingAmount}
          onChange={(e) => setProposal({...proposal, fundingAmount: e.target.value})}
        />
        <button type="submit">Criar Proposta</button>
      </form>
    </div>
  );
};
```

### 3. **Community Engagement Bot**
```javascript
// Discord Bot for DAO Management
const Discord = require('discord.js');
const { ethers } = require('ethers');

class OpenFundBot {
  constructor() {
    this.client = new Discord.Client({ intents: ['GUILDS', 'GUILD_MESSAGES'] });
    this.setupCommands();
  }

  setupCommands() {
    this.client.on('messageCreate', async (message) => {
      if (message.content.startsWith('!proposal')) {
        await this.handleProposalCommand(message);
      }
      
      if (message.content.startsWith('!vote')) {
        await this.handleVoteCommand(message);
      }
      
      if (message.content.startsWith('!balance')) {
        await this.handleBalanceCommand(message);
      }
    });
  }

  async handleProposalCommand(message) {
    const proposals = await this.getActiveProposals();
    
    const embed = new Discord.MessageEmbed()
      .setTitle('📋 Propostas Ativas')
      .setColor('#0099ff');
    
    proposals.forEach((proposal, index) => {
      embed.addField(
        `${index + 1}. ${proposal.title}`,
        `💰 ${proposal.fundingAmount} ETH\n⏰ ${proposal.timeLeft} dias restantes\n👍 ${proposal.votesFor} | 👎 ${proposal.votesAgainst}`,
        false
      );
    });
    
    message.reply({ embeds: [embed] });
  }

  async handleVoteCommand(message) {
    const args = message.content.split(' ');
    const proposalId = args[1];
    const vote = args[2]; // 'for' or 'against'
    
    // Verify user has tokens
    const userBalance = await this.getUserTokenBalance(message.author.id);
    
    if (userBalance === 0) {
      message.reply('❌ Você precisa ter tokens OFD para votar!');
      return;
    }
    
    // Record vote (simplified)
    message.reply(`✅ Seu voto "${vote}" na proposta #${proposalId} foi registrado!`);
  }
}
```

## 🚧 Desafios Enfrentados
1. **Governance complexity:** Complexidade de governança descentralizada
2. **Token economics:** Design de tokenomics sustentável
3. **Community building:** Construção de comunidade engajada
4. **Legal compliance:** Conformidade legal e regulatória
5. **Technical scalability:** Escalabilidade técnica

## 📚 Recursos Utilizados
- [DAO Handbook](https://daohandbook.xyz/)
- [OpenZeppelin Governance](https://docs.openzeppelin.com/contracts/4.x/governance)
- [Snapshot Documentation](https://docs.snapshot.org/)
- [Web3 Community Building](https://future.a16z.com/dao-canon/)

## 📈 Próximos Passos
- [ ] Implementar sistema de milestones
- [ ] Criar dashboard de métricas
- [ ] Adicionar sistema de reputação
- [ ] Implementar quadratic voting
- [ ] Criar programa de mentoria
- [ ] Desenvolver mobile app

## 🔗 Projetos Relacionados
- [Solidity CoinLink Token](../solidity-coinlink-token/) - Smart contracts
- [JS Wallet Generator](../js-wallet-generator/) - Crypto tools
- [CryptoTool](../CryptoTool/) - Cryptography library

---

**Desenvolvido por:** Felipe Macedo  
**Contato:** contato.dev.macedo@gmail.com  
**GitHub:** [FelipeMacedo](https://github.com/felipemacedo1)  
**LinkedIn:** [felipemacedo1](https://linkedin.com/in/felipemacedo1)

> 💡 **Reflexão:** Este projeto explorou as fronteiras da governança descentralizada e financiamento colaborativo. A experiência com DAOs demonstrou como a tecnologia blockchain pode democratizar o acesso a recursos e criar comunidades auto-organizadas em torno de objetivos comuns.