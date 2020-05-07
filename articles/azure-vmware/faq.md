---
title: Perguntas frequentes
description: Fornece respostas para algumas das perguntas mais comuns sobre a solução de VMware do Azure (AVS).
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 6d8e87dd52871b82109ccc794af04244efe95b06
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854608"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-avs-preview"></a>Perguntas frequentes sobre a versão prévia da solução VMware do Azure (AVS)

Respostas para perguntas frequentes sobre a solução de VMware do Azure (AVS).

## <a name="general"></a>Geral

**O que é a AVS (solução do Azure VMware)?**

À medida que as empresas buscam estratégias de modernização de ti para melhorar a agilidade dos negócios, reduzir custos e acelerar a inovação, as plataformas de nuvem híbrida surgiram como habilitadores-chave da transformação digital dos clientes. O AVS combina o software do Data Center (SDDC) definido pelo software VMware com Microsoft Azure ecossistema global de serviço de nuvem. A solução AVS é gerenciada para atender aos requisitos de desempenho, disponibilidade, segurança e conformidade.

## <a name="avs-service"></a>Serviço AVS

**Onde a AVS está disponível hoje?**

Durante a versão prévia, ele está disponível no leste dos EUA em América do Norte e em Amsterdã na Europa Ocidental.

**As cargas de trabalho em execução em uma instância da AVS (solução VMware) do Azure consomem ou se integram aos serviços do Azure?**

Todos os serviços do Azure estarão disponíveis para clientes da solução de AVS. As limitações de desempenho e disponibilidade para serviços específicos precisarão ser abordadas caso a caso.

**Eu uso as mesmas ferramentas que uso agora para gerenciar recursos de nuvem privada?**

Sim. O portal do Azure é usado para implantação e várias operações de gerenciamento. o vCenter e o NSX Manager são usados para gerenciar os recursos vSphere e NSX-T.

**Posso gerenciar uma nuvem privada com meu vCenter local?**

Na inicialização, a AVS não dará suporte a uma única experiência de gerenciamento em ambientes de nuvem privada e locais. Os clusters de nuvem privada serão gerenciados com o vCenter e o NSX Manager local para uma nuvem privada.

**Posso usar o vRealize Suite em execução local?** 

Integrações específicas e casos de uso podem ser avaliados caso a caso.

**Posso migrar VMs vSphere de ambientes locais para nuvens privadas de AVS?**

Sim. A migração de VM e o vMotion podem ser usados para mover VMs para uma nuvem privada se o vCenter padrão cruzadohttps://kb.vmware.com/s/article/210695[requisitos do VMotion] [] for atendido.

**Uma versão específica do vSphere é necessária em ambientes locais?**

Como todos os ambientes de nuvem vêm com o HCX, o vSphere 5,5 ou posterior em ambientes locais para o vMotion.

**Qual é a aparência do processo de controle de alterações?**

As atualizações feitas no próprio serviço seguirão Microsoft Azure processo de gerenciamento de alterações padrão. Os clientes são responsáveis por qualquer tarefa de administração de carga de trabalho e pelos processos de gerenciamento de alterações associados.

**Como isso é diferente da solução do Azure VMware por CloudSimple?**

Com a nova solução Azure VMware, a Microsoft e a VMware têm uma parceria de provedor de nuvem direta. A nova solução é totalmente projetada, criada e suportada pela Microsoft e endossada pela VMware. Em termos de arquitetura, as soluções são consistentes, com a pilha de tecnologia do VMware em execução em uma infraestrutura dedicada do Azure.

**Se eu for um cliente de solução do Azure VMware existente, o que essa visualização significa para mim?**

