---
title: O SQL Server em máquinas virtuais do Windows na FAQ do Azure | Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes sobre a execução do SQL Server em VMs do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: f382e3cf0f5d2d60c2868c6698b1ea901fbac023
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121435"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Perguntas frequentes sobre SQL Server em VMs do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

Este artigo fornece respostas para algumas das perguntas mais comuns sobre a execução [de SQL Server em VMS (máquinas virtuais) do Windows Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Imagens

1. **Quais imagens da galeria de máquinas virtuais do SQL Server estão disponíveis?** 

   O Azure mantém imagens de máquina virtual para todas as versões principais compatíveis do SQL Server em todas as edições para o Windows e para o Linux. Para obter mais informações, consulte a lista completa dos [imagens de VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) e [imagens de VM do Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create).

1. **As imagens da galeria de máquinas virtuais do SQL Server existentes são atualizadas?**

   A cada dois meses, as imagens do SQL Server na galeria de máquinas virtuais são atualizadas com as atualizações mais recentes do Windows e do Linux. Para imagens do Windows, isso inclui quaisquer atualizações marcadas como importantes no Windows Update, incluindo service packs e atualizações de segurança do SQL Server. Para imagens do Linux, isso inclui as atualizações mais recentes do sistema. As atualizações cumulativas do SQL Server são tratadas de maneira diferente para o Linux e para o Windows. Para o Linux, as atualizações cumulativas do SQL Server também são incluídas na atualização. Mas, neste momento, as VMs Windows não são atualizadas com as atualizações cumulativas do SQL Server ou do Windows Server.

1. **As imagens de máquina virtual do SQL Server podem ser removidas da galeria?**

   Sim. O Azure mantém apenas uma imagem por versão principal e edição. Por exemplo, quando um novo service pack do SQL Server é lançado, o Azure adiciona uma nova imagem à galeria para esse service pack. A imagem do SQL Server para o service pack anterior é removida imediatamente do Portal do Azure. No entanto, ela ainda estará disponível para provisionamento do PowerShell pelos próximos três meses. Depois de três meses, a imagem do service pack anterior não estará mais disponível. Essa política de remoção também se aplica quando uma versão do SQL Server se torna incompatível quando ela atinge o final de seu ciclo de vida.


1. **É possível implantar uma imagem mais antiga do SQL Server que não está visível no portal do Azure?**

   Sim, usando o PowerShell. Para obter mais informações sobre como implantar VMs do SQL Server usando o PowerShell, confira [Como provisionar máquinas virtuais do SQL Server com o Azure PowerShell](create-sql-vm-powershell.md).
   
1. **É possível criar uma imagem de SQL Server do Azure Marketplace generalizada de minha VM SQL Server e usá-la para implantar VMs?**

   Sim, mas você deve [registrar cada VM do SQL Server no provedor de recursos de VM do SQL Server](sql-vm-resource-provider-register.md) para gerenciar sua VM do SQL Server no portal, bem como utilizar recursos como a aplicação automatizada de patches e os backups automáticos. Ao registrar no provedor de recursos, também será necessário especificar o tipo de licença para cada VM do SQL Server.

1. **Como faço para generalizar a VM do SQL Server no Azure e usá-la para implantar novas VMs?**

   Você pode implantar uma VM do Windows Server (sem o SQL Server instalado) e usar o processo [SQL sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep?view=sql-server-ver15) para generalizar o SQL Server na VM do Azure (Windows) com a mídia de instalação do SQL Server. Os clientes que têm [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3) podem obter a mídia de instalação deles do [Centro de Licenciamento por Volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Os clientes que não têm o Software Assurance podem usar a mídia de instalação de um Azure Marketplace SQL Server imagem de VM que tem a edição desejada.

   Como alternativa, use uma das imagens SQL Server do Azure Marketplace para generalizar SQL Server na VM do Azure. Observe que você deve excluir a chave do registro a seguir na imagem de origem antes de criar sua própria imagem. Se você não fizer isso, poderá ocorrer sobrecarga da pasta de inicialização na instalação do SQL Server e/ou extensão de IaaS do SQL com estado de falha.

   Caminho da chave do registro:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > VMs do SQL Server no Azure, incluindo aquelas implantadas de imagens generalizadas personalizadas, devem estar [registradas no provedor de recursos de VM do SQL](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-register-with-resource-provider?tabs=azure-cli%2Cbash) para atender aos requisitos de conformidade e utilizar recursos opcionais, como a aplicação automatizada de patches e os backups automáticos. O provedor de recursos também permite [especificar o tipo de licença](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-ahb?tabs=azure-portal) para cada VM do SQL Server.

1. **Posso usar meu próprio VHD para implantar uma VM do SQL Server?**

   Sim, mas você deve [registrar cada VM do SQL Server no provedor de recursos de VM do SQL Server](sql-vm-resource-provider-register.md) para gerenciar sua VM do SQL Server no portal, bem como utilizar recursos como a aplicação automatizada de patches e os backups automáticos.

1. **É possível configurar as configurações não mostradas na Galeria de máquinas virtuais (por exemplo, Windows 2008 R2 + SQL Server 2012)?**

   Não. Para imagens da galeria de máquinas virtuais que incluem o SQL Server, você deve selecionar uma das imagens fornecidas, seja pelo portal do Azure ou via [PowerShell](create-sql-vm-powershell.md). No entanto, você pode implantar uma VM do Windows e fazer a instalação automática do SQL Server nela. Em seguida, você deve [registrar sua vm SQL Server com o provedor de recursos de vm SQL Server](sql-vm-resource-provider-register.md) para gerenciar sua VM SQL Server no portal do Azure, bem como utilizar recursos como aplicação de patch automatizada e backups automáticos. 


## <a name="creation"></a>Criação

1. **Como criar uma máquina virtual do Azure com o SQL Server?**

   O método mais fácil é criar uma máquina virtual que inclua SQL Server. Para obter um tutorial sobre como se inscrever no Azure e criar uma VM do SQL Server por meio do portal, confira [Provisionar uma máquina virtual do SQL Server no Portal do Azure](create-sql-vm-portal.md). Selecione uma imagem de máquina virtual que usa o licenciamento do SQL Server pago por segundo ou use uma imagem que permite trazer sua própria licença do SQL Server. Você também tem a opção de instalação manual do SQL Server em uma VM com um uma edição licenciada gratuitamente (Desenvolvedor ou Express) ou pela reutilização de uma licença local. Você deve [registrar sua VM do SQL Server no provedor de recursos da VM do SQL Server](sql-vm-resource-provider-register.md) para gerenciar sua VM do SQL Server no portal, bem como utilizar recursos como a aplicação automatizada de patches e os backups automáticos. Se você trouxer sua própria licença, será necessário ter o [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Para obter mais informações, consulte [Diretrizes de preço para VMs do Azure do SQL Server](pricing-guidance.md).

1. **Como posso migrar meu banco de dados local SQL Server para a nuvem?**

   Primeiro, crie uma máquina virtual do Azure com uma instância do SQL Server. Em seguida, migre os bancos de dados locais para essa instância. Para obter estratégias de migração de dados, confira [Migrar um banco de dados do SQL Server para o SQL Server em uma VM do Azure](migrate-to-vm-from-sql-server.md).

## <a name="licensing"></a>Licenciamento

1. **Como instalar minha cópia licenciada do SQL Server em uma VM do Azure?**

   Há três maneiras de fazer isso: Se você for um cliente Enterprise Agreement (EA), poderá provisionar uma das [imagens de máquina virtual que dá suporte a licenças](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL), que também é conhecida como BYOL (traga sua própria licença). Se você tiver o [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default), poderá habilitar o [benefício híbrido do Azure](licensing-model-azure-hybrid-benefit-ahb-change.md) em uma imagem PAYG (pré-pago) existente. Ou você pode copiar a mídia de instalação do SQL Server para uma VM do Windows Server e, em seguida, instalar SQL Server na VM. Certifique-se de registrar sua VM do SQL Server no [provedor de recursos](sql-vm-resource-provider-register.md) para obter recursos como o gerenciamento de portal, o backup automatizado e a aplicação automatizada de patches. 

1. **Posso alterar uma VM para usar minha própria licença do SQL Server se ela foi criada com base em uma das imagens pré-pagas da galeria?**

   Sim. Você pode mudar facilmente uma imagem de galeria PAYG para BYOL habilitando o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Para obter mais informações, confira [Como alterar o modelo de licenciamento para uma VM do SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md). Atualmente, essa instalação está disponível somente para clientes públicos e de nuvem do Azure Governamental.

1. **Modelos de licenciamento de comutação exigirá nenhum tempo de inatividade para o SQL Server?**

   Não. [A alteração do modelo de licenciamento](licensing-model-azure-hybrid-benefit-ahb-change.md) não requer tempo de inatividade para o SQL Server, pois a alteração entrará em vigor imediatamente e não exige a reinicialização da VM. No entanto, para registrar sua VM do SQL Server no provedor de recursos da VM do SQL Server, a [extensão IaaS do SQL](sql-server-iaas-agent-extension-automate-management.md) é um pré-requisito e sua instalação no modo _completo_ reinicia o serviço do SQL Server. Assim, se a extensão de IaaS do SQL precisar ser instalada, instale-a no modo _leve_ para obter funcionalidade limitada ou no modo _completo_ durante uma janela de manutenção. A extensão IaaS do SQL instalada no modo _leve_ pode ser atualizada para o modo _completo_ a qualquer momento, mas requer a reinicialização do serviço do SQL Server. 
   
1. **É possível alternar os modelos de licenciamento em uma VM SQL Server implantada usando o modelo clássico?**

   Não. Não há suporte para a alteração de modelos de licenciamento em uma VM clássica. Você pode migrar sua VM para o modelo de Azure Resource Manager e registrar com o provedor de recursos de VM do SQL Server. Depois que a VM estiver registrada com o provedor de recursos de VM do SQL Server, as alterações no modelo de licenciamento estarão disponíveis na VM.

1. **Posso usar o portal do Azure para gerenciar várias instâncias na mesma VM?**

   Não. O gerenciamento de portal é um recurso fornecido pelo provedor de recursos de VM do SQL Server, que se baseia na extensão do agente IaaS do SQL Server. Assim, as mesmas limitações se aplicam ao provedor de recursos e para a extensão. O portal pode gerenciar apenas uma instância padrão ou uma instância nomeada, desde que tenha sido configurada corretamente. Para obter mais informações, confira [extensão do agente IaaS do SQL Server](sql-server-iaas-agent-extension-automate-management.md). 

1. **Assinaturas de CSP podem ativar o benefício híbrido do Azure?**

   Sim, o Benefício Híbrido do Azure está disponível para assinaturas de CSP. Os clientes do CSP devem primeiro implantar uma imagem de pago conforme o uso e, em seguida, [alterar o modelo de licenciamento](licensing-model-azure-hybrid-benefit-ahb-change.md) para traga sua própria licença (BYOL).
   
 
1. **É necessário pagar para licenciar o SQL Server em uma VM do Azure se ela está sendo usada somente para espera/failover?**

   Para ter uma licença passiva gratuita para um grupo de disponibilidade secundário em espera ou instância clusterizada de failover, você deve atender a todos os seguintes critérios, conforme descrito nos [Termos de licenciamento do produto](https://www.microsoft.com/licensing/product-licensing/products):

   1. Você tem [mobilidade de licença](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) por meio do [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. A instância passiva do SQL Server não serve dados do SQL Server a clientes ou executa cargas de trabalho ativas do SQL Server. Ela é usada apenas para sincronizar com o servidor primário e, caso contrário, manter o banco de dados passivo em um estado de espera passiva. Se ela estiver servindo dados, como relatórios para clientes que executam cargas de trabalho ativas do SQL Server, ou executando qualquer outra tarefa que não seja a especificada nos termos do produto, ela deverá ser uma instância do SQL Server paga e licenciada. A atividade a seguir é permitida na instância secundária: verificações de consistência de banco de dados ou CheckDB, backups completos, backups de log de transações e monitoramento de dados de uso de recursos. A cada 90 dias, você também pode executar simultaneamente a instância primária e a de recuperação de desastre correspondente por breves períodos para testar a recuperação de desastre. 
   1. A licença do Active SQL Server é coberta pelo Software Assurance e permite **uma** instância de SQL Server secundária passiva, com até a mesma quantidade de computação do servidor ativo licenciado. 
   1. A VM secundária do SQL Server utiliza a licença de [Recuperação de desastre](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) no portal do Azure.
   
1. **O que é considerado uma instância passiva?**

   A instância passiva do SQL Server não serve dados do SQL Server a clientes ou executa cargas de trabalho ativas do SQL Server. Ela é usada apenas para sincronizar com o servidor primário e, caso contrário, manter o banco de dados passivo em um estado de espera passiva. Se ela estiver servindo dados, como relatórios para clientes que executam cargas de trabalho ativas do SQL Server, ou executando qualquer outra tarefa que não seja a especificada nos termos do produto, ela deverá ser uma instância do SQL Server paga e licenciada. A atividade a seguir é permitida na instância secundária: verificações de consistência de banco de dados ou CheckDB, backups completos, backups de log de transações e monitoramento de dados de uso de recursos. A cada 90 dias, você também pode executar simultaneamente a instância primária e a de recuperação de desastre correspondente por breves períodos para testar a recuperação de desastre.
   

1. **Quais cenários podem utilizar o benefício de DR (Recuperação de Desastre)?**

   O [guia de licenciamento](https://aka.ms/sql2019licenseguide) fornece cenários nos quais o benefício de Recuperação de Desastre pode ser utilizado. Consulte os Termos do Produto e fale com seus contatos de licenciamento ou gerente de conta para obter mais informações.

1. **Quais assinaturas dão suporte ao benefício de DR (Recuperação de Desastre)?**

   Programas abrangentes que oferecem direitos de assinatura equivalentes ao Software Assurance como um benefício fixo dão suporte ao benefício da DR. Isso inclui. Mas não está limitado a, o valor Open (OV), a assinatura de valor aberto (OVS), o Enterprise Agreement (EA), a assinatura do Enterprise Agreement (EAS) e o servidor e o registro na nuvem (SCE). Consulte os [termos do produto](https://www.microsoft.com/licensing/product-licensing/products) e fale com seus contatos de licenciamento ou gerente de conta para obter mais informações. 

   
 ## <a name="resource-provider"></a>Provedor de recursos

1. **Registrar minha VM com o novo provedor de recursos de VM do SQL Server trará custos adicionais?**

   Não. O provedor de recursos de VM de SQL Server apenas habilita a capacidade de gerenciamento adicional do SQL Server na VM do Azure, sem custos adicionais. 

1. **O provedor de recursos da VM do SQL Server está disponível para todos os clientes?**
 
   Sim, contanto que a VM do SQL Server tenha sido implantada na nuvem pública usando o modelo do Resource Manager e não o modelo clássico. Todos os outros clientes podem se registrar com o novo provedor de recursos de VM do SQL Server. No entanto, somente os clientes com o benefício do [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) poderão usar sua própria licença ativando o [AHB (Benefício Híbrido do Azure)](https://azure.microsoft.com/pricing/hybrid-benefit/) em uma VM do SQL Server. 

1. **O que acontece com o provedor de recursos (_Microsoft.SqlVirtualMachine_) se o recurso da VM for movido ou descartado?** 

   Quando o recurso Microsoft.Compute / VirtualMachine é descartado ou movido, o recurso Microsoft.SqlVirtualMachine associado é notificado para replicar de maneira assíncrona a operação.

1. **O que acontece com a VM se o provedor de recursos (_Microsoft. SqlVirtualMachine_) for descartado?**

    O recurso Microsoft.Compute / Virtual Machine não é afetado quando o recurso Microsoft.Sql Virtual Machine é descartado. No entanto, as alterações de licenciamento serão padronizadas de volta para a origem da imagem original. 

1. **É possível registrar VMs do SQL Server implantadas automaticamente com o provedor de recursos de VM do SQL Server?**

    Sim. Se você implantou o SQL Server a partir da sua própria mídia e instalou a extensão do SQL IaaS, é possível registrar sua VM do SQL com o provedor de recursos para obter os benefícios de capacidade de gerenciamento fornecidos pela extensão SQL IaaS.    


## <a name="administration"></a>Administração

1. **Posso instalar uma segunda instância do SQL Server na mesma VM? Posso alterar os recursos instalados da instância padrão?**

   Sim. A mídia de instalação do SQL Server está localizada em uma pasta na unidade **C** . Execute **Setup.exe** nessa localização para adicionar novas instâncias do SQL Server ou para alterar outros recursos instalados do SQL Server no computador. Observe que alguns recursos, como o Backup Automatizado, a Aplicação Automatizada de Patches e a Integração do Azure Key Vault, funcionam apenas na instância padrão ou em uma instância nomeada devidamente configurada (confira a Pergunta 3). Os clientes que usam [o Software Assurance por meio da benefício híbrido do Azure](licensing-model-azure-hybrid-benefit-ahb-change.md) ou do modelo de licenciamento **pago conforme o uso** podem instalar várias instâncias de SQL Server na máquina virtual sem incorrer em custos de licenciamento extras. Instâncias SQL Server adicionais podem restringir os recursos do sistema, a menos que sejam configurados corretamente. 

1. **Posso desinstalar a instância padrão do SQL Server?**

   Sim, mas há algumas considerações. Primeiro, a cobrança associada ao SQL Server pode continuar dependendo do modelo de licença da VM. Segundo, conforme indicado na resposta anterior, há recursos que dependem da [extensão do agente de IaaS do SQL Server](sql-server-iaas-agent-extension-automate-management.md). Se você desinstalar a instância padrão sem remover a extensão de IaaS, a extensão continuará a procurar a instância padrão e poderá gerar erros de log de eventos. Esses erros são provenientes das duas seguintes fontes: **Gerenciamento de Credenciais do Microsoft SQL Server** e **Microsoft SQL Server IaaS Agent**. Um dos erros pode ser semelhante ao seguinte:

      Ocorreu um erro relacionado à rede ou específico da instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível.

   Se você optar por desinstalar a instância padrão, desinstale também a [Extensão do SQL Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md). 

1. **Posso usar uma instância nomeada do SQL Server com a extensão de IaaS**?
   
   Sim, se a instância nomeada for única no SQL Server e se a instância padrão original for [devidamente desinstalada](sql-server-iaas-agent-extension-automate-management.md#install-on-a-vm-with-a-single-named-sql-server-instance). Se não houver nenhuma instância padrão e existirem várias instâncias nomeadas em uma única VM do SQL Server, a extensão do agente de IaaS do SQL Server não será instalada. 

1. **Posso remover o SQL Server completamente de uma VM de SQL Server?**

   Sim, mas você continuará a ser cobrado pela VM do SQL Server, conforme descrito em [Diretrizes de preços para VMs do Azure do SQL Server](pricing-guidance.md). Se você não precisar mais do SQL Server, você poderá implantar uma nova máquina virtual e migrar os dados e aplicativos para a nova máquina virtual. Em seguida, você pode remover a máquina virtual do SQL Server.
   
## <a name="updating-and-patching"></a>Atualização e aplicação de patch

1. **Como fazer alterar para uma versão/edição diferente do SQL Server em uma VM do Azure?**

   Os clientes podem alterar sua versão/edição do SQL Server usando a mídia de instalação que contém a versão desejada ou a edição do SQL Server. Depois que a edição for alterada, use o portal do Azure para modificar a propriedade de edição da VM para refletir com precisão a cobrança da VM. Para obter mais informações, confira [Alterar edição de uma VM do SQL Server](change-sql-server-edition.md). Não há nenhuma diferença de cobrança para versões diferentes do SQL Server, portanto, depois que a versão do SQL Server tiver sido alterada, nenhuma ação adicional será necessária.

1. **Onde posso obter a mídia de instalação para alterar a edição ou a versão do SQL Server?**

   Os clientes que têm [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) podem obter a mídia de instalação deles do [Centro de Licenciamento por Volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Os clientes que não têm o Software Assurance podem usar a mídia de instalação de um Azure Marketplace SQL Server imagem de VM que tem a edição desejada.
   
1. **Como as atualizações e os service packs são aplicados a uma VM do SQL Server?**

   As máquinas virtuais oferecem controle sobre o computador host, inclusive quando e como aplicar atualizações. Para o sistema operacional, você pode aplicar manualmente as atualizações do Windows ou habilitar um serviço de agendamento chamado [Aplicação de Patch Automatizada](automated-patching.md). A Aplicação de Patch Automatizada instala todas as atualizações marcadas como importantes, inclusive atualizações do SQL Server nessa categoria. Outras atualizações opcionais para o SQL Server devem ser instaladas manualmente.

1. **Posso fazer upgrade da minha instância do SQL Server 2008/2008 R2 depois de registrá-la no provedor de recursos de VM do SQL Server?**

   Sim. Você pode usar qualquer mídia de instalação para fazer upgrade da versão e da edição do SQL Server. Você também pode fazer upgrade do [modo de extensão de IaaS do SQL](sql-vm-resource-provider-register.md#management-modes) de _sem agente_ para _completo_. Isso dará acesso a todos os benefícios da extensão de IaaS do SQL, como gerenciamento de portal, backups automatizados e aplicação automatizada de patches. 

1. **Como posso obter atualizações de segurança estendidas gratuitas para o meu fim de suporte das instâncias do SQL Server 2008 e SQL Server 2008 R2?**

   Você pode obter [atualizações de segurança estendidas gratuitas](sql-server-2008-extend-end-of-support.md) movendo seu SQL Server no estado em que se encontra para uma máquina virtual do Azure. Para obter mais informações, confira [Opções de fim de suporte](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Geral

1. **Há suporte para FCIs (instâncias de cluster de failover) do SQL Server nas VMs do Azure?**

   Sim. Você pode instalar uma instância de cluster de failover usando [PFS (compartilhamentos de arquivos premium)](failover-cluster-instance-premium-file-share-manually-configure.md) ou [S2D (espaços de armazenamento diretos)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) para o subsistema de armazenamento. Os compartilhamentos de arquivos Premium oferecem capacidade de IOPS e taxa de transferência que atendem às necessidades de várias cargas de trabalho. Para cargas de trabalho com uso intensivo de E/S, considere o uso de espaços de armazenamento diretos baseados em discos gerenciados premium ou ultra. Como alternativa, você pode usar soluções de armazenamento ou cluster de terceiros, conforme descrito em [alta disponibilidade e recuperação de desastres para SQL Server em máquinas virtuais do Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > Neste momento, a [extensão do agente de IaaS do SQL Server](sql-server-iaas-agent-extension-automate-management.md) _completa_ não é compatível com a FCI do SQL Server no Azure. Recomendamos que você desinstale a extensão _completa_ das VMs que participam do FCI e instale a extensão no modo _leve_. Essa extensão oferece suporte a recursos, como Backup Automatizado e Aplicação Automatizada de Patches, além de alguns recursos do portal para SQL Server. Esses recursos não funcionarão para VMs do SQL Server após a desinstalação do agente _completo_.

1. **Qual é a diferença entre VMs do SQL Server e o serviço de Banco de Dados SQL?**

   Conceitualmente, a execução do SQL Server em uma máquina virtual do Azure não é diferente da execução do SQL Server em um datacenter remoto. Por outro lado, o [banco de dados SQL do Azure](../../database/sql-database-paas-overview.md) oferece o banco de dados como serviço. Com o Banco de Dados SQL, você não tem acesso às máquinas que hospedam os bancos de dados. Para obter uma comparação completa, confira [Escolher uma opção do SQL Server de nuvem: Banco de dados do Azure SQL (PaaS) ou SQL Server em máquinas virtuais do Azure (IaaS)](../../azure-sql-iaas-vs-paas-what-is-overview.md).

1. **Como instalar as ferramentas de Dados do SQL em minha VM do Azure?**

    Baixe e instale as ferramentas de Dados SQL por meio do [Microsoft SQL Server Data Tools – Business Intelligence para Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **As transações distribuídas com o MSDTC são compatíveis com VMs do SQL Server?**
   
    Sim. O DTC local oferece suporte ao SQL Server 2016 SP2 e posterior. No entanto, os aplicativos devem ser testados ao utilizar grupos de disponibilidade AlwaysOn, pois ocorrerá falha nas transações em andamento durante um failover e elas deverão ser repetidas. O DTC agrupado está disponível a partir do Windows Server 2019. 

## <a name="resources"></a>Recursos

**VMs do Windows**:

* [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Provisionar SQL Server em uma VM do Windows](create-sql-vm-portal.md)
* [Migração de um banco de dados para o SQL Server em uma VM do Azure](migrate-to-vm-from-sql-server.md)
* [Alta disponibilidade e recuperação de desastre para SQL Server em máquinas virtuais do Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Melhores práticas de desempenho para as Máquinas Virtuais do SQL Server no Azure](performance-guidelines-best-practices.md)
* [Padrões de aplicativo e estratégias de desenvolvimento para SQL Server em máquinas virtuais do Azure](application-patterns-development-strategies.md)

**VMs Linux**:

* [Visão geral do SQL Server em uma VM Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Provisionar SQL Server em uma VM do Linux](../linux/sql-vm-create-portal-quickstart.md)
* [Perguntas Frequentes (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server na documentação do Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
