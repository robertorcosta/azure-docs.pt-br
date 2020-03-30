---
title: Planejar seu sistema Avere vFXT – Azure
description: Explica o planejamento a ser feito antes de implantar o Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: cd0c74c8aa40b3e96716ef37aa27b08b5f6aece1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547533"
---
# <a name="plan-your-avere-vfxt-system"></a>Planejar seu sistema Avere vFXT

Este artigo explica como planejar um novo cluster Avere vFXT para Azure que esteja posicionado e dimensionado adequadamente para suas necessidades.

Antes de ir ao Azure Marketplace ou criar quaisquer VMs, considere esses detalhes:

* Como o cluster interagirá com outros recursos do Azure?
* Onde os elementos de cluster devem estar localizados em redes privadas e sub-redes?
* Que tipo de armazenamento back-end você usará e como o cluster irá acessá-lo?
* Quão poderosos seus nós de cluster precisam ser para suportar seu fluxo de trabalho?

Continue lendo para saber mais.

## <a name="learn-the-components-of-the-system"></a>Conheça os componentes do sistema

Pode ser útil entender os componentes do sistema Avere vFXT para Azure quando você começar a planejar.

* Nós de cluster - O cluster é composto por três ou mais VMs configurados como nós de cluster. Mais nós dão ao sistema maior rendimento e um cache maior.

* Cache - A capacidade de cache é dividida igualmente entre os nós de cluster. Defina o tamanho do cache por nó quando criar o cluster; os tamanhos do nó são adicionados para se tornar o tamanho total do cache.

* Controlador de cluster - O controlador de cluster é um VM adicional localizado dentro da mesma sub-rede que os nós de cluster. O controlador é necessário para criar o cluster e para tarefas de gerenciamento contínuas.

* Armazenamento back-end - Os dados que você deseja ter armazenados em cache são armazenados a longo prazo em um sistema de armazenamento de hardware ou em um contêiner Azure Blob. Você pode adicionar armazenamento depois de criar o cluster Avere vFXT for Azure ou se usar o armazenamento Blob você pode adicionar e configurar o contêiner ao criar o cluster.

* Clientes - Máquinas clientes que usam os arquivos armazenados em cache se conectam ao cluster usando um caminho de arquivo virtual em vez de acessar diretamente os sistemas de armazenamento. (Leia mais no [cluster Avere vFXT](avere-vfxt-mount-clients.md).)

## <a name="subscription-resource-group-and-network-infrastructure"></a>Assinatura, grupo de recursos e infra-estrutura de rede

Considere o local em que os elementos de seu vFXT Avere para implantação do Azure estarão. O diagrama a seguir mostra uma possível disposição para o vFXT Avere para componentes do Azure:

![Diagrama mostrando o cluster de controlador e o cluster de VMs em uma sub-rede. Em torno da sub-rede limite há um limite de rede virtual. Dentro da vnet, há um hexágono que representa o ponto de extremidade de serviço de armazenamento; ele está conectado com uma seta tracejada para um Armazenamento de Blobs fora da rede virtual.](media/avere-vfxt-components-option.png)

Siga essas diretrizes ao planejar a infra-estrutura de rede do cluster Avere vFXT:

* Crie uma nova assinatura para cada implantação do Avere vFXT para a implantação do Azure. Gerencie todos os componentes desta assinatura.

  Os benefícios de usar uma nova assinatura para cada implantação incluem:
  * Acompanhamento de custo mais simples – exibir e auditar todos os custos de ciclos de recursos, infraestrutura e computação em uma assinatura de auditoria.
  * Limpeza mais fácil – você pode remover toda a assinatura quando tiver terminado o projeto.
  * Particionamento conveniente de cotas de recursos - Isole os clientes Avere vFXT e cluster em uma única assinatura para proteger outras cargas de trabalho críticas de possíveis estrangulamentos de recursos. Essa separação evita conflitos ao trazer um grande número de clientes para um fluxo de trabalho de computação de alto desempenho.

* Localize seus sistemas de computação de cliente perto do cluster vFXT. O armazenamento de back-end pode ser mais remoto.  

* Localize o cluster vFXT e o controlador de cluster VM juntos - especificamente, eles devem ser:

  * Na mesma rede virtual
  * No mesmo grupo de recursos
  * Usando a mesma conta de armazenamento
  
  O modelo de criação de cluster lida com essa configuração para a maioria das situações.

* O cluster deve estar localizado em sua própria sub-rede para evitar conflitos de endereço IP com clientes ou outros recursos de computação.

