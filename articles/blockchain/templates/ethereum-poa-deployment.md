---
title: Implantar o modelo de solução do consórcio de prova de autoridade Ethereum no Azure
description: Usar a solução do consórcio de prova de autoridade do Ethereum para implantar e configurar uma rede Ethereum do consórcio de vários membros no Azure
ms.date: 04/08/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: c3e449c1d6ebaf7c6cb2c35dc9f91d55f569447a
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326167"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Implantar o modelo de solução do consórcio de prova de autoridade Ethereum no Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[O modelo de solução do Azure Ethereum de verificação de autoridade do consórcio](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) foi projetado para facilitar a implantação, a configuração e o controle de uma rede Ethereum de prova de autoridade do consórcio de vários membros com o conhecimento mínimo do Azure e do Ethereum.

Com algumas entradas de usuário e uma implantação de clique simples por meio do portal do Azure, cada membro pode provisionar sua superfície da rede, usando os serviços de computação, rede e armazenamento do Microsoft Azure em todo o mundo. O volume de rede de cada membro consiste em um conjunto de nós de validador com balanceamento de carga com o qual um aplicativo ou usuário pode interagir para enviar transações Ethereum.

## <a name="concepts"></a>Conceitos

### <a name="terminology"></a>Terminologia

-   **Consenso** – o ato de sincronização de dados em toda a rede distribuída por meio da criação e validação do bloco.

-   **Membro do Consortium** - uma entidade que participa de um consenso sobre a rede de Blockchain.

-   **Admin** - Uma conta Ethereum que é usada para gerenciar a participação de um membro de determinado consórcio.

-   **Validador** – uma máquina associada a uma conta Ethereum que participa de consenso em nome de um administrador.

### <a name="proof-of-authority"></a>Prova de autoridade

Para aqueles que são novos na comunidade de blockchain, o lançamento dessa solução é uma grande oportunidade para saber mais sobre a tecnologia de uma maneira fácil e configurável no Azure. A prova de trabalho é um mecanismo de resistência Sybil que aproveita os custos de computação para autorregular a rede e permitir a participação justa. Isso funciona muito bem em redes de blockchain anônimas abertas onde a competição por criptomoeda promove a segurança na rede. No entanto, em redes privadas/consórcio o Ether subjacente não tem valor. Um protocolo alternativo, uma prova de autoridade, é mais adequado para redes permitidas em que todos os participantes de consenso são conhecidos e respeitáveis. Sem a necessidade de mineração, a prova de autoridade é mais eficiente enquanto ainda retém a tolerância a falhas de Byzantine.

### <a name="consortium-governance"></a>Governança do Consortium

Como a prova de autoridade depende de uma lista de autoridades de rede permitidas para manter a rede íntegra, é importante fornecer um mecanismo justo para fazer modificações nessa lista de permissões. Cada implantação vem com um conjunto de contratos inteligentes e o portal para governança na cadeia dessa lista de permissão. Quando uma alteração proposta atinge um voto maioria por membros consortium, a alteração é imposta. Isso permite que novos participantes de consenso sejam adicionados ou comprometidos para serem removidos de forma transparente que incentiva uma rede honesta.

### <a name="admin-account"></a>Conta de administrador

Durante a implantação dos nós de prova de autoridade, você será solicitado a fornecer um endereço de Ethereum de administrador. Você pode usar vários mecanismos diferentes para gerar e proteger esta conta Ethereum. Depois que esse endereço é adicionado como uma autoridade na rede, você pode usar essa conta para participar de governança. Essa conta de administrador também será usada para delegar a participação de consenso para os nós de validador que são criados como parte dessa implantação. Como apenas o endereço Ethereum público é usado, cada administrador tem a flexibilidade de proteger suas chaves privadas de uma forma que segue o modelo de segurança desejado.

### <a name="validator-node"></a>Nó do validador

