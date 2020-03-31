---
title: Sobre a recuperação de desastres para aplicativos locais com o Azure Site Recovery
description: Descreve as cargas de trabalho que podem ser protegidas usando a recuperação de desastres com o serviço do Azure Site Recovery.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062832"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Sobre a recuperação de desastre para aplicativos locais

Este artigo descreve cargas de trabalho e aplicativos no local que você pode proteger para a recuperação de desastres com o serviço [azure Site Recovery.](site-recovery-overview.md)

## <a name="overview"></a>Visão geral

As organizações precisam de uma estratégia de continuidade de negócios e recuperação de desastres (BCDR) para manter as cargas de trabalho e os dados seguros e disponíveis durante o tempo de inatividade planejado e não planejado. E, recuperar-se às condições regulares de trabalho.

O Site Recovery é um serviço do Azure que contribui para sua estratégia de BCDR. Usando o Site Recovery, você pode implantar a replicação com reconhecimento de aplicativo na nuvem, ou para um site secundário. Você pode usar o Site Recovery para gerenciar a replicação, executar testes de recuperação de desastres e executar failovers e failback. Seus aplicativos podem ser executados em computadores windows ou Linux, servidores físicos, VMware ou Hyper-V.

A Recuperação do Site integra-se aos aplicativos da Microsoft, como SharePoint, Exchange, Dynamics, SQL Server e Active Directory. A Microsoft trabalha em estreita colaboração com os principais fornecedores, incluindo Oracle, SAP e Red Hat. Você pode personalizar soluções de replicação em uma base por aplicativo.

## <a name="why-use-site-recovery-for-application-replication"></a>Por que usar o Site Recovery para a replicação do aplicativo?

O Site Recovery contribui para a recuperação e a proteção no nível do aplicativo da seguinte maneira:

- App-agnóstico e fornece replicação para quaisquer cargas de trabalho em execução em uma máquina suportada.
- Replicação quase síncrona, com RPO (Recovery Point Objectives, objetivos de ponto de recuperação) tão baixos quanto 30 segundos para atender às necessidades dos aplicativos de negócios mais críticos.
- Instantâneos consistentes de aplicativos para os aplicativos simples ou multicamadas.
- Integração com o SQL Server AlwaysOn e parceria com outras tecnologias de replicação em nível de aplicativo. Por exemplo, a replicação do Active Directory, o SQL AlwaysOn e os DAGs (Exchange Database Availability Groups, grupos de disponibilidade de banco de dados do exchange).
- Planos de recuperação flexíveis que permitem recuperar uma pilha inteira de aplicativos com um único clique e incluir scripts externos e ações manuais no plano.
- Gerenciamento avançado de rede em Recuperação de Site e Azure para simplificar os requisitos da rede de aplicativos. Gerenciamento de rede, como a capacidade de reservar endereços IP, configurar o balanceamento de carga e a integração com o Azure Traffic Manager para switchovers de rede de baixo tempo de recuperação (RTO).
- Uma biblioteca de automação avançada que fornece scripts específicos do aplicativo, prontos para a produção, que pode ser baixada e integrada nos planos de recuperação.

## <a name="workload-summary"></a>Resumo de carga de trabalho

O Site Recovery pode replicar qualquer aplicativo em execução em um computador com suporte. Fizemos parcerias com equipes de produtos para fazer testes adicionais para os aplicativos especificados na tabela a seguir.

| **Carga de trabalho** |**Replicar VMs do Azure para o Azure** |**Replicar máquinas virtuais do Hyper-V para um site secundário** | **Replicar VMs do Hyper-V para o Azure** | **Replicar VMs do VMware para um site secundário** | **Replicar VMs do VMware para o Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Sim |Sim |Sim |Sim |Sim|
| Aplicativos Web (IIS, SQL) |Sim |Sim |Sim |Sim |Sim|
| System Center Operations Manager |Sim |Sim |Sim |Sim |Sim|
| SharePoint |Sim |Sim |Sim |Sim |Sim|
| SAP<br/><br/>Replicar site SAP para Azure para não cluster |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft)|
| Exchange (não DAG) |Sim |Sim |Sim |Sim |Sim|
| Área de Trabalho Remota/VDI |Sim |Sim |Sim |Sim |Sim|
| Linux (sistema operacional e aplicativos) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft)|
| Dynamics AX |Sim |Sim |Sim |Sim |Sim|
| Servidor de arquivos do Windows |Sim |Sim |Sim |Sim |Sim|
| Citrix XenApp e XenDesktop |Sim|N/D |Sim |N/D |Sim |

## <a name="replicate-active-directory-and-dns"></a>Replicar o Active Directory e o DNS

As infraestruturas do DNS e do Active Directory são essenciais para a maioria dos aplicativos corporativos. Durante a recuperação de desastres, você precisará proteger e recuperar esses componentes de infra-estrutura, antes de recuperar cargas de trabalho e aplicativos.