* Use o modelo de criação de cluster para criar a maioria dos recursos de infra-estrutura necessários para o cluster, incluindo grupos de recursos, redes virtuais, sub-redes e contas de armazenamento.

  Se você quiser usar os recursos que já existem, certifique-se de que eles atendam aos requisitos nesta tabela.

  | Recurso | Usar o existente? | Requisitos |
  |----------|-----------|----------|
  | Resource group | Sim, se estiver vazio. | Deve estar vazio.|
  | Conta de armazenamento | **Sim,** se conectar um recipiente Blob existente após a criação de cluster <br/>  **Não** se criar um novo contêiner Blob durante a criação de clusters | O recipiente Blob existente deve estar vazio <br/> &nbsp; |
  | Rede virtual | Sim | Deve incluir um ponto final de serviço de armazenamento se criar um novo contêiner Azure Blob |
  | Sub-rede | Sim | Não é possível conter outros recursos |

## <a name="ip-address-requirements"></a>Requisitos de endereço IP

Certifique-se de que a sub-rede do seu cluster tenha um intervalo de endereços IP grande o suficiente para dar suporte ao cluster.

O cluster do Avere vFXT usa os seguintes endereços IP:

* Um endereço IP de gerenciamento de cluster. Este endereço pode mover de nó para nó no cluster conforme necessário para que ele esteja sempre disponível. Use este endereço para se conectar à ferramenta de configuração do Painel de Controle Avere.
* Para cada nó de cluster:
  * Pelo menos um endereço IP voltado para o cliente. (Todos os endereços voltados para o cliente são gerenciados pelo *servidor vserver*do cluster, que pode mover os endereços IP entre nós conforme necessário.)
  * Um endereço IP para comunicação de cluster
  * Endereço IP de uma instância (atribuído à VM)

Se você usar o armazenamento Azure Blob, ele também pode exigir endereços IP da rede virtual do seu cluster:  

* Uma conta de Armazenamento de Blobs do Azure exige pelo menos cinco endereços IP. Tenha esse requisito em mente se você localizar o armazenamento Blob na mesma rede virtual que o seu cluster.
* Se você usar o armazenamento Azure Blob que está fora da rede virtual do cluster, crie um ponto final de serviço de armazenamento dentro da rede virtual. O ponto final não usa um endereço IP.

Você tem a opção de localizar recursos de rede e Armazenamento de Blobs (se usado) em diferentes grupos de recursos do cluster.

## <a name="vfxt-node-size"></a>tamanho do nó vFXT

As VMs que atuam como nós de cluster determinam a capacidade de armazenamento e a taxa de transferência de solicitação do seu cache. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Cada nó vFXT será idêntico. Ou seja, se você criar um cluster de três nós, terá três VMs do mesmo tipo e tamanho.

| Tipo de instância | vCPUs | Memória  | Armazenamento SSD local  | Discos de dados máximos | Taxa de transferência de disco sem cache | NIC (contagem) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51.200 IOPS <br/> 768 MBps | 16.000 MBps (8)  |

O cache de disco por nó é configurável e pode variar de 1.000 GB a 8.000 GB. 4 TB por nó é o tamanho de cache recomendado para Standard_E32s_v3 os nades.