Não há nenhuma alteração na solução do Azure VMware existente por CloudSimple. Continuamos a dar suporte à solução no Azure. A solução Azure VMware é apoiada por nosso [SLA (contrato de nível de serviço)](https://aka.ms/CSVMwareSLA). Os clientes devem continuar a usar o serviço para cargas de trabalho de produção; Essa é uma solução disponível governada pelos [termos de serviço da Microsoft](https://azure.microsoft.com/support/legal/).

**Posso migrar da solução do Azure VMware por CloudSimple para essa nova solução?**

Sim, a solução Azure VMware dá suporte à migração usando ferramentas de VMware familiares, como HCX. Para clientes interessados em migrar para a nova solução, trabalhe com sua equipe de conta Microsoft para explorar opções e suporte disponível.

## <a name="compute-network-and-storage"></a>Computação, rede e armazenamento

**Há mais de um tipo de host disponível?**

Há apenas um tipo de host disponível.

**Quais são as especificações de CPU em cada tipo de host?**

Os servidores têm duas CPUs Intel com 18 núcleos de 2,3 GHz.

**Qual a quantidade de memória em cada host?**

Os servidores têm 576 GB de RAM.

**Qual é a capacidade de armazenamento de cada host?**

Cada host ESXi tem dois diskgroups VSAN com uma camada de capacidade de 15,2 TB e uma camada de cache NVMe de 3,2 TB (1,6 TB em cada um dos discos).

**Quanta largura de banda de rede está disponível em cada host ESXi?**

Os hosts ESXi dão suporte à largura de banda de conectividade de até 25 Gbps.

**Os dados são armazenados nos armazenamentos do VSAN criptografados em repouso?**

Sim, todos os dados VSAN são criptografados por padrão usando chaves armazenadas em Azure Key Vault.

## <a name="hosts-clusters-and-private-clouds"></a>Hosts, clusters e nuvens privadas

**A infraestrutura subjacente está compartilhada?**

Não, hosts de nuvem privada e clusters são dedicados e apagados com segurança antes e depois do uso.

**Quais são os números mínimo e máximo de hosts por cluster?**

Os clusters podem ser dimensionados entre 3 e 16 hosts ESXi. Os clusters de avaliação são limitados a três hosts.

**Posso dimensionar meus clusters de nuvem privada?**

Sim, os clusters são dimensionados entre o número mínimo e máximo de hosts ESXi. Os clusters de avaliação são limitados a três hosts.

**O que são clusters de avaliação?**

Os clusters de avaliação são três clusters de hosts usados para avaliações de um mês de nuvens privadas de AVS.

**Posso usar hosts de alta-extremidade para clusters de avaliação?**

Não. Os hosts ESXi de topo de linha são reservados para uso em clusters de produção.

## <a name="avs-and-vmware-software"></a>Software AVS e VMware

**Quais versões do software VMware são usadas em nuvens privadas?**

Nuvens privadas usam vSphere 6,7, vSAN 6,7, HCX e versão 2,5 do NSX-T.  

**As nuvens privadas usam o VMware NSX?**

Sim, o NSX-T 2,5 é usado para a rede definida pelo software em nuvens privadas da AVS.

**Posso usar o VMware NSX-V em uma nuvem privada?**

Não. O NSX-T é a única versão com suporte do NSX.

**O NSX é necessário em ambientes locais ou redes que se conectam a uma nuvem privada.**

Não, você não precisa usar o NSX no local.

**O que é a atualização e o agendamento de atualização para o software VMware em uma nuvem privada?**

As atualizações do pacote de software de nuvem privada são feitas para manter o software dentro de uma versão da versão mais recente do formulário de pacote de software VMware. As versões de software de nuvem privada podem ser diferentes das versões mais recentes dos componentes de software individuais (ESXi, NSX-T, vCenter, VSAN).

**Com que frequência a pilha de software de nuvem privada será atualizada?**

O software de nuvem privada é atualizado em um agendamento que acompanha o lançamento do pacote de software do VMware. Sua nuvem privada não exige tempo de inatividade para atualizações.

## <a name="connectivity"></a>Conectividade

**Que planejamento de endereço IP de rede é necessário para incorporar nuvens privadas a ambientes locais?**

Um espaço de endereço de rede privada/22 é necessário para implantar uma nuvem privada de AVS. Esse espaço de endereço privado não deve se sobrepor a outras redes virtuais em uma assinatura ou com redes locais.
 
**Como fazer conectar-se de ambientes locais a uma nuvem privada de AVS?**

Você pode se conectar ao serviço em um dos dois métodos: 

- Com uma VM ou um gateway de aplicativo implantado em uma rede virtual do Azure que é emparelhada por meio do ExpressRoute para a nuvem privada.
- Por meio do ExpressRoute Alcance Global do datacenter local para um circuito de ExpressRoute do Azure.

**Como fazer conectar uma VM de carga de trabalho à Internet ou a um ponto de extremidade de serviço do Azure?**

No portal do Azure, habilite a conectividade com a Internet para uma nuvem privada. Com o NSX-T Manager, crie um roteador NSX-T T1 e um comutador lógico. Em seguida, use o vCenter para implantar uma VM no segmento de rede definido pelo comutador lógico. Essa VM terá acesso de rede à Internet e aos serviços do Azure.

**Preciso restringir o acesso da Internet a VMs em redes lógicas em uma nuvem privada?**

Não. O tráfego de rede de entrada da Internet diretamente para nuvens privadas não é permitido.

**Preciso restringir o acesso à Internet de VMs em redes lógicas à Internet?**

Sim. Você precisará usar o Gerenciador de NSX-T para criar um firewall que restringe o acesso da VM à Internet.

## <a name="accounts-and-privileges"></a>Contas e privilégios

**Quais contas e privilégios obterei com minha nova nuvem privada de AVS?**

Você recebe credenciais para um usuário cloudadmin no vCenter e acesso de administrador no Gerenciador de NSX-T. Também há um grupo CloudAdmin que pode ser usado para incorporar Azure Active Directory. Para obter mais informações, consulte [conceitos de acesso e identidade](concepts-identity.md).

**Pode ter acesso de administrador a hosts ESXi?**

Não, o acesso de administrador ao ESXi é restrito para atender aos requisitos de segurança da solução.

**Quais privilégios e permissões eu tenho no vCenter?**

Você terá privilégios de grupo de CloudAdmin. Para obter mais informações, consulte [conceitos de acesso e identidade](concepts-identity.md).

**Quais privilégios e permissões eu tenho no Gerenciador do NSX-T?**

Você terá privilégios totais de administrador no NSX-T e poderá gerenciar o controle de acesso baseado em função como faria com o Data Center do NSX-T local. Para obter mais informações, consulte [conceitos de acesso e identidade](concepts-identity.md).

> [!NOTE]
> Um roteador de T0 é criado e configurado como parte de uma implantação de nuvem privada. Qualquer modificação no roteador lógico ou nas VMs do nó de borda do NSX-T pode afetar a conectividade com sua nuvem privada.

## <a name="billing-and-support"></a>Cobrança e suporte

**Como serei cobrado durante a visualização da AVS**

A cobrança por AVS durante a visualização é mensal em uma base paga conforme o uso. Opções adicionais estarão disponíveis em disponibilidade geral.

**Como os preços serão estruturados durante a visualização da AVS?**

Para perguntas gerais sobre preços, consulte a página de [preços](https://azure.microsoft.com/pricing/details/azure-vmware) da solução VMware do Azure. Os preços da versão prévia estão disponíveis na solicitação, entre em contato com sua equipe de conta ou siga o link na página de preços para entrar em contato com as vendas.

**Quem dá suporte a AVS?**

O suporte para AVS é fornecido pela Microsoft. Observe que, de acordo com nossas diretrizes de visualização, forneceremos suporte durante as horas de expediente de 9 a 5 PM, de segunda a sexta-feira. Você pode gerar um tíquete de suporte a partir [deste link](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

**Quais contas eu preciso para criar uma nuvem privada de AVS?**

Você precisará de uma conta do Azure em uma assinatura do Azure.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
