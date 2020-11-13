---
title: Perguntas frequentes
description: Fornece respostas para algumas das perguntas mais comuns sobre a solução do Azure VMware.
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: dikamath
ms.openlocfilehash: 99e9e53693e42f907250331894f55ed0160f9e30
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577508"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution"></a>Perguntas frequentes sobre a solução do Azure VMware

Respostas para perguntas frequentes sobre a solução do Azure VMware.

## <a name="general"></a>Geral

#### <a name="what-is-azure-vmware-solution"></a>O que é a Solução VMware no Azure?

À medida que as empresas buscam estratégias de modernização de ti para melhorar a agilidade dos negócios, reduzir custos e acelerar a inovação, as plataformas de nuvem híbrida surgiram como habilitadores-chave da transformação digital dos clientes. A solução Azure VMware combina o software Software-Defined Data Center (SDDC) do VMware com Microsoft Azure ecossistema global de serviço de nuvem. A solução VMware do Azure é gerenciada para atender aos requisitos de desempenho, disponibilidade, segurança e conformidade.

## <a name="azure-vmware-solution-service"></a>Serviço de solução VMware do Azure

#### <a name="where-is-azure-vmware-solution-available-today"></a>Onde a solução Azure VMware está disponível hoje?

O serviço está sendo adicionado continuamente a novas regiões, portanto, exiba as [informações mais recentes de disponibilidade do serviço](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) para obter mais detalhes. 

#### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>As cargas de trabalho em execução em uma instância de solução do Azure VMware consomem ou se integram aos serviços do Azure?

Todos os serviços do Azure estarão disponíveis para os clientes da solução Azure VMware. As limitações de desempenho e disponibilidade para serviços específicos precisarão ser tratadas caso a caso.

#### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Eu uso as mesmas ferramentas que utilizo atualmente para gerenciar recursos de nuvem privada?

Sim. O portal do Azure é usado para implantação e várias operações de gerenciamento. O vCenter e o NSX Manager são usados para gerenciar os recursos vSphere e NSX-T.

#### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>Posso gerenciar uma nuvem privada com meu vCenter local?

Na inicialização, a solução Azure VMware não oferecerá suporte a uma única experiência de gerenciamento em ambientes de nuvem privada e locais. Os clusters de nuvem privada serão gerenciados com o vCenter e o NSX Manager local para uma nuvem privada.

#### <a name="can-i-use-vrealize-suite-running-on-premises"></a>Posso usar o vRealize Suite em execução local? 

As integrações específicas e os casos de uso podem ser avaliados caso a caso.

#### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>Posso migrar VMs vSphere de ambientes locais para nuvens privadas da solução Azure VMware?

Sim. A migração de VM e o vMotion podem ser usados para mover VMs para uma nuvem privada se [os requisitos](https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=2106952) do vCenter VMotion padrão forem atendidos.

#### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>Uma versão específica do vSphere é necessária em ambientes locais?

Todos os ambientes de nuvem vêm com o VMware HCX, o vSphere 5,5 ou posterior em ambientes locais para o vMotion.

#### <a name="what-does-the-change-control-process-look-like"></a>Como é o processo de controle de alterações?

As atualizações feitas no próprio serviço seguirão Microsoft Azure processo de gerenciamento de alterações padrão. Os clientes são responsáveis por todas as tarefas de administração de carga de trabalho e pelos processos associados de gerenciamento de alterações.

#### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Qual é a diferença disso para a Solução VMware da CloudSimple no Azure?

Com a nova Solução VMware no Azure, a Microsoft e a VMware têm uma parceria de provedor de serviços de nuvem. A nova solução é totalmente projetada, criada e suportada pela Microsoft e endossada pela VMware. Em termos de arquitetura, as soluções são consistentes, com a pilha de tecnologia do VMware em execução em uma infraestrutura dedicada do Azure.



#### <a name="can-azure-vmware-solution-vms-be-managed-by-vmrc"></a>As VMs de solução do Azure VMware podem ser gerenciadas pelo VMRC?
Sim, desde que o sistema no qual ele está instalado possa acessar a nuvem privada vCenter e esteja usando o DNS público para resolver nomes de host ESXi.

