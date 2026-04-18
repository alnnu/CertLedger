# CertLedger

## Descricao

CertLedger e um sistema descentralizado para emissao, registro e validacao de certificados digitais academicos utilizando tecnologia Blockchain. A plataforma permite que instituicoes de ensino emitam certificados de conclusao de curso que sao armazenados de forma imutavel na blockchain Ethereum, garantindo autenticidade, transparencia e resistencia a fraudes. Cada certificado emitido gera um NFT (ERC-721) associado, cujos metadados e imagens sao armazenados no IPFS (InterPlanetary File System), assegurando a descentralizacao completa do armazenamento de dados.

## Proposta

O projeto busca resolver o problema de falsificacao e verificacao de certificados academicos. No modelo tradicional, a validacao de diplomas depende de processos burocraticos e centralizados, sujeitos a fraudes e demoras. O CertLedger propoe uma solucao onde:

- **Imutabilidade**: Uma vez registrado na blockchain, o certificado nao pode ser alterado ou falsificado.
- **Verificabilidade publica**: Qualquer pessoa pode verificar a autenticidade de um certificado informando seu hash, sem depender da instituicao emissora.
- **Propriedade digital**: O aluno recebe um NFT (token ERC-721) como prova de propriedade do certificado, associado a sua carteira Ethereum.
- **Armazenamento descentralizado**: A imagem do certificado e seus metadados sao armazenados no IPFS, eliminando dependencia de servidores centralizados.

### Fluxo de funcionamento

1. A instituicao preenche o formulario com os dados do certificado (aluno, curso, data, instituicao) e faz upload da imagem.
2. A imagem e enviada ao IPFS e um hash CID e gerado.
3. Os metadados (nome do NFT, descricao e link da imagem) sao enviados ao IPFS, gerando outro hash CID.
4. O smart contract `Cert` registra o certificado na blockchain e aciona o contrato `NftIPFS` para mintar um NFT com a URI apontando para os metadados no IPFS.
5. Qualquer pessoa pode verificar o certificado informando o hash dos metadados na pagina inicial.

## Tecnologias Utilizadas

### Blockchain / Smart Contracts

| Tecnologia                 | Versao                     | Finalidade                                                             |
| -------------------------- | -------------------------- | ---------------------------------------------------------------------- |
| **Solidity**               | ^0.8.0 (compilador 0.8.21) | Linguagem dos smart contracts                                          |
| **Truffle**                | -                          | Framework de desenvolvimento, compilacao, deploy e testes              |
| **OpenZeppelin Contracts** | ^5.4.0                     | Implementacao padrao ERC-721 (NFTs) e ERC721URIStorage                 |
| **Ganache**                | -                          | Blockchain local para desenvolvimento e testes                         |
| **Geth**                   | -                          | Client Ethereum usado para deploy em rede privada (network_id: 141319) |
| **HDWallet Provider**      | ^2.1.15                    | Provider para assinar transacoes com chave privada no deploy           |

### Frontend

| Tecnologia                | Versao            | Finalidade                                                    |
| ------------------------- | ----------------- | ------------------------------------------------------------- |
| **Next.js**               | 16.0.0            | Framework React com SSR e roteamento                          |
| **React**                 | 19.2.0            | Biblioteca de interface                                       |
| **TypeScript**            | ^5                | Tipagem estatica                                              |
| **Ethers.js**             | ^6.15.0           | Interacao com smart contracts via MetaMask                    |
| **Tailwind CSS**          | ^4                | Estilizacao utilitaria                                        |
| **shadcn/ui + Radix UI**  | -                 | Componentes de interface (Card, Table, Dialog, Popover, etc.) |
| **React Hook Form + Zod** | ^7.66.0 / ^4.1.12 | Gerenciamento e validacao de formularios                      |
| **Axios**                 | ^1.13.1           | Comunicacao HTTP com a API do IPFS                            |
| **Sonner**                | ^2.0.7            | Notificacoes toast                                            |

### Infraestrutura

| Tecnologia                  | Finalidade                                                            |
| --------------------------- | --------------------------------------------------------------------- |
| **Docker + Docker Compose** | Containerizacao do frontend, blockchain (Truffle + Ganache) e IPFS    |
| **IPFS (Kubo)**             | Armazenamento descentralizado de imagens e metadados dos certificados |

## Arquitetura do Projeto

