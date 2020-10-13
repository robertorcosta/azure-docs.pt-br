---
title: Implantar o horizonte na solução VMware do Azure
description: Saiba como implantar o VMware horizonte na solução VMware do Azure.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: bda4be049e360670cb7038bfbb3070c2a5f262c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91729042"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Implantar o horizonte na solução VMware do Azure 

>[!NOTE]
>Este documento se concentra no produto VMware horizonte. Isso é conhecido anteriormente como horizonte 7 antes que o nome do produto mude do horizonte 7 para o horizonte. O horizonte é uma solução diferente da nuvem do horizonte no Azure, embora haja alguns componentes compartilhados. As principais vantagens da solução Azure VMware incluem um método de dimensionamento mais simples e que o gerenciamento do VMware Cloud Foundation é integrado ao portal do Azure.

O [VMware horizonte](https://www.vmware.com/products/horizon.html)® é uma plataforma de desktop e de aplicativos virtual que é executada na data center e fornece gerenciamento simples e centralizado. Ele fornece áreas de trabalho virtuais e aplicativos para usuários finais em qualquer dispositivo, em qualquer lugar. O horizonte permite a criação e o agente de conexões com áreas de trabalho virtuais do Windows, áreas de trabalho virtuais do Linux, aplicativos hospedados no RDS (Área de Trabalho Remota Server), desktops e máquinas físicas.

Aqui, nos concentramos especificamente na implantação do horizonte na solução VMware do Azure. Para obter informações gerais sobre o VMware Horizonte, consulte a documentação de produção do horizonte:

* [O que é o VMware horizonte?](https://www.vmware.com/products/horizon.html)

* [Saiba mais sobre o VMware horizonte](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Arquitetura de referência de horizonte](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Com a introdução do horizonte na solução VMware do Azure, agora há duas soluções de VDI (Virtual Desktop Infrastructure) na plataforma Azure. O diagrama a seguir resume as principais diferenças em um alto nível.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="As diferenças entre o horizonte na solução VMware do Azure e na nuvem de horizonte no Azure" border="false":::

O horizonte 2006 e versões posteriores na linha de lançamento do horizonte 8 dão suporte à implantação local e à implantação da solução do Azure VMware. Há alguns recursos de horizonte com suporte no local, mas não na solução do Azure VMware. Também há suporte para produtos adicionais no ecossistema de horizonte. Para obter informações, consulte [paridade e interoperabilidade de recursos](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Implantar o horizonte em uma nuvem híbrida

Você pode implantar o horizonte em um ambiente de nuvem híbrida ao usar a contador (arquitetura de pod de nuvem do horizonte) para interconectar data centers locais e centros de dados do Azure. O contador é normalmente usado para escalar verticalmente sua implantação, criar uma nuvem híbrida e fornecer redundância para continuidade dos negócios e recuperação de desastres. Para obter uma discussão aprofundada sobre as diretrizes de continuidade de negócios do VMware Horizonte, consulte [expandindo ambientes existentes do horizonte 7](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>CONTADOR não é uma implantação ampliada; cada pod de horizonte é distinto e todos os servidores de conexão que pertencem a cada pods individual devem estar localizados em um único local e ser executados no mesmo domínio de transmissão de uma perspectiva de rede.

Como o data center local ou privado, o horizonte pode ser implantado em uma nuvem privada da solução Azure VMware. Discutiremos as principais diferenças na implantação do horizonte local e na solução Azure VMware nas seções a seguir.

A nuvem privada do Azure é conceitualmente a mesma do VMware SDDC, um termo normalmente usado na documentação do horizonte. O restante deste documento usará os termos a nuvem privada do Azure e o VMware SDDC intercambiáveis.

O conector de nuvem horizonte é necessário para o horizonte na solução VMware do Azure para gerenciar licenças de assinatura. O conector de nuvem pode ser implantado na rede virtual do Azure junto com os servidores de conexão de horizonte.

>[!IMPORTANT]
>O suporte ao plano de controle de horizonte para o horizonte na solução VMware do Azure ainda não está disponível. Certifique-se de baixar a versão VHD do conector de nuvem do horizonte.

## <a name="vcenter-cloud-admin-role"></a>função de administrador de nuvem do vCenter

Como a solução Azure VMware é um serviço SDDC e o Azure gerencia o ciclo de vida do SDDC na solução Azure VMware, o modelo de permissão do vCenter na solução Azure VMware é limitado por design.

Os clientes são obrigados a usar a função de administrador de nuvem, que tem um conjunto limitado de permissões do vCenter. O produto de horizonte foi modificado para funcionar com a função de administrador de nuvem na solução VMware do Azure, especificamente:

* O provisionamento de clones instantâneos foi modificado para ser executado na solução VMware do Azure.

* Uma política vSAN específica (VMware_Horizon) foi criada na solução Azure VMware para trabalhar com o horizonte, que deve estar disponível e usado no SDDCs implantado para horizonte.

* o CBRC (vSphere content-based Read cache), também conhecido como View Storage Accelerator, é desabilitado durante a execução na solução VMware do Azure.

>[!IMPORTANT]
>CBRC não deve ser ativado novamente.

>[!NOTE]
>A solução Azure VMware configura automaticamente as configurações de horizonte específicas, desde que você implante o horizonte 2006 (também conhecido como horizonte 8) e acima na ramificação do horizonte 8 e selecione a opção **Azure** no instalador do servidor de conexão de horizonte.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Horizonte na arquitetura de implantação da solução Azure VMware

Um design típico de arquitetura de horizonte usa um pod e uma estratégia de bloqueio. Um bloco é um único vCenter, enquanto vários blocos combinados fazem um pod. Um pod de horizonte é uma unidade de organização determinada pelos limites de escalabilidade de horizonte. Cada pod de horizonte tem um portal de gerenciamento separado e, portanto, uma prática de design padrão é minimizar o número de pods.

Cada nuvem tem seu próprio esquema de conectividade de rede. Combinado com a rede do VMware SDDC/NSX Edge, a conectividade de rede da solução Azure VMware apresenta requisitos exclusivos para a implantação de horizonte que é diferente do local.

Cada nuvem privada do Azure/SDDC é capaz de lidar com sessões de desktop ou de aplicativo 4.000, o que pressupõe o seguinte:

* O tráfego de carga de trabalho é alinhado com o do perfil do operador de tarefa LoginVSI.

* Somente o tráfego de protocolo é considerado, sem dados de usuário.

* O NSX Edge está configurado para ser grande.

>[!NOTE]
>Seu perfil de carga de trabalho e suas necessidades podem ser diferentes e, portanto, os resultados podem variar com base no seu caso de uso. Os volumes de dados do usuário podem reduzir os limites de escala no contexto de sua carga de trabalho. Dimensione e planeje sua implantação de acordo. Para obter mais informações, consulte as diretrizes de dimensionamento na seção [tamanho hosts da solução do Azure VMware para implantações de horizonte](#size-azure-vmware-solution-hosts-for-horizon-deployments) .

Devido ao limite máximo de nuvem privada/SDDC do Azure, recomendamos uma arquitetura de implantação em que os servidores de conexão de horizonte e os UAGs (gateways de acesso unificado) da VMware estejam em execução dentro da rede virtual do Azure. Isso efetivamente transforma cada nuvem privada/SDDC do Azure em um bloco. Isso, por sua vez, maximiza a escalabilidade do horizonte em execução na solução VMware do Azure.

A conexão da rede virtual do Azure com as nuvens/SDDCs privadas do Azure deve ser configurada com o ExpressRoute FastPath. O diagrama a seguir mostra uma implantação de Pod básico.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="As diferenças entre o horizonte na solução VMware do Azure e na nuvem de horizonte no Azure" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Conectividade de rede para o horizonte de escala na solução VMware do Azure

Esta seção apresenta a arquitetura de rede em um alto nível para o horizonte de dimensionamento na solução do Azure VMware com alguns exemplos comuns de implantação. O foco aqui se destina especificamente a elementos críticos de rede.

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Pod de horizonte único na solução VMware do Azure

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="As diferenças entre o horizonte na solução VMware do Azure e na nuvem de horizonte no Azure" border="false":::

Um único pod de horizonte é o cenário de implantação mais direta. Neste exemplo, você decide que deseja implantar apenas um pod de horizonte na região leste dos EUA. Como cada Cloud/SDDC particular é estimado aproximadamente para lidar com o tráfego de sessões de área de trabalho 4.000, para implantar o tamanho máximo de pod de horizonte, você pode planejar a implantação de até três nuvens privadas/SDDCs.

Portanto, neste exemplo, em combinação com as VMs (máquinas virtuais) de infraestrutura de horizonte implantadas na rede virtual do Azure, você pode alcançar as 12.000 sessões por pod de horizonte com base em suas necessidades de carga de trabalho e dados. A conexão entre cada nuvem privada e o SDDC para a rede virtual do Azure é o caminho rápido do ExpressRoute, de modo que não há necessidade de tráfego leste-oeste entre nuvens privadas.

As principais suposições para esse exemplo de implantação básica incluem:

* Você não tem um pod de horizonte local que deseja conectar a esse novo pod usando a contador (arquitetura de pod de nuvem).

* Os usuários finais se conectam às suas áreas de trabalho virtuais pela Internet (vs. conectando por meio de um data center local).

Neste exemplo básico, você pode conectar seu controlador de domínio do AD na rede virtual do Azure com sua Active Directory local por meio de VPN ou um circuito de ExpressRoute.

Uma variação no exemplo básico abordada pode ser para dar suporte à conectividade para recursos locais. Podem ser usuários que acessam áreas de trabalho e geram tráfego de aplicativos de área de trabalho virtual ou conectando-se a um pod de horizonte local usando contador.

O diagrama a seguir mostra como você pode fazer isso.Para conectar sua rede corporativa à rede virtual do Azure, você precisará de um ExpressRoute.Você também precisará conectar sua rede corporativa com cada nuvem privada/SDDCs usando Alcance Global, que permite a conectividade do SDDC com os recursos do ExpressRoute e locais.

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="As diferenças entre o horizonte na solução VMware do Azure e na nuvem de horizonte no Azure" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Vários pods de horizonte na solução do Azure VMware em várias regiões

Para outro exemplo de pod de horizonte, vejamos um exemplo que mostra o horizonte de dimensionamento entre vários pods.Neste exemplo, você está implantando dois pods de horizonte em duas regiões diferentes e Federando-as usando contador.A configuração de rede é como o exemplo anterior, com alguns links de região cruzada adicionais. 

Você precisará conectar o Entrada na Rede virtual do Azure a cada região para nuvens privadas/SDDCs na outra região, permitindo que os servidores de conexão de horizonte que fazem parte da Federação contador se conectem a todos os desktops sob gerenciamento.Adicionar nuvens/SDDCs privadas adicionais a essa configuração permitiria Dimensionar para 24.000 sessões em geral. 

Embora este exemplo mostre várias regiões, o mesmo princípio se aplicaria se você quiser implantar dois pods de horizonte na mesma região. Observe que você precisaria certificar-se de que o segundo pod de horizonte seja implantado em uma *rede virtual do Azure separada*.Por fim, assim como você faria no exemplo de Pod único anterior, você pode conectar sua rede corporativa e o Pod local a este exemplo multipod/região usando o cliente ExpressRoute e Alcance Global.

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="As diferenças entre o horizonte na solução VMware do Azure e na nuvem de horizonte no Azure" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Dimensionar hosts de solução do Azure VMware para implantações de horizonte 

A metodologia de dimensionamento do horizonte em um host em execução na solução VMware do Azure é mais simples do que o horizonte local, uma vez que a instância do host da solução Azure VMware é padronizada. O dimensionamento preciso do host ajudará a determinar o número de hosts necessários para dar suporte aos seus requisitos de VDI e é fundamental para determinar o custo por área de trabalho.

### <a name="azure-vmware-solution-host-instance"></a>Instância do host da solução Azure VMware

* Servidor PowerEdge R640-DSS restrito

* 36 núcleos \@ 2.3 GHz

* 576 GB DE RAM

* Controlador HBA SAS de 12 Gbps HBA330 (não RAID)

* a combinação de SATA SSD de 1,92 TB usa 6 Gbps 512 2,5 na unidade AG Hot-plug, 3 DWPD, 10512 TBW

* Intel 1,6 TB, NVMe, misto de uso expresso flash, unidade 2,5 SFF, U. 2, P4600 com operadora

* 2 grupos de discos vSAN: 1,6 x 4 (1.92 TB)

### <a name="horizon-sizing-inputs"></a>Entradas de dimensionamento de horizonte

Descubra o seguinte para sua carga de trabalho planejada:

* Número de áreas de trabalho simultâneas

* VCPU obrigatórios por desktop

* VRAM necessária por desktop

* Armazenamento necessário por desktop

Em geral, as implantações de VDI são limitadas por CPU ou RAM, uma vez que esses fatores determinarão o tamanho do host. Vamos pegar o exemplo a seguir para um tipo de carga de trabalho do LoginVSI Worker, validado com testes de desempenho:

* 2.000 implantação de área de trabalho simultânea

* 2vCPU por área de trabalho.

* 4 GB de vRAM por área de trabalho.

* 50 GB de armazenamento por área de trabalho

Para este exemplo, o número total de hosts de fatores para 18, produzindo uma densidade de VM por host de 111.

>[!IMPORTANT]
>As cargas de trabalho do cliente variam a partir deste exemplo de um trabalhador de conhecimento LoginVSI. Como parte do planejamento de sua implantação, trabalhe com o SEs do VMware EUC para suas necessidades específicas de dimensionamento e desempenho. Certifique-se de executar seus próprios testes de desempenho usando a carga de trabalho real planejada antes de finalizar o dimensionamento e ajuste do host de acordo.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Horizonte no licenciamento da solução VMware do Azure 

Há quatro componentes para os custos gerais da execução do horizonte na solução VMware do Azure. 

### <a name="azure-vmware-solution-capacity-cost"></a>Custo da capacidade da solução VMware do Azure

Para obter informações sobre os preços, consulte a página de [preços da solução VMware do Azure](https://azure.microsoft.com/pricing/details/azure-vmware/)

### <a name="horizon-licensing-cost"></a>Custo de licenciamento de horizonte

Há duas licenças disponíveis para uso com a solução Azure VMware, que pode ser um usuário simultâneo (CCU) ou um usuário nomeado (NU):

* Licença de assinatura de horizonte

* Licença de assinatura universal do horizonte

Se estiver implantando apenas o horizonte na solução VMware do Azure para o futuro próximo, use a licença de assinatura Horizonte, pois ele é um custo mais baixo.

Se estiver implantando o horizonte na solução VMware do Azure e no local, como com um caso de uso de recuperação de desastre, escolha a licença de assinatura universal do horizonte. A licença universal é um custo mais alto, pois inclui uma licença do vSphere para implantação local.

Trabalhe com sua equipe de vendas do VMware EUC para determinar o custo de licenciamento de horizonte com base em suas necessidades.

### <a name="cost-of-the-horizon-infrastructure-vms-on-azure-virtual-network"></a>Custo das VMs de infraestrutura de horizonte na rede virtual do Azure

Com base na arquitetura de implantação padrão, as VMs de infraestrutura de horizonte são constituídas de servidores de conexão, UAGs, gerenciadores de volume de aplicativo e são implantadas na rede virtual do Azure do cliente. Instâncias nativas adicionais do Azure são necessárias para dar suporte aos serviços de HA (alta disponibilidade), Microsoft SQL ou Microsoft Active Directory (AD) no Azure. Veja a seguir uma lista de instâncias do Azure com base em um exemplo de implantação de 2.000-desktop. 

| Componente de infraestrutura de horizonte | Instância do Azure | Número de instâncias necessárias (para 2.000-desktops)    | Comentário  |
|----------------------------------|----------------|----------------------------------------------------|----------|
| Servidor de conexão                | D4sv3          | 2       | *Inclui 1 instância para HA*             |    
| UAG                              | F2sv2          | 2       | *Inclui 1 instância para HA*             |
| Gerenciador de volumes de aplicativos              | D4sv3          | 2       | *Inclui 1 instância para HA*             |
| Conector de nuvem                  | D4sv3          | 1       |                                          |
| Controlador do AD                    | D4sv3          | 2       | *Opção para usar o serviço MSFT AD no Azure* |
| Banco de dados MS-SQL                  | D4sv3          | 2       | *Opção para usar o serviço SQL no Azure*     |
| Compartilhamento de arquivos do Windows               | D4sv3          |         | *Opcional*                               |

A VM de infraestrutura custa valores para \$ 0,36 por usuário por mês para a implantação de 2.000-desktop no exemplo acima. Observe que este exemplo usa o preço da instância do Azure no leste dos EUA a partir de junho de 2020. Seu preço pode variar dependendo da região, das opções selecionadas e do tempo.
