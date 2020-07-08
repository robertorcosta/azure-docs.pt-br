---
title: Perguntas frequentes
description: Fornece respostas para algumas dúvidas comuns sobre a AVS (Solução VMware no Azure).
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 1649b5649bd18b7ab53f3cc0196d7dff0f6f5b2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84112698"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-avs-preview"></a>Perguntas frequentes sobre a versão prévia da AVS (Solução VMware no Azure)

Respostas a perguntas frequentes sobre a AVS (Solução VMware no Azure).

## <a name="general"></a>Geral

**O que é a AVS (Solução VMware no Azure)?**

À medida que as empresas buscam estratégias de modernização de TI para melhorar a agilidade dos negócios, reduzir custos e acelerar a inovação, as plataformas de nuvem híbrida despontaram como facilitadoras da transformação digital dos clientes. A AVS combina o SDDC (software do Data Center definido pelo software) do VMware com o ecossistema global de serviço de nuvem do Microsoft Azure. A solução AVS é gerenciada para atender aos requisitos de desempenho, disponibilidade, segurança e conformidade.

## <a name="avs-service"></a>Serviço AVS

**Onde a AVS está disponível atualmente?**

Durante a versão prévia, a AVS está disponível no Leste dos EUA, na América do Norte, e em Amsterdã, no Oeste da Europa.

**As cargas de trabalho em execução em uma instância da AVS (Solução VMware no Azure) consomem ou se integram aos serviços do Azure?**

Todos os serviços do Azure estarão disponíveis para clientes da solução de AVS. As limitações de desempenho e disponibilidade para serviços específicos precisarão ser tratadas caso a caso.

**Eu uso as mesmas ferramentas que utilizo atualmente para gerenciar recursos de nuvem privada?**

Sim. O portal do Azure é usado para implantação e várias operações de gerenciamento. O vCenter e o NSX Manager são usados para gerenciar os recursos vSphere e NSX-T.

**Posso gerenciar uma nuvem privada com meu vCenter local?**

Na inicialização, a AVS não dará suporte a uma experiência única de gerenciamento em ambientes locais e de nuvem privada. Os clusters de nuvem privada serão gerenciados com o vCenter e o NSX Manager local para uma nuvem privada.

**Posso usar o vRealize Suite em execução local?** 

As integrações específicas e os casos de uso podem ser avaliados caso a caso.

**Posso migrar as VMs vSphere de ambientes locais para nuvens privadas de AVS?**