#### <a name="are-there-special-instructions-for-installing-and-using-vmrc-with-azure-vmware-solution-vms"></a>Há instruções especiais para instalar e usar o VMRC com VMs de solução do Azure VMware?
Não, use as [instruções fornecidas pelo VMware](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-89E7E8F0-DB2B-437F-8F70-BA34C505053F.html) e realize os pré-requisitos de VM especificados nessas instruções. 

#### <a name="is-vmware-hcx-supported-on-vpns"></a>O VMware HCX tem suporte em VPNs?
Não, devido aos requisitos de largura de banda e latência.

#### <a name="can-azure-bastion-be-used-for-connecting-to-azure-vmware-solution-vms"></a>A bastiões do Azure pode ser usada para conectar-se às VMs da solução Azure VMware?
A bastiões do Azure é o serviço recomendado para se conectar à caixa de salto para evitar a exposição da solução Azure VMware à Internet. Você não pode usar a bastiões do Azure para se conectar às VMs da solução Azure VMware, pois elas não são objetos IaaS do Azure.

#### <a name="can-azure-load-balancer-internal-be-used-for-azure-vmware-solution-vms"></a>Pode Azure Load Balancer ser usado internamente para VMs da solução do Azure VMware?
Não. Azure Load Balancer interno só dá suporte a VMs IaaS do Azure. Azure Load Balancer não dá suporte a pools de back-end baseados em IP; somente VMs do Azure ou objetos do conjunto de dimensionamento de máquinas virtuais nas quais as VMs de solução do Azure VMware não são objetos do Azure.

#### <a name="can-an-existing-expressroute-gateway-be-used-to-connect-to-azure-vmware-solution"></a>Um gateway de ExpressRoute existente pode ser usado para se conectar à solução do Azure VMware?
Sim, você pode usar um gateway de ExpressRoute existente para se conectar à solução do Azure VMware, desde que não exceda o limite de quatro circuitos de ExpressRoute por rede virtual.  No entanto, para acessar a Solução VMware no Azure do local por meio do ExpressRoute, você precisa ter o Alcance Global do ExpressRoute, pois o gateway do ExpressRoute não fornece roteamento transitivo entre os circuitos conectados.

## <a name="compute-network-storage-and-backup"></a>Computação, rede, armazenamento e backup

#### <a name="is-there-more-than-one-type-of-host-available"></a>Existe mais de um tipo de host disponível?

Existe apenas um tipo de host disponível.

#### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Quais são as especificações de CPU em cada tipo de host?

Os servidores têm duas CPUs Intel com 18 núcleos de 2,3 GHz.

#### <a name="how-much-memory-is-in-each-host"></a>Qual a quantidade de memória existente em cada host?

Os servidores têm 576 GB de RAM.

#### <a name="what-is-the-storage-capacity-of-each-host"></a>Qual é a capacidade de armazenamento de cada host?

Cada host ESXi tem dois diskgroups vSAN com uma camada de capacidade de 15,2 TB e uma camada de cache NVMe de 3,2 TB (1,6 TB em cada um dos discos).

#### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Cada host ESXi possui quanto de largura de banda de rede disponível?

Cada host ESXi na solução VMware do Azure é configurado com NICs de 4 25 Gbps, duas NICs provisionadas para o tráfego do sistema ESXi e duas NICs provisionadas para o tráfego de carga de trabalho. 

#### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>Os dados são armazenados nos armazenamentos do vSAN criptografados em repouso?

Sim, todos os dados vSAN são criptografados por padrão usando chaves armazenadas em Azure Key Vault.

#### <a name="you-document-that-commvault-veritas-and-veeam-have-extended-their-backup-solutions-to-work-with-azure-vmware-solution-what-about-other-independent-software-vendor-isv-backup-solutions"></a>Você documenta que a CommVault, a VERITAS e a Veeam ampliaram suas soluções de backup para trabalhar com a solução Azure VMware. E quanto a outras soluções de backup de ISV (fornecedor independente de software)?

Até o que sabemos, qualquer solução de backup que usa o VMware VADP com o modo de transporte HotAdd deve funcionar imediatamente na solução Azure VMware.

#### <a name="what-about-support-for-isv-backup-solutions"></a>E quanto ao suporte para soluções de backup de ISV?

Como essas soluções de backup são instaladas e gerenciadas pelos clientes, elas podem entrar em contato com o respectivo ISV para obter suporte. 

