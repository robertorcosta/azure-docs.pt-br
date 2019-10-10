---
title: Planejar seu sistema Avere vFXT – Azure
description: Explica o planejamento a ser feito antes de implantar o Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 1317e900fd4448ded046ffea481313f8ea9f68e3
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256242"
---
# <a name="plan-your-avere-vfxt-system"></a>Planejar seu sistema Avere vFXT

Este artigo explica como planejar um novo avere vFXT para o cluster do Azure que está posicionado e dimensionado adequadamente para suas necessidades. 

Antes de passar para o Azure Marketplace ou criar qualquer VM, considere como o cluster vai interagir com outros elementos no Azure. Planeje o local em que os recursos de cluster estarão localizados em sua rede privada e sub-redes e decida qual será seu armazenamento de back-end. Certifique-se de que os nós de cluster que você cria sejam eficientes o suficiente para dar suporte ao seu fluxo de trabalho. 

Continue lendo para saber mais.

## <a name="resource-group-and-network-infrastructure"></a>Infraestrutura de rede e grupo de recursos

Considere o local em que os elementos de seu vFXT Avere para implantação do Azure estarão. O diagrama a seguir mostra uma possível disposição para o vFXT Avere para componentes do Azure:

![Diagrama mostrando o cluster de controlador e o cluster de VMs em uma sub-rede. Em torno da sub-rede limite há um limite de rede virtual. Dentro da vnet, há um hexágono que representa o ponto de extremidade de serviço de armazenamento; ele está conectado com uma seta tracejada para um Armazenamento de Blobs fora da rede virtual.](media/avere-vfxt-components-option.png)

Siga estas diretrizes ao planejar a infraestrutura de rede do seu sistema do Avere vFXT:

* Todos os elementos devem ser gerenciados com uma nova assinatura criada para a implantação do vFXT Avere. Os benefícios incluem: 
  * Acompanhamento de custo mais simples – exibir e auditar todos os custos de ciclos de recursos, infraestrutura e computação em uma assinatura de auditoria.
  * Limpeza mais fácil – você pode remover toda a assinatura quando tiver terminado o projeto.
  * Particionamento conveniente de cotas de recursos – proteja outras cargas de trabalho críticas de uma possível limitação de recursos isolando os clientes e o cluster avere vFXT em uma única assinatura. Isso evita conflitos ao trazer um grande número de clientes para um fluxo de trabalho de computação de alto desempenho.

* Localize seus sistemas de computação de cliente perto do cluster vFXT. O armazenamento de back-end pode ser mais remoto.  

* O cluster vFXT e a VM do controlador de cluster devem estar localizados na mesma rede virtual (vnet), no mesmo grupo de recursos e usar a mesma conta de armazenamento. O modelo de criação automatizado de cluster trata isso na maioria das situações.

* O cluster deve estar localizado em sua própria sub-rede para evitar conflitos de endereço IP com clientes ou recursos de computação. 

* O modelo de criação de cluster pode criar a maioria dos recursos de infraestrutura necessários para o cluster, incluindo grupos de recursos, redes virtuais, sub-redes e contas de armazenamento. Se você quiser usar recursos que já existem, verifique se eles atendem aos requisitos nesta tabela. 

  | Resource | Usar existente? | Requisitos |
  |----------|-----------|----------|
  | Grupo de recursos | Sim, se estiver vazio | Deve estar vazio| 
  | Conta de armazenamento | Sim se estiver conectando um contêiner de blob existente após a criação do cluster <br/>  Não se estiver criando um novo contêiner de blob durante a criação do cluster | O contêiner de blob existente deve estar vazio <br/> &nbsp; |
  | Rede virtual | Sim | Deve incluir um ponto de extremidade de serviço de armazenamento se estiver criando um novo contêiner de blob do Azure | 
  | Subnet | Sim |   |

## <a name="ip-address-requirements"></a>Requisitos de endereço IP 

Certifique-se de que a sub-rede do seu cluster tenha um intervalo de endereços IP grande o suficiente para dar suporte ao cluster. 

O cluster do Avere vFXT usa os seguintes endereços IP:

* Um endereço IP de gerenciamento de cluster. Esse endereço pode se mover de nó a nó no cluster, mas está sempre disponível para que você possa se conectar à ferramenta de configuração do Painel de Controle do Avere.
* Para cada nó de cluster:
  * Pelo menos um endereço IP voltado para o cliente. (Todos os endereços voltados ao cliente são gerenciados do *vserver* do cluster e você pode movê-los entre os nós conforme necessário.)
  * Um endereço IP para comunicação de cluster
  * Endereço IP de uma instância (atribuído à VM)

Se você usar o Armazenamento de Blobs do Azure, ele também poderá exigir endereços IP de rede de virtual do cluster:  

* Uma conta de Armazenamento de Blobs do Azure exige pelo menos cinco endereços IP. Lembre-se desse requisito se você localizar o Armazenamento de Blobs na mesma rede virtual do cluster.
* Se você usar um Armazenamento de Blobs do Azure que esteja fora da rede virtual para o cluster, deverá criar um ponto de extremidade de serviço de armazenamento dentro da vnet. Esse ponto de extremidade não usa um endereço IP.

Você tem a opção de localizar recursos de rede e Armazenamento de Blobs (se usado) em diferentes grupos de recursos do cluster.

## <a name="vfxt-node-size"></a>tamanho do nó vFXT

As VMs que atuam como nós de cluster determinam a capacidade de armazenamento e a taxa de transferência de solicitação do seu cache. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Cada nó vFXT será idêntico. Ou seja, se você criar um cluster de três nós, terá três VMs do mesmo tipo e tamanho. 

