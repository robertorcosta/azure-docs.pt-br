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
ms.openlocfilehash: 0d6d69b82e80ff9bc33e49302cf59766b9c2e8d4
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270818"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Perguntas freqüentes sobre o SQL Server em execução em máquinas virtuais do Windows no Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Este artigo fornece respostas para algumas das perguntas mais comuns sobre como executar [SQL Server no Windows máquinas virtuais no Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Este artigo se concentra em problemas específicos ao SQL Server em VMs Windows. Se você estiver executando o SQL Server em VMs Linux, consulte as [Perguntas frequentes sobre o Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Imagens

1. **Quais imagens da galeria de máquinas virtuais do SQL Server estão disponíveis?** 

   O Azure mantém imagens de máquina virtual para todas as versões principais compatíveis do SQL Server em todas as edições para o Windows e para o Linux. Para obter mais informações, consulte a lista completa dos [imagens de VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) e [imagens de VM do Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **As imagens da galeria de máquinas virtuais do SQL Server existentes são atualizadas?**

   A cada dois meses, as imagens do SQL Server na galeria de máquinas virtuais são atualizadas com as atualizações mais recentes do Windows e do Linux. Para imagens do Windows, isso inclui quaisquer atualizações marcadas como importantes no Windows Update, incluindo service packs e atualizações de segurança do SQL Server. Para imagens do Linux, isso inclui as atualizações mais recentes do sistema. As atualizações cumulativas do SQL Server são tratadas de maneira diferente para o Linux e para o Windows. Para o Linux, as atualizações cumulativas do SQL Server também são incluídas na atualização. Mas, neste momento, as VMs Windows não são atualizadas com as atualizações cumulativas do SQL Server ou do Windows Server.

1. **As imagens de máquina virtual do SQL Server podem ser removidas da galeria?**

   Sim. O Azure mantém apenas uma imagem por versão principal e edição. Por exemplo, quando um novo service pack do SQL Server é lançado, o Azure adiciona uma nova imagem à galeria para esse service pack. A imagem do SQL Server para o service pack anterior é removida imediatamente do Portal do Azure. No entanto, ela ainda estará disponível para provisionamento do PowerShell pelos próximos três meses. Depois de três meses, a imagem do service pack anterior não estará mais disponível. Essa política de remoção também se aplica quando uma versão do SQL Server se torna incompatível quando ela atinge o final de seu ciclo de vida.


1. **É possível implantar uma imagem mais antiga do SQL Server que não está visível no portal Azure?**

   Sim, usando o PowerShell. Para obter mais informações sobre como implantar VMs do SQL Server usando o PowerShell, confira [Como provisionar máquinas virtuais do SQL Server com o Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Como posso generalizar o SQL Server no Azure VM e usá-lo para implantar novas VMs?**

   Você pode implantar um VM do Windows Server (sem qualquer SQL Server instalado nele) e usar o processo [SQL sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep?view=sql-server-ver15) para generalizar o SQL Server no Azure VM (Windows) com a mídia de instalação do SQL Server. Os clientes que possuem [garantia de software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3) podem obter sua mídia de instalação no Centro de Licenciamento de [Volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Os clientes que não têm garantia de software podem usar a mídia de configuração a partir de uma imagem VM do Marketplace SQL Server que tem a edição desejada.

   Alternativamente, use uma das imagens do SQL Server para generalizar o SQL Server no Azure VM. Observe que você deve excluir a seguinte chave de registro na imagem de origem antes de criar sua própria imagem. Caso contrário, o bloating da pasta bootstrap de configuração do SQL Server e/ou extensão SQL IaaS em estado de falha.

   Caminho-chave do registro:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > Recomendamos que todas as VMs SQL Server Azure, incluindo aquelas implantadas a partir de imagens personalizadas generalizadas, sejam [registradas em um provedor de recurso SQL VM](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-register-with-resource-provider?tabs=azure-cli%2Cbash) para atender aos requisitos de conformidade e utilizar recursos opcionais, como patches automatizados e backups automáticos. Ele também permitirá que você [especifique o tipo de licença](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-ahb?tabs=azure-portal) para cada VM do Servidor SQL.

1. **Posso usar meu próprio VHD para implantar um VM do SQL Server?**

   Sim, mas você deve [registrar cada VM do SQL Server com o provedor de recursos SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) para gerenciar seu VM do SQL Server no portal, bem como utilizar recursos como patches automatizados e backups automáticos.

1. **É possível definir configurações não mostradas na galeria de máquinas virtuais (por exemplo, Windows 2008 R2 + SQL Server 2012)?**

   Não. Para imagens da galeria de máquinas virtuais que incluem o SQL Server, você deve selecionar uma das imagens fornecidas, seja pelo portal do Azure ou via [PowerShell](virtual-machines-windows-ps-sql-create.md). No entanto, você tem a capacidade de implantar um Windows VM e auto-instalar o SQL Server nele. Em seguida, você deve [registrar seu VM sql server com o provedor de recursos SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) para gerenciar seu VM SQL Server no portal, bem como utilizar recursos como patches automatizados e backups automáticos. 


## <a name="creation"></a>Criação

1. **Como criar uma máquina virtual do Azure com o SQL Server?**

   O método mais fácil é criar uma máquina virtual que inclua o SQL Server. Para obter um tutorial sobre a inscrição no Azure e a criação de um VM do SQL Server a partir do portal, consulte [Provision a SQL Server virtual machine in the Azure portal](virtual-machines-windows-portal-sql-server-provision.md). Selecione uma imagem de máquina virtual que usa o licenciamento do SQL Server pago por segundo ou use uma imagem que permite trazer sua própria licença do SQL Server. Você também tem a opção de instalação manual do SQL Server em uma VM com um uma edição licenciada gratuitamente (Desenvolvedor ou Express) ou pela reutilização de uma licença local. Certifique-se de [registrar seu VM do SQL Server com o provedor de recursos SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) para gerenciar seu VM do SQL Server no portal, bem como utilizar recursos como patches automatizados e backups automáticos. Se você trouxer sua própria licença, será necessário ter o [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Para obter mais informações, consulte [Diretrizes de preço para VMs do Azure do SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Como migrar meu banco de dados do SQL Server local para a nuvem?**

   Primeiro, crie uma máquina virtual do Azure com uma instância do SQL Server. Em seguida, migre os bancos de dados locais para essa instância. Para obter estratégias de migração de dados, confira [Migrar um banco de dados do SQL Server para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licenciamento

1. **Como instalar minha cópia licenciada do SQL Server em uma VM do Azure?**

   Há três maneiras de fazer isso. Se você é um cliente de contrato corporativo (EA), você pode provisionar uma das imagens de [máquina virtual que suporta licenças](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), que também é conhecida como bring-your-own-license (BYOL). Se você tiver [garantia de software,](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)você pode habilitar o [Azure Hybrid Benefit](virtual-machines-windows-sql-ahb.md) em uma imagem pay-as-you-go (PAYG) existente. Ou você pode copiar a mídia de instalação do SQL Server para um VM do Servidor Windows e, em seguida, instalar o SQL Server na VM. Certifique-se de registrar seu VM sql server com o [provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md) para recursos como gerenciamento de portais, backup automatizado e patches automatizados. 

1. **Posso alterar uma VM para usar minha própria licença do SQL Server se ela foi criada com base em uma das imagens pré-pagas da galeria?**

   Sim. Você pode facilmente mudar uma imagem de galeria pay-as-you-go (PAYG) para trazer sua própria licença (BYOL) habilitando o [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Para obter mais informações, confira [Como alterar o modelo de licenciamento para uma VM do SQL Server](virtual-machines-windows-sql-ahb.md). Atualmente, esse recurso está disponível somente para clientes de nuvem pública.

1. **Modelos de licenciamento de comutação exigirá nenhum tempo de inatividade para o SQL Server?**

   Não. [A alteração do modelo de licenciamento](virtual-machines-windows-sql-ahb.md) não requer qualquer tempo de inatividade para o SQL Server, pois a alteração é eficaz imediatamente e não requer uma reinicialização da VM. No entanto, para registrar seu VM do SQL Server com o provedor de recursos SQL Server VM, a [extensão SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) é um pré-requisito e instalar a extensão SQL IaaS no modo _completo_ reinicia o serviço SQL Server. Como tal, se a extensão SQL IaaS precisar ser instalada, instale-a no modo _leve_ para funcionalidade limitada ou instale-a em modo _completo_ durante uma janela de manutenção. A extensão SQL IaaS instalada no modo _leve_ pode ser atualizada para o modo _completo_ a qualquer momento, mas requer uma reinicialização do serviço SQL Server. 
   
1. **É possível mudar o modelo de licenciamento em um VM SQL Server implantado usando o modelo clássico?**

   Não. A mudança do modelo de licenciamento não é suportada em uma VM clássica. Você pode migrar sua VM para o modelo do Azure Resource Manager e se registrar no provedor de recursos SQL Server VM. Uma vez que a VM esteja registrada no provedor de recursos SQL Server VM, as alterações do modelo de licenciamento estarão disponíveis na VM.

1. **Posso usar o portal Azure para gerenciar várias instâncias na mesma VM?**

   Não. O gerenciamento do portal é um recurso fornecido pelo provedor de recursos SQL Server VM, que conta com a extensão do SQL Server IaaS Agent. Como tal, as mesmas limitações se aplicam ao provedor de recursos quanto à extensão. O portal só pode gerenciar uma instância padrão ou uma instância nomeada, desde que tenha sido configurada corretamente. Para obter mais informações sobre essas limitações, consulte [a extensão do agente SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). 

1. **Assinaturas de CSP podem ativar o benefício híbrido do Azure?**

   Sim, o Benefício Híbrido do Azure está disponível para assinaturas de CSP. Os clientes do CSP devem primeiro implantar uma imagem de pago conforme o uso e, em seguida, [alterar o modelo de licenciamento](virtual-machines-windows-sql-ahb.md) para traga sua própria licença (BYOL).
   
 
1. **É necessário pagar para licenciar o SQL Server em uma VM do Azure se ela está sendo usada somente para espera/failover?**

   Para ter uma licença passiva gratuita para um grupo de disponibilidade secundária em standby ou uma instância de cluster failover, você deve atender a todos os seguintes critérios [descritos](https://www.microsoft.com/licensing/product-licensing/products)pelos Termos de Licenciamento do Produto :

   1. Você tem [mobilidade de licença](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) através de Garantia de [Software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. A instância passiva do SQL Server não serve dados do SQL Server para clientes ou executa cargas de trabalho ativas do SQL Server. Ele é usado apenas para sincronizar com o servidor principal e manter o banco de dados passivo em um estado de espera quente. Se ele estiver servindo dados, como relatórios para clientes executando cargas de trabalho ativas do SQL Server ou realizando qualquer trabalho diferente do que é especificado nos termos do produto, ele deve ser uma instância sql server licenciada paga. A seguinte atividade é permitida na instância secundária: verificações de consistência do banco de dados ou CheckDB, backups completos, backups de log de transações e monitoramento de dados de uso de recursos. Você também pode executar a instância primária e correspondente de recuperação de desastres simultaneamente por breves períodos de testes de recuperação de desastres a cada 90 dias. 
   1. A licença ativa do SQL Server é coberta pelo Software Assurance e permite **uma** instância secundária passiva do SQL Server, com até a mesma quantidade de cálculo que o servidor ativo licenciado, apenas. 
   1. O VM secundário do SQL Server utiliza a licença [de recuperação de desastres](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) no portal Azure.
   
1. **O que é considerado uma instância passiva?**

   A instância passiva do SQL Server não serve dados do SQL Server para clientes ou executa cargas de trabalho ativas do SQL Server. Ele é usado apenas para sincronizar com o servidor principal e manter o banco de dados passivo em um estado de espera quente. Se ele estiver servindo dados, como relatórios para clientes executando cargas de trabalho ativas do SQL Server ou realizando qualquer trabalho diferente do que é especificado nos termos do produto, ele deve ser uma instância sql server licenciada paga. A seguinte atividade é permitida na instância secundária: verificações de consistência do banco de dados ou CheckDB, backups completos, backups de log de transações e monitoramento de dados de uso de recursos. Você também pode executar a instância primária e correspondente de recuperação de desastres simultaneamente por breves períodos de testes de recuperação de desastres a cada 90 dias.
   

1. **Quais cenários podem utilizar o benefício de Recuperação de Desastres (DR) ?**

   O [guia de licenciamento](https://aka.ms/sql2019licenseguide) fornece cenários em que o Benefício de Recuperação de Desastres pode ser utilizado. Consulte seus Termos de Produto e converse com seus contatos de licenciamento ou gerente de conta para obter mais informações.

1. **Quais assinaturas suportam o benefício de Recuperação de Desastres (DR) ?**

   Programas abrangentes que oferecem direitos de assinatura equivalentes do Software Assurance como um benefício fixo suportam o benefício DR. Isso inclui. mas não se limita a, o Valor Aberto (OV), Assinatura de Valor Aberto (OVS), Contrato Empresarial (EA), Contrato de Assinatura Empresarial (EAS) e o Servidor e Matrícula em Nuvem (SCE). Consulte os termos do [produto](https://www.microsoft.com/licensing/product-licensing/products) e converse com seus contatos de licenciamento ou gerente de conta para obter mais informações. 

   
 ## <a name="resource-provider"></a>Provedor de recursos

1. **O registro do meu VM com o novo provedor de recursos SQL Server VM trará custos adicionais?**

   Não. O provedor de recursos SQL Server VM apenas permite a capacidade de gerenciamento adicional para o SQL Server no Azure VM sem cobranças adicionais. 

1. **O provedor de recursos SQL Server VM está disponível para todos os clientes?**
 
   Sim, desde que o VM do SQL Server tenha sido implantado na nuvem pública usando o modelo Resource Manager, e não o modelo clássico. Todos os outros clientes podem se registrar no novo provedor de recursos SQL Server VM. No entanto, apenas clientes com o benefício [de Garantia de Software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) podem usar sua própria licença ativando o [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) em uma VM SQL Server. 

1. **O que acontece com o recurso do provedor de recursos _(Microsoft.SqlVirtualMachine)_ se o recurso vm for movido ou descartado?** 

   Quando o recurso Microsoft.Compute / VirtualMachine é descartado ou movido, o recurso Microsoft.SqlVirtualMachine associado é notificado para replicar de maneira assíncrona a operação.

1. **O que acontece com a VM se o recurso do provedor de recursos _(Microsoft.SqlVirtualMachine)_ for descartado?**

    O recurso Microsoft.Compute / Virtual Machine não é afetado quando o recurso Microsoft.Sql Virtual Machine é descartado. No entanto, as alterações de licenciamento serão padronizadas de volta para a origem da imagem original. 

1. **É possível registrar VMs sql server auto-implantados com o provedor de recursos SQL Server VM?**

    Sim. Se você implantou o SQL Server a partir da sua própria mídia e instalou a extensão do SQL IaaS, é possível registrar sua VM do SQL com o provedor de recursos para obter os benefícios de capacidade de gerenciamento fornecidos pela extensão SQL IaaS. No entanto, você não pode converter um VM sql server auto-implantado em pay-as-you-go.


   


## <a name="administration"></a>Administração

1. **Posso instalar uma segunda instância do SQL Server na mesma VM? Posso alterar os recursos instalados da instância padrão?**

   Sim. A mídia de instalação do SQL Server está localizada em uma pasta na unidade **C.** Executar **setup.exe** a partir desse local para adicionar novas instâncias do SQL Server ou para alterar outros recursos instalados do SQL Server na máquina. Observe que alguns recursos, como backup automatizado, patches automatizados e integração do cofre chave do Azure, só operam contra a instância padrão ou uma instância nomeada que foi configurada corretamente (Consulte a questão 3). 

1. **Posso desinstalar a instância padrão do SQL Server?**

   Sim, mas há algumas considerações. Primeiro, o faturamento associado ao SQL Server pode continuar a ocorrer dependendo do modelo de licença para a VM. Em segundo lugar, como indicado na resposta anterior, existem recursos que dependem do [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md). Se você desinstalar a instância padrão sem remover a extensão IaaS também, a extensão continuará procurando a instância padrão e pode gerar erros de registro de eventos. Esses erros são provenientes das duas seguintes fontes: **Gerenciamento de Credenciais do Microsoft SQL Server** e **Microsoft SQL Server IaaS Agent**. Um dos erros pode ser semelhante ao seguinte:

      Ocorreu um erro relacionado à rede ou específico da instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível.

   Se você decidir desinstalar a instância padrão, também desinstale a extensão do [agente IaaS do Servidor SQL](virtual-machines-windows-sql-server-agent-extension.md) também. 

1. **Posso usar uma instância nomeada do SQL Server com a extensão IaaS?**
   
   Sim, se a instância nomeada for a única instância no Servidor SQL e se a instância padrão original foi [desinstalada corretamente](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance). Se não houver uma instância padrão e houver várias instâncias nomeadas em uma única VM do Servidor SQL, a extensão do agente SQL Server IaaS falhará em ser instalada. 

1. **Posso remover o SQL Server completamente de um VM do Servidor SQL?**

   Sim, mas você continuará a ser cobrado pelo seu VM sql server, conforme descrito na [orientação de preços para VMs SQL Server Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Se você não precisar mais do SQL Server, você poderá implantar uma nova máquina virtual e migrar os dados e aplicativos para a nova máquina virtual. Em seguida, você pode remover a máquina virtual do SQL Server.
   
## <a name="updating-and-patching"></a>Atualização e aplicação de patch

1. **Como atualizar para uma nova versão/edição do SQL Server em uma VM do Azure?**

   Os clientes podem alterar sua versão/edição do SQL Server usando a mídia de instalação que contém a versão desejada ou a edição do SQL Server. Depois que a edição for alterada, use o portal do Azure para modificar a propriedade de edição da VM para refletir com precisão a cobrança da VM. Para obter mais informações, consulte [a edição de alteração de um VM do Servidor SQL](virtual-machines-windows-sql-change-edition.md). Não há diferença de faturamento para diferentes versões do SQL Server, portanto, uma vez que a versão do SQL Server tenha sido alterada, nenhuma ação adicional é necessária.

1. **Onde posso obter a mídia de configuração para alterar a edição ou versão do SQL Server?**

   Os clientes que possuem [garantia de software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) podem obter sua mídia de instalação no Centro de Licenciamento de [Volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Os clientes que não possuem garantia de software podem usar a mídia de configuração a partir de uma imagem VM do Marketplace SQL Server que tem a edição desejada.
   
1. **Como as atualizações e os service packs são aplicados a uma VM do SQL Server?**

   As máquinas virtuais oferecem controle sobre o computador host, inclusive quando e como aplicar atualizações. Para o sistema operacional, você pode aplicar manualmente as atualizações do Windows ou habilitar um serviço de agendamento chamado [Aplicação de Patch Automatizada](virtual-machines-windows-sql-automated-patching.md). A Aplicação de Patch Automatizada instala todas as atualizações marcadas como importantes, inclusive atualizações do SQL Server nessa categoria. Outras atualizações opcionais para o SQL Server devem ser instaladas manualmente.

1. **Posso atualizar minha instância sql server 2008 / 2008 R2 depois de registrá-lo no provedor de recursos SQL Server VM?**

   Sim. Você pode usar qualquer mídia de configuração para atualizar a versão e edição do SQL Server e, em seguida, você pode atualizar o modo de [extensão SQL IaaS](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)) de _nenhum agente_ para _completo_. Isso lhe dará acesso a todos os benefícios da extensão SQL IaaS, como gerenciamento de portais, backups automatizados e patches automatizados. 

1. **Como posso obter atualizações de segurança estendidas gratuitas para o meu fim de suporte SQL Server 2008 e SQL Server 2008 R2 instâncias?**

   Você pode obter [atualizações de segurança estendidas gratuitas](virtual-machines-windows-sql-server-2008-eos-extend-support.md) movendo seu SQL Server como está para uma máquina virtual Azure SQL. Para obter mais informações, consulte [o fim das opções de suporte](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Geral

1. **As instâncias de cluster de failover do SQL Server (FCI) são suportadas em VMs do Azure?**

   Sim. Você pode instalar uma instância de cluster failover usando [ações de arquivo premium (PFS)](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) ou espaços de armazenamento direto [(S2D)](virtual-machines-windows-portal-sql-create-failover-cluster.md) para o subsistema de armazenamento. Os compartilhamentos de arquivos premium fornecem IOPS e capacidades de throughput que atenderão às necessidades de muitas cargas de trabalho. Para cargas de trabalho intensivas em IO, considere usar espaços de armazenamento diretamente com base em discos premium ou ultra-discos manged. Como alternativa, você pode usar soluções de clustering ou armazenamento de terceiros, conforme descrito em [Alta disponibilidade e recuperação de desastre para SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > Neste momento, a extensão _completa_ do [Agente IaaS do Servidor SQL](virtual-machines-windows-sql-server-agent-extension.md) não é suportada para o SQL Server FCI no Azure. Recomendamos que você desinstale a extensão _completa_ das VMs que participam do FCI e instale a extensão no modo _leve._ Essa extensão suporta recursos, como backup e patching automatizados e alguns recursos de portal para o SQL Server. Esses recursos não funcionarão para VMs do Servidor SQL depois que o agente _completo_ for desinstalado.

1. **Qual é a diferença entre as VMs do SQL Server e o serviço sql database?**

   Conceitualmente, a execução do SQL Server em uma máquina virtual do Azure não é diferente da execução do SQL Server em um datacenter remoto. Por outro lado, o [Banco de Dados SQL](../../../sql-database/sql-database-technical-overview.md) oferece o banco de dados como serviço. Com o Banco de Dados SQL, você não tem acesso às máquinas que hospedam os bancos de dados. Para obter uma comparação completa, confira [Escolher uma opção do SQL Server de nuvem: Banco de Dados SQL do Azure (PaaS) ou SQL Server em VMs do Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Como instalar as ferramentas de Dados do SQL em minha VM do Azure?**

    Baixe e instale as ferramentas de Dados SQL por meio do [Microsoft SQL Server Data Tools – Business Intelligence para Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **As transações distribuídas com o MSDTC são suportadas em VMs do SQL Server?**
   
    Sim. O DTC local é suportado para o SQL Server 2016 SP2 e maior. No entanto, os aplicativos devem ser testados ao utilizar grupos de disponibilidade Always On, pois as transações em voo durante um failover falharão e devem ser repetidas novamente. O DTC clustered está disponível a partir do Windows Server 2019. 

## <a name="resources"></a>Recursos

**VMs do Windows**:

* [Visão geral do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Provisionar uma VM do Windows do SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrando um banco de dados para o SQL Server em uma VM Azure](virtual-machines-windows-migrate-sql.md)
* [Alta disponibilidade e recuperação de desastres para sql server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md)
* [Estratégias de Desenvolvimento e Padrões de Aplicativo para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**VMs Linux**:

* [Visão geral do SQL Server em uma VM Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Provisionar uma VM Linux do SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Perguntas Frequentes (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server na documentação Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
