# Solidity, contrat et IPFS

Dans ce chapitre nous allons voir le langage Solidity permettant l'écriture de smart contract sur la blockchain [ETH](https://ethereum.org/fr/) et plus largement toutes les blockchains compatible [EVM]

Nous verrons aussi la réalisation d'un environnement de dev, puis l'implémentation d'un contrat avec Remix, puis nous aborderons l'utilisation d'[IPFS](#ipfs) afin de décentraliser les ressources vers lesquelles pointent nos tokens.

## Table of contents

1. [Rappel sur les smart contracts](#backtosmartcontract)
2. [Solidity POO pour smart contract](#whatissolidity)
   - [L'environnement de dev](#devenvironment)
     - [Ganache-CLI](#ganache)
     - [VSCode/Remix](#vscodeorremix)
     - [Truffle](#truffle)
     - [Jest/Chai](#jestandchai)
     - [Metamask](#metamask)
3. [Notre premier smart contract ERC721](#urfirstcontract)
   - [Contrat du token](#nftcontract)
     - [Base du contrat et standard ERC721](#nftcontractbase)
     - [Réalisation du contrat NFT avec IPFS](#nftcontractipfs)
   - [Chainlink Price Feed pour un prix en temps réel](#pricefeed)
   - [Réalisation d'un Minter portant des limitations](#minter)
     - [Liaison du Minter avec Price Feed](#minterpricefeed)
   - [Réalisation d'une factory pour déployer](#factorydeploy)
     - [Déploiement testnet et test avec Remix](#remixtestnetandtest)
4. [Gestion de la ressource avec IPFS](#ipfs)
   - [IPFS c'est quoi ?](#whatisipfs)
   - [Comment ça marche ?](#ipfshowitworks)
   - [Le rôle d'IPFS avec les NFT](#ipfsfornft)
   - [Uploader vos metadata sur IPFS](#ipfsuploadurs)
5. [Finalité du chapitre](#final)

## Rappel sur les smart contracts <a name="backtosmartcontract"></a>

</br>
<img align="right" height="300px" src="../assets/whatisnft.jpg"/>
<p align="left">Un smart contract est un programme qui contrôle des actifs numériques, ce contrat intelligent vient convertir un accord entre deux parties en code informatique</p>
<p align="left">Il fige les règles de cet accord entre plusieurs parties dans la blockchain tout en assurant le transfert d'un actif, à l'instar des contrats légaux traditionnel, lorsque les conditions définies par le contrat se vérifient</p>

</br>

Ce type de contrat peut s'appliquer à de multiples domaines :

- Les assurances
- L'immobilier
- Les supply chains (UPS,La poste...)
- La finance

</br>

Les smart contract ne permettent pas uniquement d'automatiser les accords, ils les restreignent dans leurs actions.
Ce genre de contrat tant à se développer dans les domaines nécessitant par exemple d'assurer le respect de la conformité.

On peut "tout" créer avec un smart contract à l'image d'un langage comme le C# cependant le coût d'exécution et lui pris en compte lors de l'utilisation d'une fonction du code.

Il faudra donc optimiser son code au maximum afin de réduire au minimum les frais de transactions lorsque l'utilisateur contactera votre contrat

## Solidity POO pour smart contract <a name="whatissolidity"></a>

[Solidity] est un langage de programmation orienté objet de haut niveau utilisé dans l'implémentation de smart contract sur diverses blockchains, et notamment Ethereum.

C'est un langage de type statique conçu pour compilé le code Solidity en [Bytecode] afin que les contrats s'exécutent sur une [EVM] (Ethereum Virtual Machine).

<p align="center"><img src="../assets/evm.png"></p>

Solidity est disponible sur les blockchains:

- [Ethereum]
- [Tendermint]
- [Tron]
- [Binance Smart Chain]

### L'environnement de dev <a name="devenvironment"></a>

La blockchain peut paraître complexe et demande un environnement de développement très complet dans notre cas nous allons avoir besoin de plusieurs logiciels/librairies/IDE:

- [Ganache](#ganache) -> Permet de simuler un noeud ethereum en local
- [VSCode/Remix](#vscodeorremix) -> Un IDE local et un IDE en ligne
- [Truffle](#truffle) -> Un environnement de développement, un cadre de test et un pipeline d'actifs (js,css...)
- [Jest/Chai](#jestandchai) -> Librairie de test et d'assertion pour tester notre contrat ou notre DAPP
- [Metamask](#metamask) -> Un wallet de crypto monnaie permettant le test de notre blockchain local

#### Ganache-CLI <a name="ganache"></a>

La ganache, ou crème ganache, ... (ok je rigole)
<br/>
<img src="../assets/ganache.svg" height="200" align="right">
<br/>
Ganache, est un outil utilisé pour simuler une blockchain en local afin de rapidement développer vos contrat sur la blockchain Ethereum:

- [Github Ganache](https://github.com/trufflesuite/ganache)
- [Documentation Ganache](https://www.trufflesuite.com/docs/ganache/overview)

Il est utilisé tout au long du cycle de développement, il permet de développer, déployer et tester votre application dans un environnement sûr et déterministe.

Nous l'utiliserons dans la partie 3 de cette suite d'article afin de déployer notre contrat sur testnet sans Remix.

#### VSCode/Remix <a name="vscodeorremix"></a>

Afin de développer facilement un contrat il est conseillé de d'abord le faire sur [Remix](https://remix-project.org/) puis de l'ajouter au projet final sur [VScode](https://code.visualstudio.com/).

Pour utiliser VSCode je vous conseille d'installer l'extension portant la syntaxe Solidity dans les fichiers `.sol` -> [Solidity extension](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity).

<img src="../assets/remix.png" align="left" height="150" style="margin-right: 1rem"/>

Remix est un IDE en ligne qui permet de développer, déployer et administrer un smart contract pour les blockchains de type Ethereum.

Remix embarque un compilateur de script Solidity et un réseau de test afin de déployer le contrat.

Remix permet aussi d'avoir accès à une interface exposant les fonctions du contrat afin de tester ces fonctions.

<br/>

Il peut être utiliser en tant que plateforme d'apprentissage, il suffit pour cela de retrouver le contrat que vous voulez comprendre sur la blockchain par exemple:

<p align="center"><img src="https://sothebys-md.brightspotcdn.com/ab/6c/a29219844b9385ea689c9722b7b2/fade2.svg" height="150"></p>

Le contrat utilisé pour générer ce NFT [Fade](https://www.sothebys.com/en/buy/auction/2021/natively-digital-a-curated-nft-sale-2/to-be-announced) est trouvable facilement avec son adresse
`0x62F5418d9Edbc13b7E07A15e095D7228cD9386c5`.

en utilisant [Blockscan](https://blockscan.com) on retrouve facilement le contrat utilisé pour Fade
([Lien du contrat](https://etherscan.io/address/0x62F5418d9Edbc13b7E07A15e095D7228cD9386c5#code#F1#L1)).

En copiant `Fade.sol` dans Remix je vais donc pouvoir avoir accès à toutes les fonctions du contrat en local.
On poura donc comprendre ce qu'il se passe derrière la fonction `tokenURI` qui retourne un gradient différent à chaque fois.

#### Truffle <a name="truffle"></a>

<img src="../assets/truffle.svg" height="200" align="left">

Truffle est un framework de développement pour Ethereum qui a pour mission de rendre le développement blockchain normalement complexe accessible à tous.

Truffle va permettre de créer des migrations afin de déployer nos contrats, mais aussi l'exécution des tests permettant de certifier la sécurité et la qualité de ceux-ci.

Dans le monde centralisé lorsque j'exécute un script JS c'est l'hébergeur qui répercute le prix, dans le monde décentralisé lorsque j'exécute une transaction avec un smart contract (donc j'exécute le script ou une partie du script de ce smart contract) je paye les frais en rapport avec le coût des fonctions qui seront exécutées.

#### Jest/Chai <a name="jestandchai"></a>

[Jest](https://jestjs.io/fr/) est un framework de test JS qui va nous permettre de tester notre contrat ainsi que notre Dapp

[Chai](https://www.chaijs.com/) est une librairie JS qui va nous permettre de réaliser des assertions, c'est-à-dire, à la suite d'un test de vérifier les valeurs retournées.

Garder les en mémoire, Jest et Chai sont conseillés lors de l'utilisation de Truffle et Ganache pour l'éxécution des tests automatisés, mais aussi du déploiement automatique lors des tests.

#### Metamask <a name="metamask"></a>

[Metamask](https://metamask.io/) est un wallet (porte-monnaie) numérique, il va nous permettre de stocker nos NFT ou autre Cryptos.

Metamask va permettre la gestion des échanges sur la blockchain, l'affichage de votre solde.

Il permet de se connecter au réseau local créer par [Ganache](#ganache) afin de tester sa Dapp avant de la fournir au monde entier.

## Notre premier smart contract ERC721 <a name="urfirstcontract"></a>

Afin de réaliser notre premier smart contract et de simplifier la partie développement nous allons utiliser uniquement [Remix](https://remix.ethereum.org) dans cette partie.

### Contrat du token <a name="nftcontract"></a>

Nous allons donc créer le premier NFT de Younup, pour ce faire dans Remix, créer un nouveau fichier nommé `YounupNFT.sol`

#### Base du contrat et standard ERC721 <a name="nftcontractbase"></a>

On commence par importer les standards portés par OpenZeppelin et les indications utiles au compilateur:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.12;

// Import of ERC721 Enumerable standard
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
// Import of Ownable standard
import "@openzeppelin/contracts/access/Ownable.sol";
// Import of Strings standard
import "@openzeppelin/contracts/utils/Strings.sol";
```

#### Réalisation du contrat NFT avec IPFS <a name="nftcontractipfs"></a>

Avant de commencer si vous voulez en apprendre plus sur IPFS et uploader votre première image de façon décentralisée rendez-vous au chapitre [IPFS](#ipfs).

On va ensuite définir le contrat:

```solidity
contract YounupNFT is ERC721Enumerable, Ownable {
  //uint256 variable could use Strings library, example: value.toString()
  using Strings for uint256;

  //Declare metadata of token
  // ipfsURI => Internet Protocol File Storage URI
  string public ipfsURI;
  // ipfsExt => Extension like .json
  string public ipfsExt;

  constructor(string memory _name, string memory _symbol, string memory _ipfsURI) ERC721(_name, _symbol) {
    //Require is use to check the variable value, it will throw "No IPFS URI provided" if you don't pass it
    require(byte(_ipfsURI).length > 0, "No IPFS URI provided");
    // We allocate ipfsURI to the ipfsURI passed on contract deployment
    ipfsURI = _ipfsURI;
  }

  // This function return the metadata of a given token, here it will return our ipfsURI (json metadata)
  function tokenURI(uint256 tokenId) public view virtual override returns (string memory) {
    require(_exists(tokenId), "ERC721URIStorage: URI query for nonexistent token");
    return string(ipfsURI);
  }

  // This function is use to mint (obtain, buy...) a token, it will use _safeMint from ERC721 standard
  function mint(address recipient) public onlyOwner returns(uint256) {
    uint256 tokenId = this.totalSupply();
    _safeMint(recipient, tokenId);
    return tokenId;
  }
}
```

### Chainlink Price Feed pour un prix en temps réel <a name="pricefeed"></a>

Nous voulons vendre notre NFT à un prix fixe en $, on va alors utiliser le principe de [Price Feed](https://docs.chain.link/docs/get-the-latest-price/) de [Chainlink](https://docs.chain.link/)

Le principe est simple à l'instant donnée où l'on veut mint le NFT le smart contract va faire appelle au Price feed pour nous fournir une conversion de $ en crypto, ici nous utiliserons le token Matic de [Polygon](https://polygon.technology/) pour ses frais minimes.

`YounupNFT-priceFeed.sol`

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.12;

//Import Chainlink aggregator to call datafeed with price
import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

contract YounupNFTPriceFeed {
  // Private and immutable aggregator for security concern
  AggregatorV3Interface private immutable _aggregator;

  // We use feedAddress (address of the token) in the aggregator
  constructor(address feedAddress) {
    _aggregator = AggregatorV3Interface(feedAddress);
  }

  // Return last price of the token
  function getLatestPrice() external view returns (int) {
    // Only price not commented, we need to pass 5 arg, but we don't need the others datas
        (
          /*uint80 roundID*/,
          int price,
          /*uint startedAt*/,
          /*uint timeStamp*/,
          /*uint80 answeredInRound*/
        ) = _aggregator.latestRoundData();
        return price;
  }

  // Return the price decimals (since not all token have the same decimal we need to know it)
  function decimals() external view returns (uint8) {
    return _aggregator.decimals();
  }
}

// We add another contract so we could just deploy this one and don't need to send the token adress in the constructor since it's fixed here
// Here we pass the data from the MATIC testnet or mainnet because our NFT will be on polygon (MATIC)
contract PriceFeedMaticUSD is YounupNFTPriceFeed {
    /**
     * Network: Polygon
     * Aggregator: MATIC/USD
     * Address (mainnet): 0xAB594600376Ec9fD91F8e885dADF0CE036862dE0
     * Address (testnet): 0xd0D5e3DB44DE05E9F294BB0a3bEEaF030DE24Ada
     * From: https://docs.chain.link/docs/matic-addresses/
     */
  constructor () YounupNFTPriceFeed(0xd0D5e3DB44DE05E9F294BB0a3bEEaF030DE24Ada) {}
}
```

### Réalisation d'un Minter portant des limitations <a name="minter"></a>

Afin d'appliquer des limites de mint à notre NFT, par exemple limité son émission ou encore le nombre de token autorisé par utilisateur nous allons créer ce que l'on appelle un Minter.
C'est ce contrat qui sera le seul à avoir le droit d'appeler la fonction mint de notre contrat NFT via l'utilisation du principe `Ownable`, en effet notre contrat Minter va déployer le contrat du NFT et sera donc Owner.
Tout d'abord nous allons définir la structure des paramètres d'entrée du contrat.

`YounupNFT-minter.sol`

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.12;

import "./YounupNFT-priceFeed.sol"
import "./YounupNFT.sol"

struct Parameters {
    uint8 chainCurrencyDecimals; // Decimal of the token use
    uint presalePriceUSD; // First sale price in USD
    uint publicsalePriceUSD; // Seconde sale price in USD
    uint maxSupply; // Max token mintable
    uint maxMint; // Max mint by user
    Token token; // Token is the YounupNFT token
    YouNFTupPriceFeed priceFeed; // Our created pricefeed contract
    Periods periods; // We will use periods to know when it's first or second sales
    address payable teamAddress; // This is the team wallet that will receive all funds
}

struct Token {
  string name; // Token name ex: Ethereum
  string symbol; // Token symbol ex: ETH
  string ipfsURI; // Token IPFS Uri
}

struct Periods {
  // timestamp use for start and end private sale
  uint startPresalePeriod;
  uint endPresalePeriod;
  // timestamp use for start and end of public sale
  uint startPublicsalePeriod;
  uint endPublicsalePeriod;
}
```

Maintenant que nous avons définis les structures qui permettront d'utiliser et sécuriser notre contrat on peut écrire le reste:

```solidity
contract YounupNFTMinter {
  // We use it so we could now at wich state we are
  enum State
  {
    waitingPeriod, // no mint possible
    presalePeriod, // Mint with the private sale price
    publicsalePeriod, // Mint with the public sale price
    complete // All items are minted no more mint possible or you have outdated the endPublicSalePeriod
  }

   // Chain info and team wallet
  uint8 public immutable chainTokenDecimals;
  address payable public immutable teamAddress;
  YounupNFT public immutable token;

  //NFT max supply
  uint public immutable maxSupply;

  //NFT max mint per wallet
  uint public immutable maxMint;

  // NFT price in USD
  uint public immutable presalePriceUSD;
  uint public immutable publicsalePriceUSD;
  uint constant ticketPriceDecimals = 2;

  //Periods
  Periods public periods;

  //Chainlink Price feed
  YounupNFTPriceFeed public immutable priceFeed;

   constructor(Parameters memory params) {
     // Many test use to ensure that the periode are logics
      require(params.periods.startPresalePeriod > block.timestamp, "Invalid timestamp: startPresalePeriod");
      require(params.periods.endPresalePeriod > params.periods.startPresalePeriod, "Invalid timestamp: endPresalePeriod");
      require(params.periods.startPublicsalePeriod > params.periods.endPresalePeriod, "Invalid timestamp: startPublicsalePeriod");
      require(params.periods.endPublicsalePeriod > params.periods.startPublicsalePeriod, "Invalid timestamp: endPlubicsalePeriod");

      // We create the contract for the Token here
      token = new YounupNFT(this, params.token.name, params.token.symbol, params.token.ipfsURI);

      chainTokenDecimals = params.chainCurrencyDecimals;
      presalePriceUSD = params.presalePriceUSD;
      publicsalePriceUSD = params.publicsalePriceUSD;
      maxSupply = params.maxSupply;
      maxMint = params.maxMint;
      priceFeed = params.priceFeed;
      periods = params.periods;

      teamAddress = params.teamAddress;
    }
}
```

#### Liaison du Minter avec Price Feed <a name="minterpricefeed"></a>

Notre contrat est maintenant initialiser mais il reste encore à mettre en place la possibilité de mint au prix voulu :

```solidity
  function mintNFT() external payable {
    // Check if contract is in state where you could mint
    require(getState() == State.presalePeriod || getState() == State.publicsalePeriod, "You can't mint it right now");

    // Get the mint price from priceFeed and ensure everything is ok with all limits and price passed
    uint entryPrice = getMintPrice();
    require(msg.value >= entryPrice, "Not enough funds to mint YouNFTup. See getMintPrice()");
    require(token.totalSupply() < maxSupply, "Too much NFT minted");
    require(token.balanceOf(msg.sender) < maxMint, "You could mint only 1 YouNFTup");

    // Mint the token using the YounupNFT mint function
    token.mint(msg.sender);
    // We also transfer all the fund sent to the contract to the team wallet
    teamAddress.transfer(address(this).balance);
  }

  // Function use to get the current state of the contract
  function getState() public view returns(State) {
    if (block.timestamp < periods.startPresalePeriod) {
        return State.waitingPeriod;
    }
    if (block.timestamp < periods.endPresalePeriod) {
        return State.presalePeriod;
    }
    if (block.timestamp < periods.startPublicsalePeriod) {
        return State.waitingPeriod;
    }
    if (block.timestamp < periods.endPublicsalePeriod) {
        return State.publicsalePeriod;
    }
    return State.complete;
  }

  // Function use to get the mint price using priceFeed.getLatestPrice()
  function getMintPrice() public view returns(uint) {
    int latestPrice = priceFeed.getLatestPrice();
    uint256 ticketPriceUSD = publicsalePriceUSD;
    if (getState() == State.presalePeriod) {
        ticketPriceUSD = presalePriceUSD;
    }

    uint latestPriceAdjusted = uint(latestPrice) * 10 ** (chainTokenDecimals - priceFeed.decimals());

    uint entryPrice = 10 ** (chainTokenDecimals * 2) * ticketPriceUSD / latestPriceAdjusted / (10 ** ticketPriceDecimals);
    return entryPrice;
  }

  // Function to check if the contract is complete or no
  function isComplete() public view returns(bool) {
    return getState() == State.complete;
  }
```

Il nous faut aussi mettre à jour le token car nous utilisons maintenant un minter, comme vu à la ligne suivante:
`token = new YounupNFT(this, params.token.name, params.token.symbol, params.token.ipfsURI);`
this équivaut ici à `YounupNFTMinter`

`YounupNFT.sol`

```solidity
import "./YounupNFTMinter.sol"

...

constructor(YouNFTupMinter _minter, ...) {
  // We keep info about the minter contract address
  minter = _minter;
}
```

La fonction mint étant `public onlyOwner` seul le contrat Minter pourra minter un token YounupNFT

### Réalisation d'une factory pour déployer <a name="factorydeploy"></a>

Il ne nous reste plus qu'à déployer notre contrat pour simplifier cette partie je réalise une factory qui crée le constructeur plutôt que de le déclarer au déploiement sous forme de Tupple.

`YounupNFT-factory.sol`

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.12;

import "./YouNFTup-minter.sol";

contract YouNFTupFactory is YouNFTupMinter {
  constructor() YouNFTupMinter(Parameters({
      chainCurrencyDecimals: 18,
      presalePriceUSD: 100,
      publicsalePriceUSD: 200,
      maxSupply: 42,
      maxMint: 1,
      token: Token({
          name: "YouNFTup",
          symbol: "YNP",
          ipfsURI: "https://ipfs.io/ipfs/bafkreihmkaetjsqkb2cnzdkyhvpattzpj7duuqj4kfvtx3qfcitj2didbu"
      }),
      priceFeed: new PriceFeedMaticUSD(),
      periods: Periods({
          startPresalePeriod: block.timestamp + 1,
          endPresalePeriod: block.timestamp + 3600 * 24 * 30,
          startPublicsalePeriod: block.timestamp + 3600 * 24 * 60,
          endPublicsalePeriod: block.timestamp + 3600 * 24 * 90
      }),
      teamAddress: payable(0xB677dd9Ae9217Fbb4E3d072b9F7F68947C2a4AA6)
  })) {}
}
```

#### Déploiement testnet et test avec Remix <a name="remixtestnetandtest"></a>

Il ne reste plus qu'à compiler notre contrat et le déployer sur le réseau de test Matic.
_(pour ajouter le réseau c'est [ici](https://docs.polygon.technology/docs/develop/metamask/config-polygon-on-metamask/))_

Dans la partie "Solidity Compiler" de remix il faut choisir la version du compiler 0.8.12 puis compiler le contrat `YounupNFT-factory.sol` qui importe tous les autres.

Si vous avez bien installé Metamask et que vous avez ajouté le réseau `Matic Mumbai` nous allons maintenant déployer notre contrat.

Dans la partie "Deploy & run transactions" de Remix, il faut choisir pour Environment "Injected Web3", vous aurez alors une notification Metamask pensez à bien vérifier que vous êtes sur le testnet de polygon avant de déployer.

On sélectionne ensuite le contrat `YounupNFT-factory` puis on clique sur "Deploy", une transaction va alors apparaître sur votre Metamask si vous l'acceptez vous aller payer les fee de déploiement d'un contrat (sur testnet donc gratuit)

Vous aurez alors accès à toutes les fonctions de votre contrat pour pouvoir le tester, vous pouvez finalement minter votre premier NFT et vérifier son existence via [OpenSea Testnet](https://testnets.opensea.io/)

<p align="center"><img src="../assets/contract-func.PNG"></p>

## Gestion de la ressource avec IPFS <a name="ipfs"></a>

### IPFS c'est quoi ? <a name="whatisipfs"></a>

IPFS ou InterPlanetary File System est un protocole P2P (pair à pair) Web3.0.
Dans le monde du Web 2.0 nous stockons nos fichiers sur des serveurs centralisés, ici l'idée est de reposer sur la décentralisation et un réseau collaboratif afin d'héberger des fichiers.

### Comment ça marche ? <a name="ipfshowitworks"></a>

<p align="center"><img src="../assets/ipfs-example.jpg"></p>

L'objectif avec IPFS est de rendre le réseau :

- plus rapide
- plus sûr
- plus ouvert
- moins coûteux

Dans un idéal de décentralisation il tend à prendre une place considérable façe au protocole HTTP

### Le rôle d'IPFS avec les NFT <a name="ipfsfornft"></a>

Beaucoup de NFTs utilisent la blockchain pour leurs contrats mais n'utilisent aucun système décentralisé pour les ressources.

En effet il est important de mettre en ligne les metadata et tous les assets qui permettront la génération du NFT sur un réseau décentralisé.

L'objectif étant que le propriétaire du NFT soit en possession total de celui-ci sans que le créateur ne puisse détruire ou déplacer l'image par exemple.

### Uploader vos metadata sur IPFS <a name="ipfsuploadurs"></a>

Afin de récupérer l'URL ipfs qui permettra de récupérer les infos de notre NFT dans le contrat.

En partant de ce que nous allons développer [ici](#nftcontractipfs) :

```
      token: Token({
          name: "YouNFTup",
          symbol: "YNP",
          ipfsURI: "https://ipfs.io/ipfs/bafkreihmkaetjsqkb2cnzdkyhvpattzpj7duuqj4kfvtx3qfcitj2didbu"
      }),
```

> _l'entièreté de cette tâche peut être automatisé par du script_

Nous allons utiliser [nft.storage](https://nft.storage) pour ce faire, il existe aussi [pinata](https://www.pinata.cloud/) :

- Créer un compte ou connectez-vous avec github
- Uploader votre image afin d'avoir un lien IPFS
- Rédiger le fichier `metadata.json`

```JSON
{
  "description": "Official Younup NFT",
  "external_url": "https://younup.fr",
  "image": "ipfs://your-image-link", //example: https://ipfs.io/ipfs/bafkreigf2d4qgt6klvuxwr7d3yvkpjlrszlfhgpbphbdvwkzljivef24iu
  "name": "YouNFTup",
  "attributes": [] // You can add many attributes check OpenSea doc
}
```

- Uploader le fichier de metadata

Finalement, vous obtiendrez un fichier metada que vous pourrez lier à votre NFT.

Example : https://ipfs.io/ipfs/bafkreihmkaetjsqkb2cnzdkyhvpattzpj7duuqj4kfvtx3qfcitj2didbu

## Finalité du chapitre <a name="final"></a>

Le code : https://github.com/SoxZz5/NFT-tuto/tree/master/project/contracts

Résultat: https://testnets.opensea.io/assets/mumbai/0xe839ceaa7d410c7e957a97d970b8042b13a21c28/0

Minter contract: https://mumbai.polygonscan.com/address/0xc7cdcdfa7c724bd7148efb53a5774928f56dab46

NFT contract: https://mumbai.polygonscan.com/address/0xe839ceaa7d410c7e957a97d970b8042b13a21c28

<!-- LINK -->

[solidity]: https://docs.soliditylang.org/en/v0.8.9/ 'Solidity'
[evm]: https://ethereum.org/en/developers/docs/evm/ 'EVM'
[bytecode]: https://fr.wikipedia.org/wiki/Bytecode 'Bytecode'
[ethereum]: https://ethereum.org/en/ 'Ethereum'
[tendermint]: https://tendermint.com/ 'Tendermint'
[tron]: https://tron.network/ 'Tron'
[binance smart chain]: https://academy.binance.com/fr/articles/an-introduction-to-binance-smart-chain-bsc 'Binance Smart Chain'
