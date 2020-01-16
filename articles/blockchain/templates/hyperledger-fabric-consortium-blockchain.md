---
title: Implantar o modelo de solução do consórcio de malha de multilimiar no Azure
description: Como implantar e configurar o modelo de solução de rede do consórcio Fabric Consortium no Azure
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: caleteet
ms.openlocfilehash: 3e7dcd3cdcfa636c0b23ac6643bd7732e7f8ada0
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029153"
---
# <a name="hyperledger-fabric-consortium-network"></a>Rede de consórcio Hyperledger Fabric

Você pode usar o modelo de solução do consórcio Hyperledger Fabric para implantar e configurar uma rede de consórcio Hyperledger Fabric no Azure.

> [!IMPORTANT]
> A [malha](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-hyperledger-fabric) do modelo de infraestrutura do Azure será preterida. Em vez disso, use a [malha do superrazão no serviço kubernetes do Azure](hyperledger-fabric-consortium-azure-kubernetes-service.md) .  

Depois de ler este artigo, você irá:

- Obter conhecimento prático de blockchain, Hyperledger Fabric e arquiteturas de rede de consórcio mais complexas
- Saiba como implantar e configurar uma rede de consórcio Hyperledger Fabric no portal do Azure

## <a name="about-blockchain"></a>Sobre o blockchain