Sim. A migração de VM e o vMotion poderão ser usados para mover VMs para uma nuvem privada se forem atendidos [os requisitos do vMotion][https://kb.vmware.com/s/article/210695 ] de padrão cruzado do vCenter.

**Uma versão específica do vSphere é necessária em ambientes locais?**

O vSphere 5.5 ou posterior em ambientes locais para o vMotion, já que todos os ambientes de nuvem vêm com o HCX.

**Como é o processo de controle de alterações?**

As atualizações feitas no próprio serviço seguirão o processo de gerenciamento de alterações padrão do Microsoft Azure. Os clientes são responsáveis por todas as tarefas de administração de carga de trabalho e pelos processos associados de gerenciamento de alterações.

**Qual é a diferença disso para a Solução VMware da CloudSimple no Azure?**

Com a nova Solução VMware no Azure, a Microsoft e a VMware têm uma parceria de provedor de serviços de nuvem. A nova solução é totalmente projetada, criada e suportada pela Microsoft e endossada pela VMware. Em termos de arquitetura, as soluções são consistentes, com a pilha de tecnologia do VMware em execução em uma infraestrutura dedicada do Azure.

**Se eu já for cliente da Solução VMware no Azure, o que essa versão prévia representará para mim?**

Não há qualquer alteração na Solução VMware da CloudSimple no Azure atual. Continuamos a dar suporte à solução no Azure. A Solução VMware no Azure se ampara em nosso [SLA](https://aka.ms/CSVMwareSLA) (contrato de nível de serviço). Os clientes devem continuar a usar o serviço para cargas de trabalho de produção. Esta é uma solução disponível regida pelos [Termos de Serviço da Microsoft](https://azure.microsoft.com/support/legal/).

**Posso migrar da Solução VMware da CloudSimple no Azure para essa nova solução?**

Sim, a Solução VMware no Azure dá suporte à migração usando ferramentas conhecidas de VMware, como HCX. Para os clientes interessados em migrar para a nova solução, procure a equipe da sua conta Microsoft para explorar opções e suporte disponíveis.



## <a name="compute-network-and-storage"></a>Computação, rede e armazenamento

**Existe mais de um tipo de host disponível?**

Existe apenas um tipo de host disponível.

**Quais são as especificações de CPU em cada tipo de host?**

Os servidores têm duas CPUs Intel com 18 núcleos de 2,3 GHz.

**Qual a quantidade de memória existente em cada host?**

Os servidores têm 576 GB de RAM.

**Qual é a capacidade de armazenamento de cada host?**

Cada host ESXi tem dois diskgroups VSAN com uma camada de capacidade de 15,2 TB e uma camada de cache NVMe de 3,2 TB (1,6 TB em cada um dos discos).

**Cada host ESXi possui quanto de largura de banda de rede disponível?**

Os hosts ESXi são compatíveis com uma largura de banda de conectividade de até 25 Gbps.

**Os dados são armazenados nos armazenamentos criptografados do VSAN em repouso?**

Sim, todos os dados VSAN são criptografados por padrão usando chaves armazenadas no Azure Key Vault.

## <a name="hosts-clusters-and-private-clouds"></a>Hosts, clusters e nuvens privadas

**A infraestrutura subjacente é compartilhada?**

Não, clusters e hosts de nuvem privada são dedicados e apagados com segurança antes e depois do uso.

**Quais são os números mínimo e máximo de hosts por cluster?**

Os clusters podem ser dimensionados entre 3 e 16 hosts ESXi. Os clusters de avaliação são limitados a três hosts.

**Posso dimensionar meus clusters de nuvem privada?**

Sim, os clusters são dimensionados entre o número mínimo e máximo de hosts ESXi. Os clusters de avaliação são limitados a três hosts.

**O que são clusters de avaliação?**

Os clusters de avaliação são três clusters de hosts usados para avaliações de um mês de nuvens privadas da AVS.

**Posso usar hosts de alto nível para clusters de avaliação?**

Não. Os hosts ESXi de alto nível são reservados para uso em clusters de produção.

## <a name="avs-and-vmware-software"></a>Software AVS e VMware

**Quais versões do software VMware são usadas em nuvens privadas?**

As nuvens privadas usam vSphere 6.7, vSAN 6.7, HCX e a versão 2.5 do NSX-T.  

**As nuvens privadas usam o VMware NSX?**

Sim, o NSX-T 2.5 é usado para a rede definida pelo software em nuvens privadas da AVS.

**Posso usar o VMware NSX-V em uma nuvem privada?**

Não. A única versão com suporte do NSX é a NSX-T.

**O NSX é necessário em ambientes locais ou redes que se conectam a uma nuvem privada?**

Não, não é necessário usar o NSX no local.

**O que é a atualização e o agendamento de atualização para o software VMware em uma nuvem privada?**

As atualizações do pacote de software de nuvem privada são feitas para manter o software dentro de uma versão da versão mais recente do pacote de software do VMware. As versões de software de nuvem privada podem ser diferentes das versões mais recentes dos componentes individuais de software (ESXi, NSX-T, vCenter, VSAN).

**Com que frequência a pilha de software de nuvem privada será atualizada?**

O software de nuvem privada é atualizado em um agendamento que acompanha o lançamento do pacote de software do VMware. Sua nuvem privada não exige tempo de inatividade para atualizações.

## <a name="connectivity"></a>Conectividade

**Que planejamento de endereço IP de rede é necessário para incorporar nuvens privadas a ambientes locais?**

Um espaço de endereço de rede privada /22 é necessário para implantar uma nuvem privada da AVS. Esse espaço de endereço privado não deve se sobrepor a outras redes virtuais em uma assinatura ou a redes locais.
 
**Como faço para me conectar de ambientes locais a uma nuvem privada da AVS?**

Você pode se conectar ao serviço de duas maneiras: 

- Com uma VM ou um gateway de aplicativo implantado em uma rede virtual do Azure que é emparelhada por meio do ExpressRoute com a nuvem privada.
- Por meio do Alcance Global ExpressRoute do datacenter local com um circuito ExpressRoute do Azure.

**Como faço para conectar uma VM de carga de trabalho à Internet ou a um ponto de extremidade de serviço do Azure?**

No portal do Azure, habilite a conectividade com a Internet para uma nuvem privada. Com o NSX-T Manager, crie um roteador NSX-T T1 e um comutador lógico. Em seguida, use o vCenter para implantar uma VM no segmento de rede definido pelo comutador lógico. Essa VM terá acesso de rede à Internet e aos serviços do Azure.

**Preciso restringir o acesso da Internet a VMs em redes lógicas em uma nuvem privada?**

Não. Não é permitido o tráfego de rede de entrada da Internet diretamente para nuvens privadas.

**Preciso restringir o acesso à Internet das VMs em redes lógicas?**

Sim. Você precisará usar o NSX-T Manager para criar um firewall que restringe o acesso da VM à Internet.

## <a name="accounts-and-privileges"></a>Contas e privilégios

**Quais contas e privilégios obterei com minha nova nuvem privada da AVS?**

Você recebe credenciais para um usuário cloudadmin no vCenter e acesso de administrador no NSX-T Manager. Também há um grupo CloudAdmin que pode ser usado para incorporar o Azure AD (Azure Active Directory). Confira mais informações em [Conceitos de acesso e identidade](concepts-identity.md).

**Posso ter acesso de administrador a hosts ESXi?**

Não, o acesso de administrador ao ESXi é restrito para atender aos requisitos de segurança da solução.

**Quais privilégios e permissões eu tenho no vCenter?**

Você terá privilégios de grupo do CloudAdmin. Confira mais informações em [Conceitos de acesso e identidade](concepts-identity.md).

**Quais privilégios e permissões eu terei no NSX-T Manager?**

Você terá privilégios totais de administrador no NSX-T e poderá gerenciar o controle de acesso baseado em função da mesma maneira que faria com o Data Center do NSX-T local. Confira mais informações em [Conceitos de acesso e identidade](concepts-identity.md).

> [!NOTE]
> Um roteador T0 é criado e configurado como parte de uma implantação de nuvem privada. Toda modificação no roteador lógico ou nas VMs do nó de borda do NSX-T pode afetar a conectividade com sua nuvem privada.

## <a name="billing-and-support"></a>Cobrança e suporte

**Como serei cobrado durante a versão prévia da AVS?**

Durante a versão prévia, a cobrança pela AVS é mensal e conforme o uso. Outras opções estarão disponíveis na disponibilidade geral.

**Como os preços serão estruturados durante a versão prévia da AVS?**

Para perguntas gerais sobre preços, confira a página de [preços](https://azure.microsoft.com/pricing/details/azure-vmware) da Solução VMware no Azure. Os preços da versão prévia estão disponíveis na solicitação. Entre em contato com sua equipe de conta ou siga o link na página de preços para entrar em contato com o setor de vendas.

**Quem dá suporte à AVS?**

O suporte para AVS é fornecido pela Microsoft. Observe que, de acordo com nossas diretrizes de versão prévia, o suporte será dado durante o horário comercial, das 9 às 17h, de segunda a sexta-feira. Você pode criar um Tíquete de suporte usando [este link](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

**De que conta eu preciso para criar uma nuvem privada da AVS?**

Você precisará de uma conta do Azure em uma assinatura do Azure.

<a name="how-to-request-a-quota-increase-for-avs"></a>**Como fazer solicitar um aumento de cota de host para a solução do Azure VMware?**

Você pode solicitar um aumento de cota [enviando uma solicitação de suporte](..\azure-portal\supportability\how-to-create-azure-support-request.md). A equipe de Gerenciamento de Cotas avalia a solicitação e a aprova dentro de três dias úteis.  

> [!IMPORTANT]
> Antes de poder solicitar um aumento de cota, certifique-se de registrar o **provedor de recursos** Microsoft.AVS no portal do Azure.  
> ```azurecli-interactive
> az provider register -n Microsoft.AVS --subscription <your subscription ID>
> ```
> Confira outras maneiras de registrar o provedor de recursos em [Provedores e tipos de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

1. No portal do Azure, em **Ajuda e Suporte**, crie uma **Nova solicitação de suporte** e forneça as seguintes informações para o tíquete:
   - **Tipo de problema:** técnico
   - **Assinatura:** o ID da sua assinatura
   - **Serviço:**  Solução VMware no Azure 
   - **Resumo:** aumento da cota
   - **Tipo de problema:** Problemas de Gerenciamento de Capacidade
   - **Subtipo do problema:** solicitação do cliente para cota/capacidade de host adicional

1. Na Descrição do tíquete de suporte, na guia Detalhes, informe:
   - O número de nós adicionais   
   - A SKU do nó
   - A região

   > [!NOTE] 
   > Por padrão, será concedido um mínimo de quatro nós.

1. Clique em **Revisar e Criar** para enviar a solicitação.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