Você usar o Site Recovery para criar um plano totalmente automatizado de recuperação de desastre para o Active Directory e o DNS. Por exemplo, para falhar sobre o SharePoint e o SAP de um site primário para um local secundário, você pode configurar um plano de recuperação que primeiro falha sobre o Active Directory. Em seguida, use um plano de recuperação específico do aplicativo adicional para falhar sobre os outros aplicativos que dependem do Active Directory.

[Saiba mais](site-recovery-active-directory.md) sobre a recuperação de desastres para Active Directory e DNS.

## <a name="protect-sql-server"></a>Proteger o SQL Server

O SQL Server fornece uma base de serviços de dados para muitos aplicativos de negócios em um datacenter local. A recuperação do site pode ser usada com as tecnologias SQL Server HA/DR, para proteger aplicativos corporativos de várias camadas que usam o SQL Server.

A Recuperação de Site fornece:

- Uma solução de recuperação de desastre simples e econômica para o SQL Server. Replique diversas versões e edições dos clusters e servidores autônomos do SQL Server para o Azure ou um site secundário.
- Integração com Grupos de Disponibilidade SQL AlwaysOn para gerenciar o failover e o failback com os planos de recuperação do Azure Site Recovery.
- Planos de recuperação completos para todas as camadas em um aplicativo, incluindo os bancos de dados do SQL Server.
- Dimensionamento do SQL Server para cargas máximas com recuperação de site, _explodindo-os_ em tamanhos maiores de máquinas virtuais IaaS no Azure.
- Teste fácil de recuperação de desastres do SQL Server. Você pode executar failovers de teste para analisar os dados e executar verificações de conformidade sem afetar seu ambiente de produção.

[Saiba mais](site-recovery-sql.md) sobre a recuperação de desastres para servidor SQL.

## <a name="protect-sharepoint"></a>Proteger o SharePoint

O Azure Site Recovery ajuda a proteger as implantações do SharePoint, da seguinte forma:

- Elimina a necessidade e os custos associados à infraestrutura de um farm autônomo para a recuperação de desastres. Use a Recuperação de Sites para replicar uma fazenda inteira (níveis web, app e banco de dados) para o Azure ou para um site secundário.
- Simplifica a implantação e o gerenciamento de aplicativos. As atualizações implantadas no site principal são replicadas automaticamente. As atualizações estão disponíveis após failover e recuperação de uma fazenda em um local secundário. Reduz a complexidade de gestão e os custos associados à manutenção de uma fazenda de stand-by atualizada.
- Simplifica o desenvolvimento de aplicativos do SharePoint e teste criando um ambiente de réplica sob demanda com cópia de produção para o teste e a depuração.
- Simplifica a transição para a nuvem usando o Site Recovery para migrar as implantações do SharePoint para o Azure.

[Saiba mais](site-recovery-sharepoint.md) sobre a recuperação de desastres no SharePoint.

## <a name="protect-dynamics-ax"></a>Proteger o Dynamics AX

O Azure Site Recovery ajuda a proteger sua solução ERP do Dynamics AX:

- Gerenciamento de replicação de todo o ambiente Dynamics AX (níveis Web e AOS, níveis de banco de dados, SharePoint) para o Azure ou para um site secundário.
- Simplificando a migração das implantações do Dynamics AX para a nuvem (Azure).
- Simplificando o desenvolvimento de aplicativos Dynamics AX e teste criando uma cópia de produção sob demanda para o teste e a depuração.

[Saiba mais](site-recovery-dynamicsax.md) sobre a recuperação de desastres para OX Dinâmico.

## <a name="protect-remote-desktop-services"></a>Proteger serviços de desktop remotos

O Remote Desktop Services (RDS) permite a infra-estrutura de desktop virtual (VDI), desktops baseados em sessão e aplicativos, que permitem que os usuários trabalhem em qualquer lugar.

Com o Azure Site Recovery, você pode replicar os seguintes serviços:

- Replique desktops virtuais gerenciados ou não gerenciados em um site secundário.
- Replique aplicativos e sessões remotas em um site secundário ou No Azure.

A tabela a seguir mostra as opções de replicação:

| **Rds** |**Replicar VMs do Azure para o Azure** | **Replicar máquinas virtuais do Hyper-V para um site secundário** | **Replicar VMs do Hyper-V para o Azure** | **Replicar VMs do VMware para um site secundário** | **Replicar VMs do VMware para o Azure** | **Replicar servidores físicos para um site secundário** | **Replicar servidores físicos para o Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Área de trabalho virtual em pool (não gerenciada)** |Não|Sim |Não |Sim |Não |Sim |Não |
| **Área de trabalho virtual em pool (gerenciada e sem UPD)** |Não|Sim |Não |Sim |Não |Sim |Não |
| **Aplicativos remotos e sessões da área de trabalho (sem UDP)** |Sim|Sim |Sim |Sim |Sim |Sim |Sim |