Para obter informações adicionais sobre essas VMs, leia a documentação do Microsoft Azure: [Tamanhos otimizados de máquinas virtuais otimizadas de memória](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Cota da conta

Verifique se sua assinatura tem a capacidade para executar o cluster de do Avere vFXT, bem como todos os sistemas de computação ou cliente que estejam sendo usados. Leia [Cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para obter detalhes.

## <a name="back-end-data-storage"></a>Armazenamento de dados de back-end

Os sistemas de armazenamento back-end fornecem arquivos para o cache do cluster e também recebem dados alterados do cache. Decida se o seu conjunto de trabalho será armazenado a longo prazo em um novo contêiner Blob ou em um sistema de armazenamento existente (nuvem ou hardware). Esses sistemas de armazenamento back-end são chamados *de filers principais*.

### <a name="hardware-core-filers"></a>Filers de núcleo de hardware

Adicione sistemas de armazenamento de hardware ao cluster vFXT depois de criar o cluster. Você pode usar uma variedade de sistemas de hardware populares, incluindo sistemas locais, desde que o sistema de armazenamento possa ser alcançado a partir da sub-rede do cluster.

Leia [Configurar armazenamento](avere-vfxt-add-storage.md) para obter instruções detalhadas sobre como adicionar um sistema de armazenamento ao cluster do Avere vFXT.

### <a name="cloud-core-filers"></a>Filers de núcleo de nuvem

O sistema Avere vFXT for Azure pode usar recipientes Blob vazios para armazenamento back-end. Os contêineres devem estar vazios quando adicionados ao cluster - o sistema vFXT deve ser capaz de gerenciar seu armazenamento de objetos sem a necessidade de preservar os dados existentes.

> [!TIP]
> Se você quiser usar o armazenamento Azure Blob para o back-end, crie um novo contêiner como parte da criação do cluster vFXT. O modelo de criação de cluster pode criar e configurar um novo contêiner Blob para que ele esteja pronto para uso assim que o cluster estiver disponível. Adicionar um recipiente mais tarde é mais complicado.
>
> Leia [Criar o Avere vFXT para Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) para obter detalhes.

Depois de adicionar o recipiente de armazenamento Blob vazio como um arquivo principal, você pode copiar dados para ele através do cluster. Use um mecanismo de cópia paralelo e multi-threaded. Leia [Movimentação de dados para o cluster do vFXT](avere-vfxt-data-ingest.md) para aprender a copiar dados para o novo contêiner do cluster com eficiência usando computadores cliente e o cache do Avere vFXT.

## <a name="cluster-access"></a>Acesso ao cluster

O cluster do Avere vFXT para Azure está localizado em uma sub-rede privada e o cluster não tem um endereço IP público. Você deve ter alguma maneira de acessar a sub-rede privada para administração de clusters e conexões com clientes.

As opções de acesso incluem:

* Host de atalho – atribua um endereço IP público a uma VM separada na rede privada e use-o para criar um túnel SSL para os nós de cluster.

  > [!TIP]
  > Se você definir um endereço IP público no controlador de cluster, poderá usá-lo como o host de atalho. Leia [Controlador de cluster como host de atalho](#cluster-controller-as-jump-host) para obter mais informações.

* Rede privada virtual (VPN) - Configure uma VPN ponto a ponto ou site-para-site entre sua rede privada no Azure e redes corporativas.

* Azure ExpressRoute – configure uma conexão privada por meio de um parceiro do ExpressRoute.

Para obter detalhes sobre essas opções, leia a [Documentação da Rede Virtual do Azure sobre comunicação com a Internet](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Controlador de cluster como host de atalho

Se você definir um endereço IP público no controlador de cluster, poderá usá-lo como um host de atalho para contatar o cluster do Avere vFXT de fora da sub-rede privada. No entanto, como o controlador tem privilégios de acesso para modificar nós de cluster, isso cria um pequeno risco de segurança.

Para melhorar a segurança de um controlador com um endereço IP público, o script de implantação cria automaticamente um grupo de segurança de rede que restringe o acesso de entrada à porta 22 apenas. Você pode proteger ainda mais o sistema bloqueando o acesso a seu intervalo de endereços IP de origem, ou seja, permitir somente conexões de computadores que você pretende usar para acesso ao cluster.

Ao criar o cluster, você pode escolher se deseja ou não criar um endereço IP público no controlador de cluster.

* Se você criar uma **nova rede virtual** ou uma nova **sub-rede,** o controlador de cluster será atribuído a um endereço IP **público.**
* Se você selecionar uma rede virtual e uma sub-rede existentes, o controlador de cluster terá apenas endereços IP **privados.**

## <a name="vm-access-roles"></a>Funções de acesso vm

O Azure usa o RBAC [(Role-Based Access Control, controle de acesso baseado em função)](../role-based-access-control/index.yml) para autorizar as VMs do cluster a executar determinadas tarefas. Por exemplo, o controlador de cluster precisa de autorização para criar e configurar as VMs do nó de cluster. Os nós de cluster precisam ser capazes de atribuir ou reatribuir endereços IP a outros nós de cluster.

Duas funções azure incorporadas são usadas para as máquinas virtuais Avere vFXT:

* O controlador de cluster usa a função incorporada [Avere Contributor](../role-based-access-control/built-in-roles.md#avere-contributor).
* Os nós de cluster usam a função [incorporada Avere Operator](../role-based-access-control/built-in-roles.md#avere-operator).

Se você precisar personalizar as funções de acesso para componentes Avere vFXT, você deve definir sua própria função e, em seguida, atribuí-la às VMs no momento em que elas são criadas. Não é possível usar o modelo de implantação no Azure Marketplace. Consulte o Serviço de Atendimento ao Cliente e Suporte da Microsoft abrindo um ticket no portal Azure conforme descrito em [Obter ajuda com o seu sistema](avere-vfxt-open-ticket.md).

## <a name="next-steps"></a>Próximas etapas

[A visão geral](avere-vfxt-deploy-overview.md) da implantação dá a visão geral das etapas necessárias para criar um sistema Avere vFXT para Azure e prepará-lo para servir dados.
