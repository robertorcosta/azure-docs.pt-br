---
title: O SQL Server em máquinas virtuais do Windows na FAQ do Azure | Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes sobre a execução do SQL Server em VMs do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: c3b4fabb319a3ea76ee62c8c699d4613184a4e76
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791051"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Perguntas freqüentes sobre o SQL Server em execução em máquinas virtuais do Windows no Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Este artigo fornece respostas para algumas das perguntas mais comuns sobre como executar [SQL Server no Windows máquinas virtuais no Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Este artigo se concentra em problemas específicos ao SQL Server em VMs Windows. Se você estiver executando o SQL Server em VMs Linux, consulte as [Perguntas frequentes sobre o Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Imagens

1. **Quais imagens da galeria de máquinas virtuais do SQL Server estão disponíveis?** 

   O Azure mantém imagens de máquina virtual para todas as versões principais compatíveis do SQL Server em todas as edições para o Windows e para o Linux. Para obter mais informações, consulte a lista completa dos [imagens de VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) e [imagens de VM do Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **As imagens da galeria de máquinas virtuais do SQL Server existentes são atualizadas?**

   A cada dois meses, as imagens do SQL Server na galeria de máquinas virtuais são atualizadas com as atualizações mais recentes do Windows e do Linux. Para imagens do Windows, isso inclui quaisquer atualizações marcadas como importantes no Windows Update, incluindo service packs e atualizações de segurança do SQL Server. Para imagens do Linux, isso inclui as atualizações mais recentes do sistema. As atualizações cumulativas do SQL Server são tratadas de maneira diferente para o Linux e para o Windows. Para o Linux, as atualizações cumulativas do SQL Server também são incluídas na atualização. Mas, neste momento, as VMs Windows não são atualizadas com as atualizações cumulativas do SQL Server ou do Windows Server.

1. **As imagens de máquina virtual do SQL Server podem ser removidas da galeria?**

   Sim. O Azure mantém apenas uma imagem por versão principal e edição. Por exemplo, quando um novo service pack do SQL Server é lançado, o Azure adiciona uma nova imagem à galeria para esse service pack. A imagem do SQL Server para o service pack anterior é removida imediatamente do Portal do Azure. No entanto, ela ainda estará disponível para provisionamento do PowerShell pelos próximos três meses. Depois de três meses, a imagem do service pack anterior não estará mais disponível. Essa política de remoção também se aplica quando uma versão do SQL Server se torna incompatível quando ela atinge o final de seu ciclo de vida.


1. **É possível implantar uma imagem mais antiga do SQL Server que não esteja visível na portal do Azure?**

   Sim, usando o PowerShell. Para obter mais informações sobre como implantar VMs do SQL Server usando o PowerShell, confira [Como provisionar máquinas virtuais do SQL Server com o Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Posso criar uma imagem generalizada do Azure SQL Server Marketplace da minha VM SQL Server e usá-la para implantar VMs?**

   Sim, mas você deve [registrar cada vm SQL Server com o provedor de recursos de vm SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) para gerenciar sua vm de SQL Server no portal, bem como utilizar recursos como aplicação de patch automatizada e backups automáticos. Ao registrar com o provedor de recursos, também será necessário especificar o tipo de licença para cada VM SQL Server. 

1. **Posso usar meu próprio VHD para implantar uma VM SQL Server?**

   Sim, mas você deve [registrar cada vm SQL Server com o provedor de recursos de vm SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) para gerenciar sua vm de SQL Server no portal, bem como utilizar recursos como aplicação de patch automatizada e backups automáticos.

1. **É possível definir configurações não mostradas na galeria de máquinas virtuais (por exemplo, Windows 2008 R2 + SQL Server 2012)?**

   Não. Para imagens da galeria de máquinas virtuais que incluem o SQL Server, você deve selecionar uma das imagens fornecidas, seja pelo portal do Azure ou via [PowerShell](virtual-machines-windows-ps-sql-create.md). No entanto, você pode implantar uma VM do Windows e a autoinstalação SQL Server a ela. Em seguida, você deve [registrar sua vm SQL Server com o provedor de recursos de vm SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) para gerenciar sua VM SQL Server no portal, bem como utilizar recursos como aplicação de patch automatizada e backups automáticos. 


## <a name="creation"></a>Criação

1. **Como criar uma máquina virtual do Azure com o SQL Server?**

   O método mais fácil é criar uma máquina virtual que inclua SQL Server. Para obter um tutorial sobre como se inscrever no Azure e criar uma VM SQL Server no portal, consulte [provisionar uma máquina virtual SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). Selecione uma imagem de máquina virtual que usa o licenciamento do SQL Server pago por segundo ou use uma imagem que permite trazer sua própria licença do SQL Server. Você também tem a opção de instalação manual do SQL Server em uma VM com um uma edição licenciada gratuitamente (Desenvolvedor ou Express) ou pela reutilização de uma licença local. Certifique-se de [registrar sua VM de SQL Server com o provedor de recursos de vm SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) para gerenciar sua vm de SQL Server no portal, bem como utilizar recursos como aplicação de patch automatizada e backups automáticos. Se você trouxer sua própria licença, será necessário ter o [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Para obter mais informações, consulte [Diretrizes de preço para VMs do Azure do SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Como migrar meu banco de dados do SQL Server local para a nuvem?**

   Primeiro, crie uma máquina virtual do Azure com uma instância do SQL Server. Em seguida, migre os bancos de dados locais para essa instância. Para obter estratégias de migração de dados, confira [Migrar um banco de dados do SQL Server para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licenciamento

1. **Como instalar minha cópia licenciada do SQL Server em uma VM do Azure?**

   Há três maneiras de fazer isso. Se você for um cliente do EA (Enterprise Agreement), poderá provisionar uma das [imagens de máquina virtual que dá suporte a licenças](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), que também é conhecida como BYOL (traga sua própria licença). Se você tiver o [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default), poderá habilitar o [benefício híbrido do Azure](virtual-machines-windows-sql-ahb.md) em uma imagem PAYG (pré-pago) existente. Ou você pode copiar a mídia de instalação do SQL Server para uma VM do Windows Server e, em seguida, instalar SQL Server na VM. Certifique-se de registrar sua VM SQL Server com o [provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md) para recursos como gerenciamento de portal, backup automatizado e aplicação de patch automatizada. 

1. **É necessário pagar para licenciar o SQL Server em uma VM do Azure se ela está sendo usada somente para espera/failover?**

   Para ter uma licença passiva gratuita para um grupo de disponibilidade secundário em espera ou instância clusterizada de failover, você deve atender a todos os critérios a seguir, conforme descrito no [Guia de licenciamento PDF](https://download.microsoft.com/download/7/8/C/78CDF005-97C1-4129-926B-CE4A6FE92CF5/SQL_Server_2017_Licensing_guide.pdf):

   1. Você tem o [License Mobility](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) por meio do [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. A instância de SQL Server passiva não atende SQL Server dados aos clientes ou executa cargas de trabalho do Active SQL Server. Ele é usado apenas para sincronizar com o servidor primário e, caso contrário, manter o banco de dados passivo em um estado de espera passiva. Se ele estiver servindo dados, como relatórios para clientes que executam cargas de trabalho do Active SQL Server, ou se executar qualquer "operação", como backups adicionais do servidor secundário, ele deverá ser uma instância paga de SQL Server licenciada. 
   1. A licença do Active SQL Server é coberta pelo Software Assurance e permite **uma** instância de SQL Server secundário passiva, com até a mesma quantidade de computação que o servidor ativo licenciado, apenas. 
   1. A VM de SQL Server secundária utiliza o [modelo de licença](virtual-machines-windows-sql-ahb.md)BYOL (traga sua própria licença) ou benefício híbrido do Azure (AHB). 

1. **Posso alterar uma VM para usar minha própria licença do SQL Server se ela foi criada com base em uma das imagens pré-pagas da galeria?**

   Sim. Você pode alternar facilmente uma imagem de galeria pré-paga (PAYG) para trazer sua própria licença (BYOL) habilitando o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)...  Para obter mais informações, confira [Como alterar o modelo de licenciamento para uma VM do SQL Server](virtual-machines-windows-sql-ahb.md). Atualmente, esse recurso está disponível somente para clientes de nuvem pública.

1. **Modelos de licenciamento de comutação exigirá nenhum tempo de inatividade para o SQL Server?**

   Não. [A alteração do modelo de licenciamento](virtual-machines-windows-sql-ahb.md) não requer tempo de inatividade para o SQL Server, pois a alteração entrará em vigor imediatamente e não exige a reinicialização da VM. No entanto, para registrar sua VM SQL Server com o provedor de recursos de VM SQL Server, a [extensão de IaaS do SQL](virtual-machines-windows-sql-server-agent-extension.md) é um pré-requisito e a instalação da extensão SQL IaaS no modo _completo_ reinicia o serviço SQL Server. Assim, se a extensão de IaaS do SQL precisar ser instalada, instale-a no modo _leve_ para funcionalidade limitada ou instale-a em modo _completo_ durante uma janela de manutenção. A extensão IaaS do SQL instalada no modo _leve_ pode ser atualizada para o modo _completo_ a qualquer momento, mas requer uma reinicialização do serviço SQL Server. 

1. **Posso usar o portal do Azure para gerenciar várias instâncias na mesma VM?**

   Não. O gerenciamento de portal é um recurso fornecido pelo provedor de recursos de VM SQL Server, que se baseia na extensão do agente IaaS SQL Server. Assim, as mesmas limitações se aplicam ao provedor de recursos para a extensão. O portal pode gerenciar apenas uma instância padrão ou uma instância nomeada, desde que tenha sido configurada corretamente. Para obter mais informações sobre essas limitações, consulte [SQL Server extensão do agente IaaS](virtual-machines-windows-sql-server-agent-extension.md). 

1. **Assinaturas de CSP podem ativar o benefício híbrido do Azure?**

   Sim, o Benefício Híbrido do Azure está disponível para assinaturas de CSP. Os clientes do CSP devem primeiro implantar uma imagem de pago conforme o uso e, em seguida, [alterar o modelo de licenciamento](virtual-machines-windows-sql-ahb.md) para traga sua própria licença (BYOL).

1. **O registro da minha VM com o novo SQL Server provedor de recursos da VM trará custos adicionais?**

   Não. O provedor de recursos de VM SQL Server apenas habilita a capacidade de gerenciamento adicional para SQL Server na VM do Azure sem encargos adicionais. 

1. **O provedor de recursos de VM SQL Server está disponível para todos os clientes?**
 
   Sim, contanto que a VM SQL Server tenha sido implantada na nuvem pública usando o modelo do Resource Manager e não o modelo clássico. Todos os outros clientes podem se registrar no novo SQL Server provedor de recursos de VM. No entanto, somente os clientes com o benefício do [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) podem usar sua própria licença ativando o [benefício híbrido do Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) em uma VM SQL Server. 

1. **O que acontecerá com o recurso de provedor de recursos (_Microsoft. SqlVirtualMachine_) se o recurso da VM for movido ou descartado?** 

   Quando o recurso Microsoft.Compute / VirtualMachine é descartado ou movido, o recurso Microsoft.SqlVirtualMachine associado é notificado para replicar de maneira assíncrona a operação.

1. **O que acontecerá com a VM se o recurso do provedor de recursos (_Microsoft. SqlVirtualMachine_) for descartado?**

    O recurso Microsoft.Compute / Virtual Machine não é afetado quando o recurso Microsoft.Sql Virtual Machine é descartado. No entanto, as alterações de licenciamento serão padronizadas de volta para a origem da imagem original. 

1. **É possível registrar VMs de SQL Server implantadas automaticamente com o provedor de recursos de VM SQL Server?**

    Sim. Se você implantou o SQL Server a partir da sua própria mídia e instalou a extensão do SQL IaaS, é possível registrar sua VM do SQL com o provedor de recursos para obter os benefícios de capacidade de gerenciamento fornecidos pela extensão SQL IaaS. No entanto, você não pode converter uma VM de SQL Server implantada para o pré-pago.

1. **É possível alternar o modelo de licenciamento em uma VM SQL Server implantada usando o modelo clássico?**

   Não. Não há suporte para a alteração do modelo de licenciamento em uma VM clássica. Você pode migrar sua VM para o modelo de Azure Resource Manager e registrar com o provedor de recursos de VM SQL Server. Depois que a VM estiver registrada com o provedor de recursos de VM SQL Server, as alterações no modelo de licenciamento estarão disponíveis na VM. 
   


## <a name="administration"></a>Administração

1. **Posso instalar uma segunda instância do SQL Server na mesma VM? Posso alterar os recursos instalados da instância padrão?**

   Sim. A mídia de instalação do SQL Server está localizada em uma pasta na unidade **C** . Execute **Setup.exe** nessa localização para adicionar novas instâncias do SQL Server ou para alterar outros recursos instalados do SQL Server no computador. Observe que alguns recursos, como o backup automatizado, a aplicação de patch automatizada e a integração de Azure Key Vault, operam apenas na instância padrão ou uma instância nomeada que foi configurada corretamente (consulte a pergunta 3). 

1. **Posso desinstalar a instância padrão do SQL Server?**

   Sim, mas há algumas considerações. Primeiro, a cobrança SQL Server associada pode continuar a ocorrer dependendo do modelo de licença da VM. Em segundo lugar, conforme mencionado na resposta anterior, há recursos que dependem da [extensão do agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Se você desinstalar a instância padrão sem remover a extensão IaaS também, a extensão continuará a procurar a instância padrão e poderá gerar erros de log de eventos. Esses erros são provenientes das duas seguintes fontes: **Gerenciamento de Credenciais do Microsoft SQL Server** e **Microsoft SQL Server IaaS Agent**. Um dos erros pode ser semelhante ao seguinte:

      Ocorreu um erro relacionado à rede ou específico da instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível.

   Se você optar por desinstalar a instância padrão, desinstale também a [Extensão do SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md). 

1. **Posso usar uma instância nomeada do SQL Server com a extensão IaaS**?
   
   Sim, se a instância nomeada for a única na SQL Server e se a instância padrão original tiver sido [desinstalada corretamente](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance). Se não houver nenhuma instância padrão e houver várias instâncias nomeadas em uma única VM SQL Server, a extensão do agente IaaS SQL Server não será instalada. 

1. **Posso remover completamente SQL Server de uma VM SQL Server?**

   Sim, mas você continuará a ser cobrado por sua VM SQL Server, conforme descrito em [diretrizes de preços para SQL Server VMs do Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Se você não precisar mais do SQL Server, você poderá implantar uma nova máquina virtual e migrar os dados e aplicativos para a nova máquina virtual. Em seguida, você pode remover a máquina virtual do SQL Server.
   
## <a name="updating-and-patching"></a>Atualização e aplicação de patch

1. **Como atualizar para uma nova versão/edição do SQL Server em uma VM do Azure?**

   Os clientes podem alterar sua versão/edição do SQL Server usando a mídia de instalação que contém a versão desejada ou a edição do SQL Server. Depois que a edição for alterada, use o portal do Azure para modificar a propriedade de edição da VM para refletir com precisão a cobrança da VM. Para obter mais informações, consulte [Change Edition of a VM SQL Server](virtual-machines-windows-sql-change-edition.md). Não há nenhuma diferença de cobrança para versões diferentes do SQL Server, portanto, depois que a versão do SQL Server tiver sido alterada, nenhuma ação adicional será necessária.

1. **Onde posso obter a mídia de instalação para alterar a edição ou a versão do SQL Server?**

  Os clientes que têm o [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) podem obter a mídia de instalação do [centro de licenciamento por volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Os clientes que não têm o Software Assurance podem usar a mídia de instalação de um Marketplace SQL Server imagem de VM que tenha sua edição desejada.

1. **Como as atualizações e os service packs são aplicados a uma VM do SQL Server?**

   As máquinas virtuais oferecem controle sobre o computador host, inclusive quando e como aplicar atualizações. Para o sistema operacional, você pode aplicar manualmente as atualizações do Windows ou habilitar um serviço de agendamento chamado [Aplicação de Patch Automatizada](virtual-machines-windows-sql-automated-patching.md). A Aplicação de Patch Automatizada instala todas as atualizações marcadas como importantes, inclusive atualizações do SQL Server nessa categoria. Outras atualizações opcionais para o SQL Server devem ser instaladas manualmente.

1. **Posso atualizar minha instância do SQL Server 2008/2008 R2 depois de registrá-la com o provedor de recursos de VM SQL Server?**

   Sim. Você pode usar qualquer mídia de instalação para atualizar a versão e a edição do SQL Server e, em seguida, pode atualizar o [modo de extensão de IaaS do SQL](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)) de _nenhum agente_ para _completo_. Isso dará acesso a todos os benefícios da extensão de IaaS do SQL, como gerenciamento de portal, backups automatizados e aplicação de patch automatizada. 

## <a name="general"></a>Geral

1. **Há suporte para FCIs (Instâncias de Cluster de Failover) do SQL Server nas VMs do Azure?**

   Sim. É possível [criar um Cluster de Failover do Windows no Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) e usar o S2D (Espaços de Armazenamento Diretos) para o armazenamento de cluster. Como alternativa, você pode usar soluções de clustering ou armazenamento de terceiros, conforme descrito em [Alta disponibilidade e recuperação de desastre para SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > Neste momento, não há suporte para a extensão _completa_ do [agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md) para SQL Server FCI no Azure. Recomendamos que você desinstale a extensão _completa_ de VMs que participam do FCI e instale a extensão no modo _leve_ . Essa extensão oferece suporte a recursos, como backup automatizado e aplicação de patches e alguns recursos do portal para SQL Server. Esses recursos não funcionarão para VMs SQL Server depois que o agente _completo_ for desinstalado.

1. **Qual é a diferença entre SQL Server VMs e o serviço de banco de dados SQL?**

   Conceitualmente, a execução do SQL Server em uma máquina virtual do Azure não é diferente da execução do SQL Server em um datacenter remoto. Por outro lado, o [Banco de Dados SQL](../../../sql-database/sql-database-technical-overview.md) oferece o banco de dados como serviço. Com o Banco de Dados SQL, você não tem acesso às máquinas que hospedam os bancos de dados. Para obter uma comparação completa, confira [Escolher uma opção do SQL Server de nuvem: Banco de Dados SQL do Azure (PaaS) ou SQL Server em VMs do Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Como instalar as ferramentas de Dados do SQL em minha VM do Azure?**

    Baixe e instale as ferramentas de Dados SQL por meio do [Microsoft SQL Server Data Tools – Business Intelligence para Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **As transações distribuídas com o MSDTC têm suporte em VMs SQL Server?**
   
    Sim. O DTC local tem suporte para o SQL Server 2016 SP2 e superior. No entanto, os aplicativos devem ser testados ao utilizar grupos de disponibilidade Always On, pois as transações em andamento durante um failover falharão e deverão ser repetidas. O DTC clusterizado está disponível a partir do Windows Server 2019. 

## <a name="resources"></a>Implante

**VMs do Windows**:

* [Visão geral do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Provisionar uma VM do Windows do SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migração de um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md)
* [Alta disponibilidade e recuperação de desastres para SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md)
* [Estratégias de Desenvolvimento e Padrões de Aplicativo para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**VMs Linux**:

* [Visão geral do SQL Server em uma VM Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Provisionar uma VM Linux do SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Perguntas Frequentes (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server na documentação do Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