Se você é novo na comunidade blockchain, esse modelo de solução é uma ótima oportunidade para aprender sobre a tecnologia de maneira fácil e configurável no Azure. Blockchain é a tecnologia por trás do Bitcoin; no entanto, é muito mais do que apenas um ativador de uma moeda virtual. É uma combinação de banco de dados existente, sistema distribuído e tecnologias criptográficas que permitem a computação segura de vários participantes com garantias em relação à imutabilidade, verificabilidade, capacidade de auditoria e resiliência ao ataque. Diferentes protocolos empregam mecanismos diferentes para fornecer esses atributos. [Malha Hyperledger](https://github.com/hyperledger/fabric) é um protocolo de tal.

## <a name="consortium-architecture-on-azure"></a>Arquitetura do consórcio no Azure

Para habilitar o Hyperledger Fabric no Azure, há dois tipos principais de implantação com suporte. Essas implantações foram projetadas para acomodar diferentes topologias, com base no destino desejado.

- **Máquina virtual individual, servidor de desenvolvedor** - esse tipo de implantação foi projetado como um ambiente de desenvolvimento usado para compilar e testar soluções criadas no Hyperledger Fabric.
- **Várias máquinas virtuais, implantação expansível** - esse tipo de implantação foi projetado para ambientes que modelam um consórcio de participantes diferentes, aproveitando um ambiente compartilhado.

Em qualquer implantação, os blocos de construção que formam o núcleo do Hyperledger Fabric são os mesmos.  As diferenças nas implantações são como esses componentes são dimensionados.

- **Nós de CA**: um nó que executa uma autoridade de certificação que é usada para gerar certificados que são usados para identidades na rede.
- **Nós do solicitante**: um nó que executa o serviço de comunicação implementando uma garantia de entrega, como a transmissão de ordem total ou transações atômicas.
- **Nós pares**: um nó que confirma as transações e mantém o estado e uma cópia do razão distribuído.
- **Nós CouchDB**: um nó que pode executar o serviço CouchDB que pode manter o banco de dados de estado e fornecer consultas avançadas de data chaincode, expandindo de chave/valor simples para o armazenamento de tipo JSON.

### <a name="single-virtual-machine-architecture"></a>Arquitetura de máquina virtual individual

Conforme mencionado anteriormente a arquitetura de máquina virtual individual foi criada para que os desenvolvedores tenham um servidor de baixo volume que é usado para desenvolver aplicativos. Todos os contêineres mostrados estão em execução em uma única máquina virtual. O serviço de ordenação está usando [SOLO](https://github.com/hyperledger/fabric/tree/master/orderer) para essa configuração. Essa configuração *não* é um serviço de ordenação tolerante a falhas, mas foi projetado para ser leve para fins de desenvolvimento.

![Arquitetura de máquina virtual individual](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>Arquitetura de várias máquinas virtuais

A arquitetura de várias máquinas virtuais expansível, foi concebida para ter alta disponibilidade e capacidade de dimensionamento de cada componente no núcleo. Essa arquitetura é muito mais adequada para implantações de produção.

![Arquitetura de várias máquinas virtuais](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>Introdução

Para começar, você precisa de uma assinatura do Azure que possa suportar a implantação de várias máquinas virtuais e contas de armazenamento padrão. Se você não tiver uma assinatura do Azure, poderá [ criar uma conta gratuita do Azure ](https://azure.microsoft.com/free/).

Depois de ter uma assinatura, acesse o [ Portal do Azure ](https://portal.azure.com). Selecione **Criar um recurso > Blockchain > Consórcio Hyperledger Fabric**.

![Modelo de mercado de blockchain de membro único de tecido Hyperledger](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Implantação

No modelo do **Consórcio Hyperledger Fabric**, selecione **Criar**.

A implantação de modelos orientará você na configuração da rede [Hyperledger 1.3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/) de vários nós. O fluxo de implantação é dividido em quatro etapas: Noções básicas, configurações de rede do consórcio, configuração de malha e componentes opcionais.

### <a name="basics"></a>Noções básicas

Em **Noções básicas**, especifique valores para parâmetros padrão para qualquer implantação. Tais como, assinatura, grupo de recursos e propriedades básicas da máquina virtual.

![Noções básicas](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| Nome do parâmetro | Description | Valores permitidos |
|---|---|---|
**Prefixo do recurso** | Prefixo do nome para os recursos provisionados como parte da implantação |6 caracteres ou menos |
**Nome de usuário** | O nome de usuário do administrador de cada uma das máquinas virtuais implementadas para esse membro |1 a 64 caracteres |
**Tipo de autenticação** | O método para autenticar na máquina virtual |Senha ou chave pública SSH|
**Senha (Tipo de Autenticação = Senha)** |A senha para a conta de administrador para cada uma das máquinas virtuais implantadas. A senha deve conter três dos seguintes tipos de caracteres: 1 caractere maiúsculo, 1 caractere minúsculo, 1 número e 1 caractere especial<br /><br />Embora todas as VMs tenham inicialmente a mesma senha, você pode alterar a senha após o provisionamento|12 a 72 caracteres|
**Chave SSH (tipo de autenticação = chave SSH pública)** |A chave de shell segura usada para logon remoto ||
**Assinatura** |A assinatura para a qual implantar ||
**Grupo de recursos** |O grupo de recursos no qual será implantada a rede do consórcio ||
**Localidade** |A região do Azure na qual será implantada o primeiro membro ||

Selecione **OK**.

### <a name="consortium-network-settings"></a>Configurações de rede do consórcio

Em **Configurações de rede**, especifique entradas para criar ou ingressar em uma rede de consórcio existente e defina as configurações de sua organização.

![Configurações de rede do consórcio](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| Nome do parâmetro | Description | Valores permitidos |
|---|---|---|
**Configuração de rede** |Você pode optar por criar uma nova rede ou ingressar em uma existente. Se você escolher *Ingressar na existente*, você precisa fornecer valores adicionais. |Nova rede <br/> Ingressar na existente |
**Senha de AC de HLF** |Uma senha usada para certificados gerados por autoridades de certificação que são criadas como parte da implantação. A senha deve conter três dos seguintes tipos de caracteres: 1 caractere maiúsculo, 1 caractere minúsculo, 1 número e 1 caractere especial.<br /><br />Embora todas as máquinas virtuais tenham inicialmente a mesma senha, você pode alterar a senha após o provisionamento.|1 a 25 caracteres |
**Configuração da organização** |Você pode personalizar o nome da sua organização e o certificado ou usar os valores padrão.|Padrão <br/> Avançado |
**Configurações de rede VPN** | Provisionar um gateway de túnel VPN para acessar as VMs | Sim <br/> Não |

Selecione **OK**.

### <a name="fabric-specific-settings"></a>Configurações específicas de malha

Em **Configuração da malha**, configure o tamanho da rede e o desempenho e especifique as entradas para a disponibilidade da rede. Por exemplo, número de nós pares e de nós do comprador, mecanismo de persistência usado por cada nó e o tamanho da VM.

![Configurações de tecido](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| Nome do parâmetro | Description | Valores permitidos |
|---|---|---|
**Tipo de escala** |O tipo de implantação de uma única máquina virtual com vários contêineres ou várias máquinas virtuais em um modelo de expansão.|VM individual ou várias VMs |
**Tipo de disco da VM** |O tipo de armazenamento que suporta cada um dos nós implementados. <br/> Para saber mais sobre os tipos de disco disponíveis, acesse [escolher um tipo de disco](../../virtual-machines/windows/disks-types.md).|SSD Standard <br/> SSD Premium |

### <a name="multiple-vm-deployment-additional-settings"></a>Implantação de várias VMs (configurações adicionais)

![Configurações de malha para implantações de várias VMs](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| Nome do parâmetro | Description | Valores permitidos |
|---|---|---|
**Número de nós do comprador** |O número de nós que ordenam (organizam) as transações em um bloco. <br />Para obter detalhes adicionais sobre o serviço de pedidos, visite a [documentação](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) do Hyperledger |1-4 |
**Tamanho da máquina virtual do nó do comprador** |O tamanho da máquina virtual usada para os nós do comprador na rede|Standard Bs,<br />Standard Ds,<br />Standard FS |
**Número de nós pares** | Nós que são de propriedade de membros do consórcio que executam transações e mantêm o estado e uma cópia do ledger.<br />Para obter detalhes adicionais sobre o serviço de pedidos, visite a [documentação do Hyperledger ](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|1-4 |
**Persistência de estado do nó** |O mecanismo de persistência usado pelos nós pares. Você pode configurar esse mecanismo por nó par. Confira os detalhes abaixo para vários nós pares.|CouchDB <br />LevelDB |
**Tamanho da máquina virtual do nó par** |O tamanho da máquina virtual usado para todos os nós na rede|Standard Bs,<br />Standard Ds,<br />Standard FS |

### <a name="multiple-peer-node-configuration"></a>Configuração de vários nós pares

Este modelo permite escolher seu mecanismo de persistência por nó par. Por exemplo, se você tiver três nós pares você pode usar CouchDB em um e LevelDB nos outros dois.

![Configuração de vários nós pares](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

Selecione **OK**.

### <a name="deploy"></a>Implantação

No **Resumo**, revise as entradas especificadas e execute a validação básica de pré-implantação.

![Resumo](./media/hyperledger-fabric-consortium-blockchain/summary.png)

Revise os termos legais e de privacidade e selecione **Comprar** para implantar. Dependendo do número de VMs provisionadas, o tempo de implantação pode variar de alguns minutos a dezenas de minutos.

## <a name="next-steps"></a>Próximos passos

Agora você está pronto para se concentrar no desenvolvimento de aplicações e chaincode em sua rede blockchain de consórcio Hyperledger.