```
CertLedger/
├── blockchain/
│   ├── contracts/
│   │   ├── Cert.sol          # Contrato principal - registro de certificados
│   │   ├── NftIPFS.sol       # Contrato NFT ERC-721 com URI no IPFS
│   │   └── Nft.sol           # Contrato NFT basico (versao inicial)
│   ├── migrations/
│   │   └── 1_deploy_contracts.js   # Script de deploy dos contratos
│   ├── test/
│   │   ├── unit/
│   │   │   ├── cert.test.js          # Testes unitarios do contrato Cert
│   │   │   ├── nft.test.js           # Testes unitarios do contrato NftIPFS
│   │   │   └── faliure.cert.test.js  # Testes de cenarios de falha
│   │   └── integration/
│   │       └── createCertFlow.test.js # Teste de integracao do fluxo completo
│   ├── Dockerfile
│   ├── docker-compose.yml    # IPFS node (Kubo)
│   └── truffle-config.js
├── frontend/
│   ├── src/
│   │   ├── app/
│   │   │   ├── page.tsx              # Pagina inicial - verificacao de certificado
│   │   │   ├── criar/page.tsx        # Pagina de emissao de certificado
│   │   │   ├── certificado/
│   │   │   │   ├── page.tsx          # Listagem de todos os certificados
│   │   │   │   └── [hash]/page.tsx   # Visualizacao detalhada de um certificado
│   │   │   └── components/
│   │   │       ├── hero.tsx          # Componente hero com campo de busca
│   │   │       ├── WalletButton.tsx  # Botao de conexao com MetaMask
│   │   │       ├── certificado/
│   │   │       │   ├── createForm.tsx # Formulario de criacao de certificado
│   │   │       │   └── certTable.tsx  # Tabela de certificados
│   │   │       └── layout/
│   │   │           └── nav.tsx       # Navbar responsiva
│   │   ├── service/
│   │   │   ├── web3.ts       # Servicos de interacao com blockchain
│   │   │   └── ipfs.ts       # Servicos de interacao com IPFS
│   │   ├── lib/
│   │   │   ├── cert.ts       # Conversao de dados do contrato para objetos
│   │   │   ├── axios.ts      # Instancia Axios para API IPFS
│   │   │   └── utils.ts      # Utilitarios (cn)
│   │   └── schema/
│   │       └── certSchema.ts # Tipos TypeScript dos certificados
│   ├── Dockerfile
│   └── docker-compose.yml
```

## Smart Contracts

### Cert.sol (Contrato Principal)

Responsavel pelo registro e consulta de certificados. Cada certificado armazena:

- **subject**: Nome do aluno
- **course**: Nome do curso
- **date**: Data de emissao
- **credentials**: Nome da instituicao emissora e chave publica (endereco) de quem emitiu
- **display**: Hash da imagem, hash dos metadados e ID do NFT

Funcoes principais:

- `addCert()` - Registra um novo certificado e minta automaticamente um NFT
- `getCert()` - Busca um certificado pelo hash dos metadados
- `getAllCerts()` - Retorna todos os certificados registrados

### NftIPFS.sol (Contrato NFT)

Implementa o padrao ERC-721 com ERC721URIStorage do OpenZeppelin. Cada NFT possui uma URI apontando para metadados armazenados no IPFS (`ipfs://<CID>`).

## Testes

O projeto possui testes unitarios e de integracao utilizando o framework de testes do Truffle (Mocha/Chai):

- **Unitarios**: Criacao e recuperacao de certificados, mintagem de NFTs, verificacao de dados
- **Falha**: Busca de certificado inexistente (revert esperado), criacao com endereco invalido
- **Integracao**: Fluxo completo de criacao verificando endereco do contrato NFT, dados do certificado, URI do token e propriedade do NFT

## Dificuldades Encontradas

### 2. Deploy em rede privada Geth

A transicao do ambiente de desenvolvimento (Ganache) para uma rede privada Geth exigiu configuracao adicional de HDWallet Provider, desbloqueio de contas e ajuste de `network_id` (141319). O gerenciamento de chaves privadas via variaveis de ambiente (`.env`) foi necessario para seguranca no deploy.

### 4. Evolucao do contrato NFT

O projeto passou por uma evolucao do contrato NFT: inicialmente o `Nft.sol` armazenava metadados como `uint256` diretamente on-chain. Posteriormente, foi refatorado para o `NftIPFS.sol` que utiliza `ERC721URIStorage` para armazenar URIs IPFS como strings, uma abordagem mais padronizada e interoperavel com marketplaces e wallets.

### 5. Upload e rollback no IPFS

O formulario de criacao de certificados implementa um mecanismo de rollback: se o upload da imagem ou dos metadados no IPFS falhar apos ja ter sido feito parcialmente, o sistema remove (unpin + garbage collect) os arquivos ja enviados para evitar dados orfaos no no IPFS.

### 6. Integracao frontend com MetaMask e rede privada

Conectar o frontend a uma rede Ethereum privada via MetaMask exigiu validacao do `chainId` (141319n) e tratamento de erros especificos de conexao de carteira. A interacao com os contratos via `ethers.js` demandou configuracao manual de `gasLimit` para evitar falhas em transacoes mais complexas.