#### <a name="what-is-the-correct-storage-policy-for-the-dedupe-setup"></a>Qual é a política de armazenamento correta para a configuração de eliminação de duplicação?

Use a política de armazenamento *thin_provision* para o modelo de VM.  O padrão é *thick_provision*.

#### <a name="are-the-snmp-infrastructure-logs-shared"></a>Os logs de infraestrutura SNMP são compartilhados?

Não.

## <a name="hosts-clusters-and-private-clouds"></a>Hosts, clusters e nuvens privadas

#### <a name="is-the-underlying-infrastructure-shared"></a>A infraestrutura subjacente é compartilhada?

Não, clusters e hosts de nuvem privada são dedicados e apagados com segurança antes e depois do uso.

#### <a name="what-are-the-minimum-and-the-maximum-number-of-hosts-per-cluster"></a>Qual é o número mínimo e máximo de hosts por cluster?

Os clusters podem ser dimensionados entre 3 e 16 hosts ESXi. Os clusters de avaliação são limitados a três hosts.

#### <a name="can-i-scale-my-private-cloud-clusters"></a>Posso dimensionar meus clusters de nuvem privada?

Sim, os clusters são dimensionados entre o mínimo e o número máximo de hosts ESXi. Os clusters de avaliação são limitados a três hosts.

#### <a name="what-are-trial-clusters"></a>O que são clusters de avaliação?

Os clusters de avaliação são três clusters de hosts usados para avaliações de um mês de nuvens privadas da solução Azure VMware.

#### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>Posso usar hosts de alto nível para clusters de avaliação?

Não. Os hosts ESXi de alto nível são reservados para uso em clusters de produção.

## <a name="azure-vmware-solution-and-vmware-software"></a>Solução VMware do Azure e software VMware

#### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>Quais versões do software VMware são usadas em nuvens privadas?

Nuvens privadas usam vSphere 6,7, vSAN 6,7, VMware HCX e versão 2,5 do NSX-T.  

#### <a name="do-private-clouds-use-vmware-nsx"></a>As nuvens privadas usam o VMware NSX?

Sim, o NSX-T 2,5 é usado para a rede definida pelo software nas nuvens privadas da solução Azure VMware.

#### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>Posso usar o VMware NSX-V em uma nuvem privada?

Não. A única versão com suporte do NSX é a NSX-T.

#### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>O NSX é necessário em ambientes locais ou redes que se conectam a uma nuvem privada?

Não, não é necessário usar o NSX no local.

#### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>O que é a atualização e o agendamento de atualização para o software VMware em uma nuvem privada?

As atualizações do pacote de software de nuvem privada são feitas para manter o software dentro de uma versão da versão mais recente do pacote de software do VMware. As versões de software de nuvem privada podem ser diferentes das versões mais recentes dos componentes de software individuais (ESXi, NSX-T, vCenter, vSAN).

#### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Com que frequência a pilha de software de nuvem privada será atualizada?

O software de nuvem privada é atualizado em um agendamento que controla a versão do pacote de software do VMware. Sua nuvem privada não exige tempo de inatividade para atualizações.

## <a name="connectivity"></a>Conectividade

#### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>Que planejamento de endereço IP de rede é necessário para incorporar nuvens privadas a ambientes locais?

Um espaço de endereço de rede privada/22 é necessário para implantar uma nuvem privada da solução Azure VMware. Esse espaço de endereço privado não deve se sobrepor a outras redes virtuais em uma assinatura ou com redes locais.
 
#### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>Como fazer conectar-se de ambientes locais a uma nuvem privada da solução Azure VMware?

Você pode se conectar ao serviço de duas maneiras: 

- Com uma VM ou um gateway de aplicativo implantado em uma rede virtual do Azure que é emparelhada por meio do ExpressRoute com a nuvem privada.
- Por meio do ExpressRoute Alcance Global do seu data center local para um circuito do Azure ExpressRoute.

#### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>Como faço para conectar uma VM de carga de trabalho à Internet ou a um ponto de extremidade de serviço do Azure?

No portal do Azure, habilite a conectividade com a Internet para uma nuvem privada. Com o NSX-T Manager, crie um roteador NSX-T T1 e um comutador lógico. Em seguida, use o vCenter para implantar uma VM no segmento de rede definido pelo comutador lógico. Essa VM terá acesso de rede à Internet e aos serviços do Azure.

#### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>Preciso restringir o acesso da Internet a VMs em redes lógicas em uma nuvem privada?

Não. Não é permitido o tráfego de rede de entrada da Internet diretamente para nuvens privadas.

#### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>Preciso restringir o acesso à Internet das VMs em redes lógicas?

Sim. Você precisará usar o NSX-T Manager para criar um firewall que restringe o acesso da VM à Internet.


#### <a name="can-azure-vmware-solution-use-azure-virtual-wan-hosted-expressroute-gateways"></a>A solução do Azure VMware pode usar gateways de ExpressRoute hospedados da WAN virtual do Azure?
Sim.

#### <a name="can-transit-connectivity-be-established-between-on-premises-and-azure-vmware-solution-through-azure-virtual-wan-over-expressroute-global-reach"></a>A conectividade de trânsito pode ser estabelecida entre a solução local e a VMware do Azure por meio da WAN virtual por meio do ExpressRoute Alcance Global?
A WAN virtual do Azure não fornece roteamento transitivo entre dois circuitos de ExpressRoute conectados e um gateway de ExpressRoute de WAN não virtual. O uso do ExpressRoute Alcance Global permite a conectividade entre a solução local e a VMware do Azure, mas passa pela rede global da Microsoft em vez do hub de WAN virtual.


## <a name="accounts-and-privileges"></a>Contas e privilégios

#### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Quais contas e privilégios serão obtidos com minha nova nuvem privada da solução Azure VMware?

Você recebe credenciais para um usuário cloudadmin no vCenter e acesso de administrador no NSX-T Manager. Também há um grupo CloudAdmin que pode ser usado para incorporar o Azure AD (Azure Active Directory). Confira mais informações em [Conceitos de acesso e identidade](concepts-identity.md).

#### <a name="can-have-administrator-access-to-esxi-hosts"></a>Posso ter acesso de administrador a hosts ESXi?

Não, o acesso de administrador ao ESXi é restrito para atender aos requisitos de segurança da solução.

#### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Quais privilégios e permissões eu tenho no vCenter?

Você terá privilégios de grupo do CloudAdmin. Confira mais informações em [Conceitos de acesso e identidade](concepts-identity.md).

#### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Quais privilégios e permissões eu terei no NSX-T Manager?

Você terá privilégios totais de administrador no NSX-T e poderá gerenciar o controle de acesso baseado em função da mesma maneira que faria com o Data Center do NSX-T local. Confira mais informações em [Conceitos de acesso e identidade](concepts-identity.md).

> [!NOTE]
> Um roteador T0 é criado e configurado como parte de uma implantação de nuvem privada. Toda modificação no roteador lógico ou nas VMs do nó de borda do NSX-T pode afetar a conectividade com sua nuvem privada.

## <a name="billing-and-support"></a>Cobrança e suporte

#### <a name="how-will-pricing-be-structured-for-azure-vmware-solution"></a>Como os preços serão estruturados para a solução VMware do Azure?

