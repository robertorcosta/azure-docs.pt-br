---
title: Conceitos-nuvens e clusters privados
description: Saiba mais sobre os principais recursos dos data centers definidos pelo software Azure VMware e clusters vSphere na solução VMware no Azure pela VMware.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 09e1fd45b1dd873509f942ef8b524783acfed4ce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84906982"
---
# <a name="azure-vmware-solution-avs-preview-private-cloud-and-cluster-concepts"></a>Computação da solução VMware do Azure (AVS) Preview privada e conceitos de cluster

A solução VMware do Azure (AVS) fornece nuvens privadas baseadas no VMware no Azure. As nuvens privadas são criadas a partir de clusters de hosts bare-metal dedicados e são implantadas e gerenciadas por meio do portal do Azure. Os clusters em nuvens privadas são provisionados com o software VMware vSphere, vCenter, vSAN e NSX. As implantações de hardware e software da nuvem privada de AVS são totalmente integradas e automatizadas no Azure.

Há uma relação lógica entre as assinaturas do Azure, as nuvens privadas da AVS, os clusters vSAN e os hosts. No diagrama, são mostradas duas nuvens privadas em uma única assinatura do Azure. As nuvens privadas representam um ambiente de desenvolvimento e de produção, cada uma com sua própria nuvem privada. Em cada uma dessas nuvens privadas, há dois clusters. Para mostrar as necessidades mais baixas em potencial de um ambiente de desenvolvimento, clusters menores com hosts de menor capacidade são usados. Todos esses conceitos são descritos nas seções abaixo.

![Imagem de duas nuvens privadas em uma assinatura de cliente](./media/hosts-clusters-private-clouds-final.png)

## <a name="private-clouds"></a>Nuvens privadas

Nuvens privadas contêm clusters vSAN criados com hosts do Azure dedicados e bare-metal. Cada nuvem privada pode ter vários clusters, todos gerenciados pelo mesmo servidor vCenter e pelo NSX-T Manager. Você pode implantar e gerenciar nuvens privadas no portal, da CLI ou com o PowerShell. Assim como ocorre com outros recursos, as nuvens privadas são instaladas e gerenciadas de dentro de uma assinatura do Azure.

O número de nuvens privadas em uma assinatura é escalonável. Inicialmente, há um limite de uma nuvem privada por assinatura.

## <a name="clusters"></a>Clusters

Você criará pelo menos um cluster vSAN em cada nuvem privada. Quando você cria uma nuvem privada, há um cluster por padrão. Você pode adicionar clusters adicionais a uma nuvem privada usando o portal do Azure ou por meio da API. Todos os clusters têm um tamanho padrão de três hosts e podem ser dimensionados de 3 para 16 hosts. O tipo de hosts usado em um cluster deve ser do mesmo tipo. Os tipos de hosts são descritos na próxima seção.

Os clusters de avaliação estão disponíveis para avaliação e são limitados a três hosts e um único cluster de avaliação por nuvem privada. Você pode dimensionar um cluster de avaliação por um único host durante o período de avaliação.

Você cria, exclui e dimensiona clusters por meio do portal ou da API. Você ainda usa o vSphere e o NSX-T Manager para gerenciar a maioria dos outros aspectos da configuração ou operação de cluster. Todo o armazenamento local de cada host em um cluster está sob controle do vSAN.

## <a name="hosts"></a>Hosts

Nós de infraestrutura hiperconvergentes e bare-metal são usados em clusters de nuvem privada da AVS. A RAM, a CPU e as capacidades de disco do host são fornecidas na tabela a seguir. 

| Tipo de host              |             CPU             |   RAM (GB)   |  Camada de cache do vSAN NVMe (TB, bruto)  |  tipo de capacidade vSAN SSD (TB, RAW)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| High-end (ele)          |  dual Intel 18 Core de 2,3 GHz  |     576      |                3.2               |                15,20               |

Os hosts que são usados para criar ou dimensionar clusters são adquiridos de um pool isolado de hosts. Esses hosts passaram por testes de hardware e tiveram todos os dados excluídos com segurança dos discos flash. Quando você remove um host de um cluster, os discos internos são apagados com segurança e os hosts são colocados no pool isolado de hosts. Quando você adiciona um host a um cluster, é usado um host limpo do pool isolado.

## <a name="vmware-software-versions"></a>Versões de software VMware

As versões de software atuais do software VMware usado em clusters de nuvem privada AVS são:

| Software              |    Versão   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U2    | 
| ESXi                  |    6,7 U2    | 
| vSAN                  |    6,7 U2    |
| NSX-T                 |      2.5     |

Para qualquer novo cluster em uma nuvem privada, a versão do software corresponderá ao que está sendo executado atualmente na nuvem privada. Para qualquer nova nuvem privada em uma assinatura de cliente, a versão mais recente da pilha de software é instalada.

As políticas e os processos de atualização gerais para o software da plataforma AVS são descritos no documento conceitos de atualizações.

## <a name="host-maintenance-and-lifecycle-management"></a>Manutenção do host e gerenciamento do ciclo de vida

A manutenção do host e o gerenciamento do ciclo de vida são feitos sem afetar a capacidade ou o desempenho de clusters de nuvem privada. Exemplos de manutenção automatizada do host incluem atualizações de firmware e reparo ou substituição de hardware.

A Microsoft é responsável pelo gerenciamento do ciclo de vida de dispositivos NSX-T, como NSX-T Manager e NSX-T Edge. A Microsoft também é responsável por inicializar a configuração de rede, como a criação do gateway de camada 0 e a habilitação do roteamento norte-sul. Como administrador de sua nuvem privada de AVS, você é responsável pela configuração de SDN do NSX-T, como segmentos de rede, regras de firewall distribuídas, gateways de camada 1 e balanceadores de carga.

> [!IMPORTANT]
> Um administrador da AVS não deve modificar a configuração do gateway da camada 0 ou da borda do NSX-T. Isso pode resultar em uma perda de serviço.

## <a name="backup-and-restoration"></a>Backup e restauração

As configurações do vCenter e do NSX-T da nuvem privada são submetidas a cada hora. Os backups são mantidos por três dias. A restauração de um backup é solicitada por meio de uma solicitação de serviço no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

A próxima etapa é aprender os [conceitos de rede e entre conectividade](concepts-networking.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

