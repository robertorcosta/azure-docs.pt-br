---
title: Implantar o horizonte na solução VMware do Azure
description: Saiba como implantar o VMware horizonte na solução VMware do Azure.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 6a466aea5cbdf4452a2c46b455932042d920c3b9
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369005"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Implantar o horizonte na solução VMware do Azure 

>[!NOTE]
>Este documento se concentra no produto VMware Horizonte, anteriormente conhecido como horizonte 7. O horizonte é uma solução diferente da nuvem do horizonte no Azure, embora haja alguns componentes compartilhados. As principais vantagens da solução Azure VMware incluem um método de dimensionamento mais direto e a integração do gerenciamento do VMware Cloud Foundation ao portal do Azure.

O [VMware horizonte](https://www.vmware.com/products/horizon.html)®, uma plataforma de área de trabalho virtual e aplicativos, executado no Data Center e fornece gerenciamento simples e centralizado. Ele fornece áreas de trabalho virtuais e aplicativos em qualquer dispositivo, em qualquer lugar. O horizonte permite que você crie e agente conexões com áreas de trabalho virtuais do Windows e Linux, aplicativos hospedados do Área de Trabalho Remota Server (RDS), áreas de trabalho e computadores físicos.

Aqui, nos concentramos especificamente na implantação do horizonte na solução VMware do Azure. Para obter informações gerais sobre o VMware Horizonte, consulte a documentação de produção do horizonte:

* [O que é o VMware horizonte?](https://www.vmware.com/products/horizon.html)

* [Saiba mais sobre o VMware horizonte](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Arquitetura de referência de horizonte](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Com a introdução do horizonte na solução VMware do Azure, agora há duas soluções de VDI (Virtual Desktop Infrastructure) na plataforma Azure. O diagrama a seguir resume as principais diferenças em um alto nível.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Horizonte na solução VMware do Azure e na nuvem de horizonte no Azure" border="false":::

O horizonte 2006 e versões posteriores na linha de lançamento do horizonte 8 dão suporte à implantação local e à implantação da solução do Azure VMware. Há alguns recursos de horizonte com suporte no local, mas não na solução do Azure VMware. Também há suporte para produtos adicionais no ecossistema de horizonte. Para obter informações, consulte [paridade e interoperabilidade de recursos](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Implantar o horizonte em uma nuvem híbrida

Você pode implantar o horizonte em um ambiente de nuvem híbrida ao usar a contador (arquitetura de pod de nuvem do horizonte) para interconectar os data centers locais e do Azure. O contador dimensiona sua implantação, cria uma nuvem híbrida e fornece redundância para a continuidade dos negócios e a recuperação de desastres.  Para obter mais informações, consulte [expandindo ambientes existentes do horizonte 7](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>CONTADOR não é uma implantação ampliada; cada pod de horizonte é distinto e todos os servidores de conexão que pertencem a cada pods individual devem estar localizados em um único local e ser executados no mesmo domínio de transmissão de uma perspectiva de rede.

Como o data center local ou privado, o horizonte pode ser implantado em uma nuvem privada da solução Azure VMware. Discutiremos as principais diferenças na implantação do horizonte local e na solução Azure VMware nas seções a seguir.

A nuvem privada do Azure é conceitualmente a mesma do VMware SDDC, um termo normalmente usado na documentação do horizonte. O restante deste documento usa os termos a nuvem privada do Azure e o VMware SDDC intercambiáveis.

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

Cada nuvem privada do Azure e o SDDC podem lidar com sessões de desktop ou de aplicativo 4.000, supondo que:

* O tráfego de carga de trabalho se alinha com o perfil do operador de tarefa LoginVSI.

* Somente o tráfego de protocolo é considerado, sem dados de usuário.

* O NSX Edge está configurado para ser grande.

>[!NOTE]
>Seu perfil de carga de trabalho e suas necessidades podem ser diferentes e, portanto, os resultados podem variar com base no seu caso de uso. Os volumes de dados do usuário podem reduzir os limites de escala no contexto de sua carga de trabalho. Dimensione e planeje sua implantação de acordo. Para obter mais informações, consulte as diretrizes de dimensionamento na seção [tamanho hosts da solução do Azure VMware para implantações de horizonte](#size-azure-vmware-solution-hosts-for-horizon-deployments) .

Dada a nuvem privada do Azure e o limite máximo do SDDC, recomendamos uma arquitetura de implantação em que os servidores de conexão de horizonte e os UAGs (gateways de acesso unificado) da VMware estejam em execução dentro da rede virtual do Azure. Ele efetivamente transforma cada nuvem privada do Azure e o SDDC em um bloco. Por sua vez, maximizar a escalabilidade do horizonte em execução na solução VMware do Azure.

A conexão da rede virtual do Azure com as nuvens/SDDCs privadas do Azure deve ser configurada com o ExpressRoute FastPath. O diagrama a seguir mostra uma implantação de Pod básico.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Horizonte na solução VMware do Azure e na nuvem de horizonte no Azure" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Conectividade de rede para o horizonte de escala na solução VMware do Azure

Esta seção descreve a arquitetura de rede em um alto nível com alguns exemplos comuns de implantação para ajudá-lo a dimensionar o horizonte na solução VMware do Azure. O foco está especificamente em elementos críticos de rede. 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Pod de horizonte único na solução VMware do Azure

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Horizonte na solução VMware do Azure e na nuvem de horizonte no Azure" border="false":::

Um único pod de horizonte é o cenário de implantação mais direto, pois você implanta apenas um pod de horizonte na região leste dos EUA.  Como cada nuvem privada e o SDDC são estimados para lidar com 4.000 sessões de desktop, você implanta o tamanho máximo de pod de horizonte.  Você pode planejar a implantação de até três nuvens/SDDCs privadas.

Com as VMs (máquinas virtuais) de infraestrutura de horizonte implantadas na rede virtual do Azure, você pode alcançar as 12.000 sessões por pod de horizonte. A conexão entre cada nuvem privada e o SDDC para a rede virtual do Azure é o caminho rápido do ExpressRoute.  Não é necessário tráfego leste-oeste entre nuvens privadas. 

As principais suposições para esse exemplo de implantação básica incluem:

* Você não tem um pod de horizonte local que deseja conectar a esse novo pod usando a contador (arquitetura de pod de nuvem).

* Os usuários finais se conectam às suas áreas de trabalho virtuais por meio da Internet (vs. conectando por meio de um data center local).

Conecte seu controlador de domínio do AD na rede virtual do Azure com seu AD local por meio do circuito VPN ou ExpressRoute.

Uma variação no exemplo básico pode ser oferecer suporte à conectividade para recursos locais. Por exemplo, os usuários acessam áreas de trabalho e geram tráfego de aplicativo de área de trabalho virtual ou se conectam a um pod de horizonte local usando contador.

O diagrama mostra como dar suporte à conectividade para recursos locais. Para se conectar à rede corporativa à rede virtual do Azure, você precisará de um circuito do ExpressRoute.  Você também precisará conectar sua rede corporativa a cada uma das nuvem privada e SDDCs usando o ExpressRoute Alcance Global.  Ele permite a conectividade do SDDC para o circuito do ExpressRoute e recursos locais. 

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Horizonte na solução VMware do Azure e na nuvem de horizonte no Azure" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Vários pods de horizonte na solução do Azure VMware em várias regiões

Outro cenário é o horizonte de dimensionamento em vários pods.  Nesse cenário, você implanta dois pods de horizonte em duas regiões diferentes e federa-os usando contador. Ele é semelhante à configuração de rede no exemplo anterior, mas com alguns links entre regiões adicionais. 

Você conectará o Entrada na Rede virtual do Azure a cada região para nuvens privadas/SDDCs na outra região. Ele permite que os servidores de conexão de horizonte parte da Federação contador se conectem a todos os desktops sob gerenciamento. Adicionar nuvens/SDDCs privadas adicionais a essa configuração permitiria Dimensionar para 24.000 sessões em geral. 

Os mesmos princípios se aplicam se você implantar dois pods de horizonte na mesma região.  Certifique-se de implantar o segundo pod de horizonte em uma *rede virtual do Azure separada*. Assim como o único exemplo de Pod, você pode conectar sua rede corporativa e o Pod local a este exemplo multipod/região usando ExpressRoute e Alcance Global. 

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="Horizonte na solução VMware do Azure e na nuvem de horizonte no Azure" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Dimensionar hosts de solução do Azure VMware para implantações de horizonte 

A metodologia de dimensionamento do horizonte em um host em execução na solução VMware do Azure é mais simples do que o horizonte local.  Isso ocorre porque o host da solução Azure VMware está padronizado.  O dimensionamento exato do host ajuda a determinar o número de hosts necessários para dar suporte aos seus requisitos de VDI.  É fundamental determinar o custo por área de trabalho.

### <a name="azure-vmware-solution-host-instance"></a>Instância do host da solução Azure VMware

* Servidor PowerEdge R640-DSS restrito

* 36 núcleos \@ 2.3 GHz

* 576 GB DE RAM

* Controlador HBA SAS de 12 Gbps HBA330 (não RAID)

* a combinação de SATA SSD de 1,92 TB usa 6 Gbps 512 2,5 na unidade AG Hot-plug, 3 DWPD, 10512 TBW

* Intel 1,6 TB, NVMe, misto de uso expresso flash, unidade 2,5 SFF, U. 2, P4600 com operadora

* 2 grupos de discos vSAN: 1,6 x 4 (1.92 TB)

### <a name="horizon-sizing-inputs"></a>Entradas de dimensionamento de horizonte

Veja o que você precisará coletar para sua carga de trabalho planejada:

* Número de áreas de trabalho simultâneas

* VCPU obrigatórios por desktop

* VRAM necessária por desktop

* Armazenamento necessário por desktop

Em geral, as implantações de VDI são limitadas por CPU ou RAM, o que determina o tamanho do host. Vamos pegar o exemplo a seguir para um tipo de carga de trabalho do LoginVSI Worker, validado com testes de desempenho:

* 2.000 implantação de área de trabalho simultânea

* 2vCPU por área de trabalho.

* vRAM de 4 GB por área de trabalho.

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

Se implantado na solução do Azure VMware e no local, como com um caso de uso de recuperação de desastre, escolha a licença de assinatura universal do horizonte. Ele inclui uma licença do vSphere para implantação local, portanto, ele tem um custo mais alto.

Trabalhe com sua equipe de vendas do VMware EUC para determinar o custo de licenciamento de horizonte com base em suas necessidades.

### <a name="cost-of-the-horizon-infrastructure-vms-on-azure-virtual-network"></a>Custo das VMs de infraestrutura de horizonte na rede virtual do Azure

Com base na arquitetura de implantação padrão, as VMs de infraestrutura de horizonte são constituídas de servidores de conexão, UAGs, gerenciadores de volume de aplicativo. Eles são implantados na rede virtual do Azure do cliente. Instâncias nativas adicionais do Azure são necessárias para dar suporte aos serviços de HA (alta disponibilidade), Microsoft SQL ou Microsoft Active Directory (AD) no Azure. A tabela lista as instâncias do Azure com base em um exemplo de implantação 2.000-desktop. 

>[!NOTE]
>Para poder lidar com falhas, implante mais um servidor do que o necessário para o número de conexões (n + 1). O número mínimo recomendado de instâncias do servidor de conexão, UAG e Gerenciador de volumes de aplicativos é 2, e o número de necessário aumentará com base na quantidade de usuários que o ambiente dará suporte.  Um único servidor de conexão dá suporte a um máximo de 4.000 sessões, embora 2.000 seja recomendado como uma prática recomendada. Até sete servidores de conexão têm suporte por Pod, com uma recomendação de 12.000 sessões ativas no total por Pod. Para obter os números mais recentes, consulte o [artigo da base de dados de conhecimento do VMware sobre limites e recomendações de dimensionamento do VMware horizonte 7](https://kb.vmware.com/s/article/2150348).

| Componente de infraestrutura de horizonte | Instância do Azure | Número de instâncias necessárias (para 2.000-desktops)    | Comentário  |
|----------------------------------|----------------|----------------------------------------------------|----------|
| Servidor de conexão                | D4sv3          | 2       | *Consulte a observação acima*                         |    
| UAG                              | F2sv2          | 2       | *Consulte a observação acima*                         |
| Gerenciador de volumes de aplicativos              | D4sv3          | 2       | *Consulte a observação acima*                         |
| Conector de nuvem                  | D4sv3          | 1       |                                          |
| Controlador do AD                    | D4sv3          | 2       | *Opção para usar o serviço MSFT AD no Azure* |
| Banco de dados MS-SQL                  | D4sv3          | 2       | *Opção para usar o serviço SQL no Azure*     |
| Compartilhamento de arquivos do Windows               | D4sv3          |         | *Opcional*                               |

A VM de infraestrutura custa valores para \$ 0,36 por usuário por mês para a implantação de 2.000-desktop no exemplo acima. Este exemplo usa o preço da instância do Azure de junho de 2020 no leste dos EUA. Seu preço pode variar dependendo da região, das opções selecionadas e do tempo.