| Tipo de instância | vCPUs | Memória  | Armazenamento SSD local  | Discos de dados máximos | Taxa de transferência de disco sem cache | NIC (contagem) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51.200 IOPS <br/> 768 MBps | 16.000 MBps (8)  |

O cache de disco por nó é configurável e pode variar de 1.000 GB a 8.000 GB. 4 TB por nó é o tamanho de cache recomendado para nós Standard_E32s_v3.

Para obter informações adicionais sobre essas VMs, leia a documentação do Microsoft Azure:

* [Tamanhos de máquina virtual otimizada para memória](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Cota da conta

Verifique se sua assinatura tem a capacidade para executar o cluster de do Avere vFXT, bem como todos os sistemas de computação ou cliente que estejam sendo usados. Leia [Cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para obter detalhes.

## <a name="back-end-data-storage"></a>Armazenamento de dados de back-end

Em que local o cluster do Avere vFXT deve armazenar seus dados quando eles não estiverem em cache? Decida se seu conjunto de trabalho será armazenado a longo prazo em um novo contêiner de Blobs ou em um sistema de armazenamento de hardware ou de nuvem existente. 

Se você quiser usar o Armazenamento de Blobs do Azure para o back-end, deverá criar um novo contêiner como parte da criação do cluster vFXT. Essa opção cria e configura o novo contêiner para que ele esteja pronto para uso assim que o cluster estiver pronto. 

Leia [Criar o Avere vFXT para Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) para obter detalhes.

> [!NOTE]
> Somente contêineres de Armazenamento de Blobs vazios podem ser usados como arquivistas principais para o sistema de vFXT Avere. O vFXT deve ser capaz de gerenciar seu armazenamento de objeto sem necessidade de preservar os dados existentes. 
>
> Leia [Movimentação de dados para o cluster do vFXT](avere-vfxt-data-ingest.md) para aprender a copiar dados para o novo contêiner do cluster com eficiência usando computadores cliente e o cache do Avere vFXT.

Se você quiser usar um sistema de armazenamento local existente, adicione-o ao cluster vFXT após sua criação. Leia [Configurar armazenamento](avere-vfxt-add-storage.md) para obter instruções detalhadas sobre como adicionar um sistema de armazenamento ao cluster do Avere vFXT.

## <a name="cluster-access"></a>Acesso ao cluster 

O cluster do Avere vFXT para Azure está localizado em uma sub-rede privada e o cluster não tem um endereço IP público. Você deve ter algum método para acessar a sub-rede privada para conexões de cliente e administração de cluster. 

As opções de acesso incluem:

* Host de atalho – atribua um endereço IP público a uma VM separada na rede privada e use-o para criar um túnel SSL para os nós de cluster. 

  > [!TIP]
  > Se você definir um endereço IP público no controlador de cluster, poderá usá-lo como o host de atalho. Leia [Controlador de cluster como host de atalho](#cluster-controller-as-jump-host) para obter mais informações.

* VPN (rede virtual privada) – configure uma VPN ponto a site ou site a site para sua rede privada.

* Azure ExpressRoute – configure uma conexão privada por meio de um parceiro do ExpressRoute. 

Para obter detalhes sobre essas opções, leia a [Documentação da Rede Virtual do Azure sobre comunicação com a Internet](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Controlador de cluster como host de atalho

Se você definir um endereço IP público no controlador de cluster, poderá usá-lo como um host de atalho para contatar o cluster do Avere vFXT de fora da sub-rede privada. No entanto, como o controlador tem privilégios de acesso para modificar nós de cluster, isso cria um pequeno risco de segurança.  

Para melhorar a segurança de um controlador com um endereço IP público, o script de implantação cria automaticamente um grupo de segurança de rede que restringe o acesso de entrada apenas à porta 22. Você pode proteger ainda mais o sistema bloqueando o acesso a seu intervalo de endereços IP de origem, ou seja, permitir somente conexões de computadores que você pretende usar para acesso ao cluster.

Ao criar o cluster, você pode escolher se deseja ou não criar um endereço IP público no controlador de cluster. 

* Se você criar uma VNET ou uma nova sub-rede, o controlador de cluster receberá um endereço IP público.
* Se você selecionar uma VNET e uma sub-rede existentes, o controlador de cluster terá apenas os endereços IP privados. 

## <a name="vm-access-roles"></a>Funções de acesso à VM 

O Azure usa o RBAC ( [controle de acesso baseado em função](../role-based-access-control/index.yml) ) para autorizar as VMs de cluster a executar determinadas tarefas. Por exemplo, o controlador de cluster precisa de autorização para criar e configurar as VMs do nó de cluster. Os nós de cluster precisam ser capazes de atribuir ou reatribuir endereços IP a outros nós de cluster.

Duas funções internas do Azure são usadas para as máquinas virtuais avere vFXT: 

* O controlador de cluster usa a função interna [colaborador do avere](../role-based-access-control/built-in-roles.md#avere-contributor). 
* Nós de cluster usam o operador de função interna [avere](../role-based-access-control/built-in-roles.md#avere-operator)

Se você precisar personalizar funções de acesso para componentes avere vFXT, deverá definir sua própria função e, em seguida, atribuí-la às VMs no momento em que elas forem criadas. Você não pode usar o modelo de implantação no Azure Marketplace. Consulte atendimento ao cliente da Microsoft e suporte abrindo um tíquete no portal do Azure, conforme descrito em [obter ajuda com o sistema](avere-vfxt-open-ticket.md). 

## <a name="next-step-understand-the-deployment-process"></a>Próxima etapa: Compreender o processo de implantação

A [Visão geral da implantação](avere-vfxt-deploy-overview.md) apresenta uma visão geral de todas as etapas necessárias para criar um Avere vFXT para o sistema do Azure e prepará-lo para veicular dados.  