Para perguntas gerais sobre preços, consulte a página de [preços](https://azure.microsoft.com/pricing/details/azure-vmware) da solução VMware do Azure. 

#### <a name="can-azure-vmware-solution-be-purchased-through-a-microsoft-csp"></a>A solução do Azure VMware pode ser adquirida por meio de um Microsoft CSP?

Sim, os clientes podem implantar a solução do Azure VMware em uma assinatura do Azure gerenciada por um CSP.

#### <a name="who-supports-azure-vmware-solution"></a>Quem dá suporte à solução Azure VMware?

A Microsoft oferece suporte à solução Azure VMware. Você pode enviar uma [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). 

Para assinaturas gerenciadas pelo CSP, o primeiro nível de suporte será fornecido pelo provedor de solução da mesma maneira que o CSP faz para outros serviços do Azure.

#### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Quais contas eu preciso para criar uma nuvem privada da solução Azure VMware?

Você precisará de uma conta do Azure em uma assinatura do Azure.

#### <a name="are-red-hat-solutions-supported-on-azure-vmware-solution"></a>As soluções Red Hat têm suporte na solução VMware do Azure?

A Microsoft e a Red Hat compartilham uma equipe de suporte integrada e colocalizada que fornece um ponto de contato unificado para ecossistemas Red Hat em execução na plataforma Azure.  Assim como outros serviços da plataforma Azure que funcionam com o Red Hat Enterprise Linux, a solução Azure VMware está sob o acesso à nuvem e a abrangência de suporte integrado, e Red Hat Enterprise Linux tem suporte para execução sobre a solução Azure VMware no Azure.

#### <a name="is-vmware-hcx-enterprise-edition-available-and-if-so-how-much-does-it-cost"></a>O VMware HCX Enterprise Edition está disponível e, nesse caso, o quanto custa?

O VMware HCX EE (Edição Enterprise) está disponível com a Solução VMware no Azure como uma função ou um serviço de *Versão prévia*. Embora a solução VMware HCX EE para Azure VMware esteja em versão prévia, ela é uma função/serviço gratuita e está sujeita a termos e condições de serviço de visualização. Depois que o serviço VMware HCX EE ficar em GA, você receberá um aviso de 30 dias de que a cobrança será iniciada. Você pode desativar ou recusar o serviço.

#### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Como fazer solicitar um aumento de cota de host para a solução do Azure VMware?

Para assinaturas gerenciadas pelo CSP, o cliente deve executar a solicitação para o parceiro e a equipe de parceiros entrará na Microsoft para obter a cota aumentada para a assinatura. 

Para assinaturas EA, o cliente deve seguir este procedimento.

* Você precisará de um [ea (Enterprise Agreement do Azure)](../cost-management-billing/manage/ea-portal-agreements.md) com a Microsoft.
* Você precisará de uma conta do Azure em uma assinatura do Azure.

Antes de criar o recurso da solução Azure VMware, você deve enviar um tíquete de suporte para alocar seus nós. Leva até cinco dias úteis para confirmar sua solicitação e alocar seus nós. Se você tiver uma nuvem privada da Solução VMware no Azure existente e quiser mais nós alocados, deverá passar pelo mesmo processo.


1. Em seu portal do Azure, em **ajuda + suporte** , crie uma **[nova solicitação de suporte](https://rc.portal.azure.com/#create/Microsoft.Support)** e forneça as seguintes informações para o tíquete:
   - **Tipo de problema:** técnico
   - **Assinatura:** Selecione sua assinatura
   - **Serviço:** Todos os serviços > solução VMware do Azure
   - **Recurso:** Pergunta geral 
   - **Resumo:** Capacidade necessária
   - **Tipo de problema:** Problemas de Gerenciamento de Capacidade
   - **Subtipo do problema:** solicitação do cliente para cota/capacidade de host adicional

1. Na **Descrição** do tíquete de suporte, na guia **detalhes** , forneça:

   - POC ou produção 
   - Nome da Região
   - Número de nós
   - Quaisquer outros detalhes

   >[!NOTE]
   >A solução Azure VMware recomenda um mínimo de três nós para criar sua nuvem privada e para redundância de N + 1 nós. 

1. Selecione **revisão + criar** para enviar a solicitação.

   Levará até cinco dias úteis para que um representante de suporte confirme sua solicitação.

   >[!IMPORTANT] 
   >Se você já tiver uma solução existente do Azure VMware e estiver solicitando nós adicionais, observe que precisamos de cinco dias úteis para alocar os nós. 

1. Antes de provisionar seus nós, certifique-se de registrar o provedor de recursos **Microsoft. AVS** no portal do Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>

   For additional ways to register the resource provider, see [Azure resource providers and types](../azure-resource-manager/management/resource-providers-and-types.md). 

#### Are Reserved Instances available for purchasing through the Cloud Solution Provider (CSP) program?

Yes. CSP can purchase reserved instances on behalf of their customers, see [Save costs with a reserved instance](reserved-instance.md) article. 


## Customer communication

#### How can I receive an alert when Azure sends service health notifications to my Azure subscription?

Service issues, planned maintenance, health advisories, security advisories notifications are published through **Service Health** in the Azure portal.  You can take timely actions when you set up activity log alerts for these notifications. For more information, see [Create service health alerts using the Azure portal](../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal).

:::image type="content" source="media/service-health.png" alt-text="Screenshot of Service Health notifications":::



<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=21069522

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md