[Saiba mais](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) sobre a recuperação de desastres para RDS.

## <a name="protect-exchange"></a>Proteger o Exchange

O Site Recovery ajuda a proteger o Exchange da seguinte maneira:

- Para implantações pequenas do Exchange, como um servidor único ou autônomo, o Site Recovery pode replicar e fazer failover no Azure ou em um site secundário.
- Para as implantações maiores, o Site Recovery integra-se ao Exchange DAGS.
- Os Exchange DAGs são a solução recomendada para recuperação de desastre do Exchange em uma empresa.  Os planos de recuperação do Site Recovery podem incluir DAGs para coordenar o failover do DAG nos sites.

Para saber mais sobre a recuperação de desastres para o Exchange, consulte [Exchange DAGs](/Exchange/high-availability/database-availability-groups/database-availability-groups) e [Exchange recovery](/Exchange/high-availability/disaster-recovery/disaster-recovery).

## <a name="protect-sap"></a>Proteger o SAP

Use o Site Recovery para proteger sua implantação do SAP como a seguir:

- Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver Production em execução no local, por meio da replicação de componentes no Azure.
- Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver Production em execução no Azure, por meio da replicação de componentes em outro datacenter do Azure.
- Simplifique a migração na nuvem usando o Site Recovery para migrar sua implantação do SAP para o Azure.
- Simplifique as atualizações, teste e criação de protótipos de projeto SAP criando um clone de produção sob demanda para testar aplicativos SAP.

[Saiba mais](site-recovery-sap.md) sobre a recuperação de desastres para SAP.

## <a name="protect-internet-information-services"></a>Proteger serviços de informações da Internet

Use a recuperação do site para proteger a implantação do IIS (Internet Information Services, serviços de informações da Internet) da seguinte forma:

O Azure Site Recovery oferece recuperação de desastre replicando os componentes essenciais de seu ambiente em um site remoto frio ou em uma nuvem pública, como o Microsoft Azure. Uma vez que as máquinas virtuais com o servidor web e o banco de dados são replicadas para o site de recuperação, não há necessidade de um backup separado para arquivos de configuração ou certificados. As associações e mapeamentos de aplicativo dependentes de variáveis de ambiente que são alterados após o failover podem ser atualizados por meio de scripts integrados aos planos de recuperação de desastre. As máquinas virtuais são criadas no local de recuperação apenas durante um failover. O Azure Site Recovery também ajuda você a orquestrar o failover de ponta a ponta, fornecendo-lhe os seguintes recursos:

- O desligamento e a inicialização de máquinas virtuais nas várias camadas.
- Adicionando scripts para permitir atualizações de dependências de aplicativos e vinculações nas máquinas virtuais depois de iniciadas. Os scripts também podem ser usados para atualizar o servidor DNS a fim de apontar para o local de recuperação.
- Aloque endereços IP para máquinas virtuais pré-failover mapeando as redes primária e de recuperação e use scripts que não precisam ser atualizados após failover.
- Capacidade para um failover de um clique para vários aplicativos da Web que elimina o escopo de confusão durante um desastre.
- A capacidade de testar os planos de recuperação em um ambiente isolado para exercícios de recuperação de desastre.

[Saiba mais](site-recovery-iis.md) sobre a recuperação de desastres para o IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Proteger o Citrix XenApp e o XenDesktop

Use o Site Recovery para proteger suas implantações Citrix XenApp e XenDesktop, da seguinte maneira:

- Habilite a proteção da implantação citrix XenApp e XenDesktop. Replique as diferentes camadas de implantação ao Azure: Active Directory, servidor DNS, servidor de banco de dados SQL, Citrix Delivery Controller, StoreFront server, XenApp Master (VDA), Citrix XenApp License Server.
- Simplifique a migração na nuvem usando o Site Recovery para migrar sua implantação do Citrix XenApp e XenDesktop para o Azure.
- Simplifique o teste do Citrix XenApp/XenDesktop criando uma cópia de produção sob demanda para teste e depuração.
- Essa solução só se aplica a desktops virtuais do Windows Server e não a desktops virtuais clientes. Os desktops virtuais do cliente ainda não têm suporte para licenciamento no Azure. [Saiba mais](https://azure.microsoft.com/pricing/licensing-faq/) sobre licenciamento para áreas de trabalho de cliente/servidor no Azure.

[Saiba mais](site-recovery-citrix-xenapp-and-xendesktop.md) sobre a recuperação de desastres para implantações citrix XenApp e XenDesktop. Ou, você pode se referir ao [whitepaper Citrix](https://aka.ms/citrix-xenapp-xendesktop-with-asr).

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](azure-to-azure-quickstart.md) sobre a recuperação de desastres para uma VM Azure.