No protocolo de prova de autoridade, os nós do validador tomam o lugar dos nós de mineração tradicional. Cada validador tem uma identidade exclusiva de Ethereum que é adicionada a uma lista de permissão do contrato inteligente. Depois que um validador estiver nessa lista, ele pode participar no processo de criação de bloco. Para saber mais sobre esse processo, consulte a documentação de paridade na [consenso Autoridade Round](https://wiki.parity.io/Aura). Cada membro do consórcio pode provisionar dois ou mais nós de validador em cinco regiões, para redundância geográfica. Os nós do validador se comunicam com outros nós de mineração para chegar a consenso sobre o estado da razão distribuído de forma subjacente.
Para garantir a participação justa na rede, cada membro do consórcio é proibido de usar validadores mais que o primeiro membro na rede (se o primeiro membro implantar três validadores, cada membro só pode ter até três validadores).

### <a name="identity-store"></a>Armazenamento de identidade

Como cada membro terá vários nós de validador em execução simultaneamente e cada nó deve ter uma identidade permitida, é importante que os validadores possam adquirir com segurança uma identidade ativa exclusiva na rede. Para tornar isso mais fácil, criamos um repositório de identidades que é implantado na assinatura de cada membro que mantém com segurança as identidades Ethereum geradas. Após a implantação, o contêiner de orquestração irá gerar uma chave privada Ethereum para cada validador e armazená-lo em Azure Key Vault. Antes do nó de paridade ser iniciado, ele primeiro adquire uma concessão em uma identidade não utilizada para garantir que a identidade não seja selecionada por outro nó. A identidade é fornecida ao cliente que fornece a ele a autoridade para iniciar a criação de blocos. Se a VM de hospedagem sofrer uma interrupção, a concessão de identidade será liberada, permitindo que um nó de substituição retome sua identidade no futuro.

### <a name="bootnode-registrar"></a>Registrador Bootnode

Para habilitar a facilidade de conectividade, cada membro hospedará um conjunto de informações de conexão com os [dados de ponto de extremidade de API](#data-api). Esses dados incluem uma lista de bootnodes que são fornecidos como nós de emparelhamento para o membro de junção. Como parte desses dados API, podemos manter essa lista bootnode atualizada

### <a name="bring-your-own-operator"></a>Traga seu próprio operador

Muitas vezes um membro do consórcio deseja participar de governança de rede, mas não quer operar e manter sua infraestrutura. Ao contrário dos sistemas tradicionais, tendo um único operador na rede, funciona com base no modelo descentralizado dos sistemas de blockchain. Em vez da contratação de um intermediário centralizado para operar uma rede, cada membro do consórico pode delegar o gerenciamento da infraestrutura para o operador de sua escolha. Isso permite que um modelo híbrido em que cada membro possa escolher operar sua própria infraestrutura ou delegar operação para um parceiro diferente. O fluxo de trabalho de operação delegado funciona da seguinte maneira:

1.  **Membro do Consortium** gera um endereço Ethereum (mantém a chave privada)

2.  **Membro do Consórcio** fornece o endereço público Ethereum ao **Operador**

3.  **Operador** implanta e configura os nós de validador de PoA usando a nossa solução do Azure Resource Manager

4.  **Operador** fornece o ponto de extremidade RPC e gerenciamento para o **Membro do Consortium**

5.  **Membro do Consortium** usa sua chave privada para assinar uma solicitação aceitando os nós de validador **Operador** implantou para participar em seu nome

### <a name="azure-monitor"></a>Azure Monitor

Essa solução também vem com o Azure Monitor para acompanhar as estatísticas de rede e de nó. Para os desenvolvedores de aplicativos, isso fornece visibilidade sobre o blockchain subjacente para acompanhar as estatísticas de geração de bloco. Os operadores de rede podem usar o Azure Monitor para detectar e evitar interrupções de rede por meio de estatísticas de infraestrutura e logs que podem ser consultados rapidamente. Para obter mais informações, consulte [monitoramento de serviço](#service-monitoring).

### <a name="deployment-architecture"></a>Arquitetura de implantação

#### <a name="description"></a>DESCRIÇÃO

Este modelo de solução pode implantar a rede consortium Ethereum de vários membros baseadas em uma ou várias regiões. Por padrão, o RPC e pontos de extremidade de emparelhamento são acessíveis pela IP público para permitir a conectividade simplificada entre assinaturas e nuvens. É recomendável aproveitar os [contratos de permissão da Paridade](https://wiki.parity.io/Permissioning) para controles de acesso de nível de aplicativo. Também damos suporte a redes implantadas atrás de VPNs, que aproveitam os gateways de rede virtual para conectividade entre assinaturas. Essas implantações são mais complexas, portanto, é recomendável iniciar com o modelo de IP público pela primeira vez.

#### <a name="consortium-member-overview"></a>Visão geral do ingresso de membro do consórcio

Cada implantação de membro do consórcio inclui:

-   Máquinas virtuais para executar os validadores de PoA

-   Azure Load Balancer para distribuir RPC, emparelhamento e solicitações de Governança Dapp

-   Azure Key Vault para proteger as identidades do validador

-   Armazenamento do Microsoft Azure para hospedar as informações de rede persistentes e leasing de coordenação

-   Azure Monitor para agregar logs e estatísticas de desempenho

-   Gateway de rede virtual para permitir conexões VPN entre redes virtuais privadas (opcional)

![arquitetura de implantação](./media/ethereum-poa-deployment/deployment-architecture.png)

Podemos aproveitar a contêineres do docker para confiabilidade e modularidade. Usamos o Registro de Contêiner do Azure para hospedar e servir imagens com controle de versão como parte de cada implantação. As imagens de contêiner consistem em:

-   Orchestrator

    -   É executado uma vez durante a implantação

    -   Gera as identidades e contratos de governança

    -   Armazena as identidades no Armazenamento de Identidade

-   Cliente de Paridade

    -   Concede a identidade do Armazenamento de Identidade

    -   Descobre e conecta-se aos pares

-   Agente EthStats

    -   Coleta estatísticas e logs locais via RPC e envia por push para o Azure Monitor

-   DApp de Governança

    -   Interface da web para interagir com os contratos de Governança

## <a name="how-to-guides"></a>Guias de instruções
### <a name="governance-dapp"></a>DApp de Governança

O cerne da prova de autoridade é descentralizado de governança. O DApp de governança é um conjunto de [contratos inteligentes](https://github.com/Azure-Samples/blockchain/tree/master/ethereum-on-azure/) pré-implantados e um aplicativo Web que são usados para controlar as autoridades na rede.
Autoridades são divididas em identidades do administrador e nós de validador.
Os administradores têm a capacidade de delegar a participação de consenso para um conjunto de nós de validador. Os administradores também podem votar outros administradores para dentro ou fora da rede.

![dapp de governança](./media/ethereum-poa-deployment/governance-dapp.png)

-   **Governança descentralizada -** alterações em autoridades de rede são administradas por meio de votação na cadeia pela seleção de administradores.

-   **Delegação de Validador -** autoridades podem gerenciar seus nós de validador que são definidos em cada implantação PoA.

-   **Histórico de Alterações Auditável -** cada alteração será registrada do blockchain fornecendo transparência e a capacidade de auditoria.

#### <a name="getting-started-with-governance"></a>Introdução à governança
Para executar qualquer tipo de transação por meio do DApp de governança, você precisará aproveitar uma carteira de Ethereum.  A abordagem mais simples é usar uma carteira no navegador, como o [MetaMask](https://metamask.io). No entanto, como se trata de contratos inteligentes implantados na rede, você também pode automatizar suas interações com o contrato de Governança.

Depois de instalar o MetaMask, navegue até o DApp de Governança no navegador.  Você pode localizar a URL no email de confirmação de implantação ou por meio do portal do Azure na saída da implantação.  Se você não tiver uma carteira no navegador instalada, não poderá executar nenhuma ação; no entanto, você ainda pode ler o estado do administrador.  

#### <a name="becoming-an-admin"></a>Tornando-se um administrador
Se você for o primeiro membro implantado na rede, você se tornará automaticamente um administrador e seus nós de paridade serão listados como validadores.  Se estiver ingressando na rede, você precisará entrar como administrador por uma maioria (maior que 50%) do conjunto de administradores existente.  Se você optar por não se tornar um Administrador, seus nós ainda sincronizarão e validarão o blockchain. No entanto, eles não participarão do processo de criação de bloco. Para iniciar o processo de votação para tornar-se um Administrador, clique em __Nomear__ e insira seu endereço e alias do Ethereum.

![Nomear](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

#### <a name="candidates"></a>Candidatos
Selecionar a guia __Candidatos__ mostrará a você o conjunto atual de candidatos a administradores.  Depois que um candidato atingir um voto de maioria dos administradores atuais, o candidato será promovido a um administrador.  Para votar em um candidato, selecione a linha e clique em "votar em" na parte superior.  Se mudar de ideia em relação ao voto, selecione o candidato e clique em "Rescindir voto".

![Candidatos](./media/ethereum-poa-deployment/governance-dapp-candidates.png)


#### <a name="admins"></a>Administradores
A guia __Administradores__ mostrará o conjunto atual de Administradores e permitirá que você vote.  Quando um administrador perder mais de 50% de suporte, eles serão removidos como um administrador na rede.  Todos os nós validadores que esse Administrador tiver perderão o status de validador e se tornarão nós de transação na rede.  Um administrador pode ser removido por vários motivos; no entanto, cabe ao consórcio concordar sobre uma política com antecedência.

![Administradores](./media/ethereum-poa-deployment/governance-dapp-admins.png)

#### <a name="validators"></a>Validadores
Selecionar a guia __Validadores__ no menu esquerdo exibirá os nós de Paridade implantados atualmente para essa instância e seus status atuais (tipo de nó).  Cada membro do consórcio terá um conjunto diferente de validadores nesta lista, já que essa exibição representa o membro do consórcio implantado atual.  Se esta for uma instância implantada recentemente e você ainda não tiver adicionado seus validadores, você verá a opção "Adicionar validadores".  Selecionar isso irá escolher automaticamente um conjunto de nós de paridade com balanceamento de região e atribuí-los ao seu conjunto de validadores.  Se você tiver implantado mais nós do que a capacidade permitida, os nós restantes se tornarão nós de transação na rede.

O endereço de cada validador é atribuído automaticamente por meio do [repositório de identidades](#identity-store) no Azure.  Se um nó falhar, ele abrirá mão de sua identidade, permitindo que o outro nó na implantação assuma seu lugar.  Isso garante que sua participação no consenso seja altamente disponível.

![Validadores](./media/ethereum-poa-deployment/governance-dapp-validators.png)

#### <a name="consortium-name"></a>Nome do consórcio
Qualquer Administrador pode atualizar o nome do consórcio, exibido na parte superior da página.  Selecione o ícone de engrenagem na parte superior esquerda para atualizar o nome do consórcio.

#### <a name="account-menu"></a>Menu da conta
No canto superior direito, estão o identicon e o alias da conta do Ethereum.  Se você for um administrador, terá a capacidade de atualizar seu alias.

![Conta](./media/ethereum-poa-deployment/governance-dapp-account.png)

### <a name="deploy-ethereum-proof-of-authority"></a>Implantar prova de autoridade do Ethereum

Aqui está um exemplo de um fluxo de implantação de várias partes:

1.  Três membros geram uma conta de Ethereum usando MetaMask

2.  *Membro A* implanta Ethereum PoA, fornecendo o endereço público do Ethereum

3.  *Membro de A* fornece a URL do consórcio para o *Membro B* e *Membro C*

4.  *Implantação do Membro B* e *Membro C*, Ethereum PoA, fornecendo seu En Público Ethereum e URL de consórcio do *Membro A*

5.  *Membro A* vota na *Membro B* como um administrador

6.  *Membro de A* e *Membro B* ambos os votam *Membro C* como um administrador

Esse processo requer uma assinatura do Azure que possa dar suporte à implantação de vários discos gerenciados e máquinas virtuais. Se necessário, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/) para começar.

Quando uma assinatura for protegida, vá para o portal do Azure. Selecione “+”, Microsoft Azure Marketplace (“Ver todos”) e procure Ethereum PoA Consortium.

A seção a seguir levará você para a configuração do volume do membro na rede. O fluxo de implantação é dividido em cinco etapas: Noções básicas, Regiões de implantação, Tamanho de rede e desempenho, Configurações de Ethereum, Azure Monitor.

#### <a name="basics"></a>Noções básicas

Em **Noções básicas de**, especifique os valores para os parâmetros padrão para qualquer tipo de implantação, como assinatura, o grupo de recursos e propriedades básicas da máquina virtual.

Para obter uma descrição detalhada de cada parâmetro:

Nome do parâmetro|DESCRIÇÃO|Valores permitidos|Valores padrão
---|---|---|---
Criar uma nova rede ou ingressar em uma rede existente?|Criar uma nova rede ou ingressar em uma rede de consortium pré-existente|Criar Nova Ingressar em Existente|Criar Novo
Endereço de Email (opcional)|Você receberá uma notificação por email quando sua implantação for concluída com informações sobre sua implantação.|Endereço de email inválido|ND
Nome de usuário da VM|Nome de usuário do administrador de cada VM implantada (somente caracteres alfanuméricos)|1 a 64 caracteres|ND
Tipo de autenticação|O método para autenticar para a máquina virtual.|Senha ou chave pública SSH|Senha
Senha (Tipo de Autenticação = Senha)|A senha para a conta de administrador para cada uma das máquinas virtuais implantadas.  A senha deve conter 3 dos seguintes: 1 caractere maiúsculo, 1 caractere minúsculo, 1 número e 1 caractere especial. Embora todas as VMs tenham inicialmente a mesma senha, você pode alterar a senha após o fornecimento.|12 a 72 caracteres|ND
Chave SSH (tipo de autenticação = chave pública)|A chave do secure shell usada para logon remoto.||ND
Assinatura|A assinatura para a qual deseja implantar a rede consortium||ND
grupo de recursos|O grupo de recursos para a qual deseja implantar a rede consortium.||ND
Local padrão|A região do Azure para o grupo de recursos.||ND

Uma implantação de exemplo é mostrada abaixo: ![folha básica](./media/ethereum-poa-deployment/basic-blade.png)

#### <a name="deployment-regions"></a>Regiões de implantação

Em seguida, em Regiões de implantação, especificar entradas para Número de regiões para implantar a rede consortium e seleção de regiões do Azure com base no número de regiões fornecido. O usuário pode implantar no máximo 5 regiões. É recomendável escolher a primeira região para coincidir com o local do grupo de recursos na seção Noções básicas. Para redes de teste ou desenvolvimento, recomenda-se uma única região por membro. Para a produção, é recomendável implantar em duas ou mais regiões para alta disponibilidade.

Para obter uma descrição detalhada de cada parâmetro:

  Nome do parâmetro|DESCRIÇÃO|Valores permitidos|Valores padrão
  ---|---|---|---
  Número de regiões|Número de regiões para implantar a rede consortium|1, 2, 3, 4, 5|1
  Primeira região|Primeira região para implantar a rede consortium|Todas as regiões permitidas do Azure|ND
  Segunda região|Segunda região para implantar a rede consortium (visível somente quando o número de regiões é selecionado como 2)|Todas as regiões permitidas do Azure|ND
  Terceira região|Terceira região para implantar a rede consortium (visível somente quando o número de regiões é selecionado como 3)|Todas as regiões permitidas do Azure|ND
  Quarta região|Quarta região para implantar a rede consortium (visível somente quando o número de regiões é selecionado como 4)|Todas as regiões permitidas do Azure|ND
  Quinta região|Quinta região para implantar a rede consortium (visível somente quando o número de regiões é selecionado como 5)|Todas as regiões permitidas do Azure|ND

Uma implantação de exemplo é mostrada abaixo: ![regiões de implantação](./media/ethereum-poa-deployment/deployment-regions.png)

#### <a name="network-size-and-performance"></a>Tamanho e desempenho da rede

Em seguida, em “Tamanho e desempenho de rede”, especifique as entradas para o tamanho da rede do consórcio, como número e tamanho de nós.
O tamanho de armazenamento do nó de validador ditará o tamanho potencial do blockchain. Isso pode ser alterado após a implantação.

Para obter uma descrição detalhada de cada parâmetro:

  Nome do parâmetro|DESCRIÇÃO|Valores permitidos|Valores padrão
  ---|---|---|---
  Número de nós do validador balanceador de carga|O número de nós do validador para provisionar como parte da rede|2-15|2
  Desempenho do armazenamento de nó do validador|Tipo de disco gerenciado de suporte de cada um de nós de mineração implantado.|SSD Standard ou Premium|SSD Standard
  Tamanho da máquina virtual do nó de mineração|O tamanho de máquina virtual usado para nós de mineração.|Série Standard A, Standard D, Standard D-v2, Standard F, Standard DS e Standard FS|Standard D1 V2

[Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/managed-disks/)

[Detalhes de preços de máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

A máquina virtual e a camada de armazenamento afetarão o desempenho da rede.  Recomendamos os seguintes SKUs com base no custo-benefício desejado:

  SKU da máquina virtual|Camada de armazenamento|Preço|Produtividade|Latência
  ---|---|---|---|---
  F1|SSD Standard|baixa|baixa|alto
  D2_v3|SSD Standard|média|média|média
  F16s|SSD Premium|alto|alto|baixa

Uma implantação de exemplo é mostrada abaixo: ![tamanho e desempenho de rede](./media/ethereum-poa-deployment/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Configurações de Ethereum

Em seguida, em Configurações de Ethereum, especifique as configurações Ethereum relacionadas, como a ID de rede e bloco gênese ou senha da conta Ethereum.

Para obter uma descrição detalhada de cada parâmetro:

  Nome do parâmetro|DESCRIÇÃO|Valores permitidos|Valores padrão
  ---|---|---|---
ID do Membro do Consortium|A ID associada a cada membro que está participando da rede consortium usada para configurar os espaços de endereço IP para evitar a colisão. No ao de rede privada, a ID do membro deve ser exclusiva em diferentes organizações na mesma rede.  É necessária uma ID de membro exclusiva mesmo quando a mesma organização implanta em várias regiões. Anote o valor desse parâmetro, pois você precisará compartilhá-lo com outros membros de ingresso para garantir que não haja colisão.|0-255|ND
ID da Rede|A ID de rede para a rede de Ethereum consortium que está sendo implantada.  Cada rede Ethereum tem sua própria ID de rede, sendo que 1 indica a ID para a rede pública.|5 - 999.999.999|10101010
Endereço do administrador Ethereum|Endereço de conta Ethereum que é usado para a participação na governança PoA.  É recomendável usar MetaMask para gerar um endereço de Ethereum.|42 caracteres alfanuméricos começando com 0x|ND
Opções avançadas|Opções avançadas para configurações do Ethereum|Habilitar ou Desabilitar|Desabilitar
IP público (opções avançadas = habilitar)|Implanta a rede por trás de um Gateway de rede virtual e remove o acesso de emparelhamento. Se essa opção for selecionada, todos os membros devem usar um Gateway de rede virtual para a conexão para ser compatível.|Rede virtual privada de IP público|IP público
Limite de gás do bloco (opções avançadas = habilitar)|O limite inicial de gás do bloco da rede|Qualquer numérico|50000000
Período para selar o bloco novamente (s)|A frequência com que serão criados blocos vazios quando não existirem transações na rede. Uma frequência superior terá uma finalidade mais rápida, mas os custos de armazenamento serão maiores.|Qualquer numérico|15
Contrato de permissão de transação (opções avançadas = habilitar)|Código de bytes para o contrato de permissão de transação. Restringe a implantação e a execução do contrato inteligente a uma lista permitida de contas do Ethereum.|Código de bytes do contrato|ND

Uma implantação de exemplo é mostrada abaixo: ![configurações do ethereum](./media/ethereum-poa-deployment/ethereum-settings.png)

#### <a name="monitoring"></a>Monitoramento

A folha monitoramento permite que você configure um recurso de logs de Azure Monitor para sua rede. O agente de monitoramento coletará e fornecerá métricas e logs úteis da rede, oferecendo a capacidade de verificar a integridade da rede ou de depurar problemas rapidamente.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

  Nome do parâmetro|DESCRIÇÃO|Valores permitidos|Valores padrão
  ---|---|---|---
Monitoramento|Opção para habilitar o monitoramento|Habilitar ou Desabilitar|Habilitar
Conectar-se a logs de Azure Monitor existentes|Criar uma nova instância de logs de Azure Monitor ou ingressar em uma instância existente|Criar nova ou ingresar em existente|Criar Novo
Local do monitor (conectar a logs de Azure Monitor existentes = criar novo)|A região em que a nova instância de logs de Azure Monitor será implantada|Todas as regiões de logs de Azure Monitor|ND
ID do espaço de trabalho do log Analytics existente (conectar a logs de Azure Monitor existentes = ingressar existente)|ID do espaço de trabalho da instância existente de logs de Azure Monitor||ND
Chave primária do log Analytics existente (conectar a logs de Azure Monitor existentes = ingressar existente)|A chave primária usada para se conectar à instância existente de logs de Azure Monitor||ND


Uma implantação de exemplo é mostrada abaixo: ![folha básica](./media/ethereum-poa-deployment/azure-monitor.png)

#### <a name="summary"></a>Resumo

Clique na folha de resumo para revisar as entradas especificadas e executar uma validação básica antes da implantação. Antes de implantar, você pode baixar o modelo e parâmetros.

Revise os termos legais e de privacidade e clique em Compra para implantar. Se a implantação incluir gateways de rede virtual, a implantação será levar de 45 a 50 minutos.

#### <a name="post-deployment"></a>Pós-implantação

##### <a name="deployment-output"></a>Saída de implantação

Depois que a implantação for concluída, você poderá acessar os parâmetros necessários por meio do email de confirmação ou por meio do portal do Azure. Nesses parâmetros você encontrará:

-   Ponto de extremidade RPC do Ethereum

-   URL do painel de governança

-   URL do Azure Monitor

-   URL de dados

-   ID do recurso do gateway da VNET (opcional)

##### <a name="confirmation-email"></a>Email de confirmação

Se você fornecer um endereço de email ([seção Noções básicas](#basics)), um email será enviado ao endereço de email com as informações de saída de implantação.

![email de implantação](./media/ethereum-poa-deployment/deployment-email.png)

##### <a name="portal"></a>Portal

Depois que a implantação for concluída com êxito e todos os recursos tiverem sido provisionados, você poderá exibir os parâmetros de saída em seu grupo de recursos.

1.  Localize o grupo de recursos no portal

2.  Navegue para *Implantações*

3.  Selecione a implantação superior com o mesmo nome que seu grupo de recursos

4.  Selecione *Saídas*

### <a name="growing-the-consortium"></a>Aumentando o consórcio

Para expandir seu consórcio, primeiro você deve se conectar a rede física.
Usando a implantação com base em IP público a primeira etapa é perfeita. Se estiver implantando por trás de uma VPN, consulte a seção [conectando o gateway de VNet](#connecting-vnet-gateways) para fazer a conexão de rede como parte da implantação do novo membro.  Após a conclusão da implantação, use o [DApp de Governança](#governance-dapp) para tornar-se um Administrador da rede.

#### <a name="new-member-deployment"></a>Nova implantação de membro

1.  Compartilhe as informações a seguir com o membro da junção. Essas informações podem ser encontradas em seu email de pós-implantação ou na saída de implantação do portal.

    -  URL de dados do Consortium

    -  O número de nós que você implantou

    -  ID do recurso do gateway da VNET (no caso de uso da VPN)

2.  O membro de implantação deve usar a [mesma solução](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) ao implantar sua presença de rede com tendo em mente o seguinte:

    -  Selecione *Ingressar Existente*

    -  Escolha o mesmo número de nós de validador, como o restante dos membros na rede para garantir a representação satisfatória

    -  Use o mesmo endereço do Ethereum fornecido na etapa anterior

    -  Passar na *URL de dados do Consortium* sobre a guia de *configurações do Ethereum*

    -  Se o restante da rede estiver atrás de uma VPN, selecione *VNet privada* na seção avançada

#### <a name="connecting-vnet-gateways"></a>Conectar-se a gateways de rede virtual

Você poderá ignorar esta etapa se tiver implantado usando as configurações de IP público padrão. No caso de uma rede privada, os membros diferentes são conectados por meio de conexões de gateway de rede virtual. Antes que um membro possa ingressar na rede e ver o tráfego da transação, um membro existente deve fazer uma configuração final em seu gateway de VPN para aceitar a conexão. Isso significa que os nós Ethereum do membro de junção não serão executados até que uma conexão seja estabelecida. É recomendável criar conexões de rede redundantes (malha) no consórcio para reduzir as chances de um único ponto de falha.

Depois que o novo membro implanta, o membro existente deve concluir a conexão bi-direcional, configurando uma conexão de gateway de rede virtual para o novo membro. Para fazer isso, o membro existente será necessário:

1.  A ResourceID do gateway da VNET do membro que está se conectando (confira a saída da implantação)

2.  A chave de conexão compartilhada

O membro existente deve executar o seguinte script do PowerShell para concluir a conexão. É recomendável usar Azure Cloud Shell localizado na barra de navegação superior direita no Portal.

![cloud shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

### <a name="service-monitoring"></a>Monitoramento do serviço

Você pode localizar seu portal do Azure Monitor por seguir o link no email de implantação ou localizar o parâmetro na saída de implantação \[OMS\_PORTAL\_URL\].

O portal exibirá primeiro estatísticas de rede de alto nível como uma visão geral.

![categorias de monitor](./media/ethereum-poa-deployment/monitor-categories.png)

Selecionar a **Visão geral de nó** direcionará você para um portal para exibir estatísticas por nó.

![status do nó](./media/ethereum-poa-deployment/node-stats.png)

Selecionar **Estatísticas de rede** direcionará você para exibir estatísticas de rede Ethereum.

![estatísticas de rede](./media/ethereum-poa-deployment/network-stats.png)

#### <a name="sample-kusto-queries"></a>Consultas Kusto de exemplo

Por trás desses painéis está um conjunto de logs brutos que podem ser consultados. Você pode usar esses logs brutos para personalizar os painéis, investigar falhas ou limitar o alerta de configuração. Abaixo você encontrará um conjunto de consultas de exemplo que pode ser executado na ferramenta de pesquisa de Log:

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>Lista de blocos que foram relatados por mais de um validador. É útil para ajudar a localizar as bifurcações de cadeia.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>Obter uma contagem de pares média para um nó do validador especificado em média em bukets de 5 minutos.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

### <a name="ssh-access"></a>Acesso SSH

Por motivos de segurança, o acesso de porta SSH é negado por uma regra de segurança de grupo de rede por padrão. Para acessar as instâncias de máquina virtual na rede PoA, você precisará alterar essa regra para \"permitir\"

1.  Comece na seção Visão geral do grupo de recursos implantados do portal do Azure.

    ![visão geral ssh](./media/ethereum-poa-deployment/ssh-overview.png)

2.  Selecione o Grupo de Segurança de Rede da região da VM que você deseja acessar

    ![ssh nsg](./media/ethereum-poa-deployment/ssh-nsg.png)

3.  Selecione a regra \"permitir-ssh\"

    ![ssh-permitir](./media/ethereum-poa-deployment/ssh-allow.png)

4.  Alterar \"Ação\" para permitir

    ![habilitar permissão ssh](./media/ethereum-poa-deployment/ssh-enable-allow.png)

5.  Clique em \"Salvar\" (as alterações podem levar alguns minutos para serem aplicadas)

Você pode agora se conectar remotamente às máquinas virtuais para os nós de transação via SSH com seu nome de usuário admin e senha/chave SSH fornecidos.
O comando SSH de execução para acessar o primeiro nó do validador está listado no parâmetro de saída da implantação de modelo como 'SSH\_TO\_FIRST\_VL\_NODE\_REGION1' (para a implantação de exemplo: ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com). Para obter nós de transações adicionais, incremente o número da porta em um (por exemplo, o primeiro nó de transação está na porta 4000).

Se você implantou a mais de uma região, altere o comando acima para o nome DNS ou endereço IP do balanceador de carga nessa região. Para encontrar o nome DNS ou endereço IP de outras regiões, localize o recurso com a convenção de nomenclatura\*\*\*\*\*-lbpip-reg\#, e seu nome DNS e as propriedades do endereço IP.

### <a name="azure-traffic-manager-load-balancing"></a>Balanceamento de carga do Gerenciador de Tráfego

O Gerenciador de Tráfego do Azure ajuda a reduzir o tempo de inatividade e a melhorar a capacidade de resposta da rede PoA roteando o tráfego de entrada em várias implantações em diferentes regiões. A verificação de integridade interna e o reencaminhamento automático ajudam a garantir a alta disponibilidade de pontos de extremidade RPC e a governança DApp. Esse recurso é útil se você tiver implantado em várias regiões e estiver pronto para produção.

Use o Gerenciador de Tráfego para:

-   Melhore a disponibilidade de rede PoA com failover automático.

-   Aumente sua capacidade de resposta de redes pelo roteamento de usuários finais para o local do Azure com a menor latência de rede.

Se você optar por criar um perfil do Gerenciador de Tráfego do Microsoft Azure, você pode usar o nome DNS do perfil para acesso à sua rede. Uma vez que outros membros do consórcio foram adicionados à rede, o Gerenciador de Tráfego do Microsoft Azure também pode ser usado para balancear a carga entre seus validadores implantados.

#### <a name="creating-a-traffic-manager-profile"></a>Criando perfil do Gerenciador de Tráfego

Pesquise e selecione \"perfil do Gerenciador de Tráfego\" depois de clicar no botão \"Criar um recurso\" no portal do Azure.

![pesquise o gerenciador de tráfego do azure](./media/ethereum-poa-deployment/traffic-manager-search.png)

Atribua o perfil de um nome exclusivo e selecione o grupo de recursos que foi criado durante a implantação de PoA. Clique no botão "Criar" para implantar.

![criar gerenciador de tráfego](./media/ethereum-poa-deployment/traffic-manager-create.png)

Depois de implantado, selecione a instância no grupo de recursos. O nome DNS para acessar o gerenciador de tráfego pode ser encontrado na guia Visão geral

![Localize o Gerenciador de tráfego DNS](./media/ethereum-poa-deployment/traffic-manager-dns.png)

Selecione a guia Pontos de extremidade e clique no botão Adicionar. Forneça um nome exclusivo para o ponto de extremidade. Altere o tipo de recurso de destino para Endereço IP público. Em seguida, selecione o endereço IP público da primeira região\'balanceador de carga de s.

![Roteamento do gerenciador de tráfego](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Repita para cada região na rede implantada. Depois que os pontos de extremidade estiverem no status de\" \"habilitado, eles serão carregados automaticamente e a região será balanceada no nome DNS do Gerenciador de tráfego. Agora você pode usar esse nome DNS, em vez do parâmetro \[CONSORTIUM\_DATA\_URL\] nas outras etapas do documento.

### <a name="data-api"></a>API de dados

Cada membro do consórcio hospeda as informações necessárias para que outras pessoas se conectem à rede. O membro existente fornecerá o [CONSORTIUM_DATA_URL] antes da implantação do membro. Após a implantação, um membro de junção irá recuperar informações da interface do JSON no ponto de extremidade seguir:

`<CONSORTIUM_DATA_URL>/networkinfo`

A resposta conterá informações úteis para ingressar Membros (bloco Genesis, ABI do contrato de conjunto de validadores, bootnodes) e informações úteis para o membro existente (endereços do validador). Recomendamos o uso de padronização para estender o consórcio entre provedores na nuvem. Essa API retornará uma resposta JSON formatado com a seguinte estrutura:
```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```
## <a name="tutorials"></a>Tutoriais

### <a name="programmatically-interacting-with-a-smart-contract"></a>Interagir programaticamente com um contrato inteligente

> [!WARNING]
> Nunca envie sua chave privada do Ethereum pela rede! Certifique-se de que cada transação é assinada localmente primeiro e a transação com sinal é enviada pela rede.

No exemplo a seguir, usamos *ethereumjs-wallet* para gerar um endereço de Ethereum, *ethereumjs-tx* para fazer logon localmente, e *web3* para enviar a transação bruta para o Ponto de extremidade RPC Ethereum.

Usaremos esse contrato inteligente de Olá, mundo simples para este exemplo:

```javascript
pragma solidity ^0.4.11;
contract postBox {
    string message;
    function postMsg(string text) public {
        message = text;
    }
    function getMsg() public view returns (string) {
        return message;
    }
}
```

Este exemplo supõe que o contrato já foi implantado. Você pode usar *solc* e *web3* para a implantação de um contrato por meio de programação. Em primeiro lugar, instale os seguintes módulos de nó:
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
Este script nodeJS executará o seguinte:

-   Construir uma transação bruta: postMsg

-   Inscrever a transação usando a chave privada gerada

-   Enviar a transação conectada à rede Ethereum

```javascript
var ethereumjs = require('ethereumjs-tx')
var wallet = require('ethereumjs-wallet')
var Web3 = require('web3')

// TODO Replace with your contract address
var address = "0xfe53559f5f7a77125039a993e8d5d9c2901edc58";
var abi = [{"constant": false,"inputs": [{"name": "text","type": "string"}],"name": "postMsg","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": true,"inputs": [],"name": "getMsg","outputs": [{"name": "","type": "string"}],"payable": false,"stateMutability": "view","type": "function"}];

// Generate a new Ethereum account
var account = wallet.generate();
var accountAddress = account.getAddressString()
var privateKey = account.getPrivateKey();

// TODO Replace with your RPC endpoint
var web3 = new Web3(new Web3.providers.HttpProvider(
    "http://testzvdky-dns-reg1.eastus.cloudapp.azure.com:8545"));

// Get the current nonce of the account
web3.eth.getTransactionCount(accountAddress, function (err, nonce) {
   var data = web3.eth.contract(abi).at(address).postMsg.getData("Hello World");
   var rawTx = {
     nonce: nonce,
     gasPrice: '0x00',
     gasLimit: '0x2FAF080',
     to: address,
     value: '0x00',
     data: data
   }
   var tx = new ethereumjs(rawTx);

   tx.sign(privateKey);

   var raw = '0x' + tx.serialize().toString('hex');
   web3.eth.sendRawTransaction(raw, function (txErr, transactionHash) {
     console.log("TX Hash: " + transactionHash);
     console.log("Error: " + txErr);
   });
 });
```

### <a name="deploy-smart-contract-with-truffle"></a>Implantar contrato inteligente com o Truffle

-   Instalar as bibliotecas necessárias

```javascript
npm init

npm install truffle-hdwallet-provider --save
```
-   No truffle.js, adicione o seguinte código para desbloquear sua conta do MetaMask e configurar o nó de PoA como ponto de entrada fornecendo a expressão mnemônica (MetaMask/Settings/Reveal Seed Words)

```javascript
var HDWalletProvider = require("truffle-hdwallet-provider");

var rpc_endpoint = "XXXXXX";
var mnemonic = "twelve words you can find in metamask/settings/reveal seed words";

module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    poa: {
      provider: new HDWalletProvider(mnemonic, rpc_endpoint),
      network_id: 3,
      gasPrice : 0
    }
  }
};

```

-   Implantar na rede de PoA

```javascript
$ truffle migrate --network poa
```

### <a name="debug-smart-contract-with-truffle"></a>Depurar o contrato inteligente com o Truffle

Truffle tem uma rede de desenvolvimento local que está disponível para depuração inteligente do contrato. Você pode encontrar o tutorial completo [aqui](https://truffleframework.com/tutorials/debugging-a-smart-contract).

### <a name="webassembly-wasm-support"></a>Suporte de WebAssembly (WASM)

O Suporte de WebAssembly já está habilitado para você em redes de PoA implantados recentemente. Ele permite desenvolvimento inteligente contrato em qualquer linguagem que transpila ao Assembly de Web (Rust, C, C++). Veja os links abaixo para obter informações adicionais

-   Visão geral de paridade de WebAssembly- <https://wiki.parity.io/WebAssembly-Home>

-   Tutorial da paridade Tech - <https://github.com/paritytech/pwasm-tutorial>

## <a name="reference"></a>Referência

### <a name="faq"></a>FAQ

#### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Observei que há muitas transações na rede que eu não enviei. De onde elas vêm?

Não é seguro para desbloquear a [API pessoal](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html). Os bots ouvem as contas do Ethereum desbloqueadas e tentam drenar os fundos. O bot pressupõe que essas contas contêm real e tentativa pra ser o primeiro a extrair o saldo. Não habilite a API pessoal na rede. Nesse caso, assine previamente as transações manualmente usando uma carteira, como o MetaMask, ou de forma programática, conforme descrito na seção [Interagir de forma programática com um contrato inteligente](#programmatically-interacting-with-a-smart-contract).

#### <a name="how-to-ssh-onto-a-vm"></a>Como o SSH em uma VM?

A porta SSH não é exposta por motivos de segurança. Siga [este guia para habilitar a porta SSH](#ssh-access).

#### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Como configurar um nó de transação ou membro de auditoria?

Nós de transação são um conjunto de clientes de paridade que emparelhadas com a rede, mas não fazem parte de consenso. Esses nós ainda podem ser usados para enviar transações Ethereum e ler o estado de contrato inteligente.
Isso funciona bem como um mecanismo para fornecer capacidade de auditoria para membros do consórcio que não são autoridades na rede. Para fazer isso simplesmente siga a Etapa 2 de um consórcio de crescimento.

#### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Por que as transações de MetaMask estão demorando muito tempo?

Para garantir que as transações são recebidas na ordem correta, cada transação Ethereum vem com um incremento nonce. Se você já usou uma conta no MetaMask em uma rede diferente, você precisará redefinir o valor do nonce. Clique no ícone de configurações (3 barras), Configurações, Redefinir conta. O histórico de transações será limpo e agora você pode reenviar a transação.

#### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>É necessário especificar o valor de gás no MetaMask?

O Ether não tem nenhuma finalidade no consórcio de prova de autoridade. Portanto, não é necessário especificar o valor de gás ao enviar transações no MetaMask.

#### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>O que deverei fazer se minha implantação falhar devido à falha de provisionamento do Azure OMS?

O monitoramento é um recurso opcional. Em alguns casos raros em que a implantação falhará devido à incapacidade de provisionar recursos do Azure Monitor com êxito, você poderá reimplantar sem o Azure Monitor.

#### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>As implantações de IP público são compatíveis com as implantações de rede privada?

Não, o emparelhamento requer comunicação bidirecional para que toda a rede seja pública ou privada.

#### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Qual é a taxa de transferência de transação esperada da prova de autoridade?

A taxa de transferência de transação será altamente dependente dos tipos de transações e da topologia de rede.  Usando transações simples, obtivemos um parâmetro de comparação de 400 transações por segundo com uma rede implantada em várias regiões.

#### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Como assinar eventos do contrato inteligente?

Agora, a prova de autoridade do Ethereum dá suporte a soquetes da Web.  Verifique seu email de implantação ou a saída da implantação para localizar a porta e a URL do soquete da Web.

## <a name="next-steps"></a>Próximas etapas

Introdução ao uso da solução [Consortium Ethereum de prova de autoridade](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium).
