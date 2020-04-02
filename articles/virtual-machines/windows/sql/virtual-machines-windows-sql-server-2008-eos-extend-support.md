---
title: Estender o suporte para SQL Server 2008 & 2008 R2
description: Amplie o suporte ao SQL Server 2008 e ao SQL Server 2008 R2 migrando sua instância do SQL Server para o Azure ou comprando suporte estendido para manter instâncias no local.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: faaff9e9ad5982efac6409f2284158a3d0711331
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548400"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Estender o suporte para SQL Server 2008 e SQL Server 2008 R2 com o Azure

O SQL Server 2008 e o SQL Server 2008 R2 chegaram ao [fim do ciclo de vida do suporte (EOS).](https://www.microsoft.com/sql-server/sql-server-2008) Como muitos clientes ainda estão usando ambas as versões, estamos fornecendo várias opções para continuar recebendo suporte. Você pode migrar suas instâncias do SQL Server no local para as Máquinas Virtuais (VMs) do Azure, migrar para o Banco de Dados SQL do Azure ou permanecer no local e comprar atualizações de segurança estendidas.

Ao contrário de uma instância gerenciada, migrar para uma VM Azure não requer recertificar seus aplicativos. E ao contrário de permanecer no local, você receberá patches de segurança estendidos gratuitos migrando para uma VM Azure.

O resto deste artigo fornece considerações para migrar sua instância do SQL Server para uma VM Do Zure.

Para obter mais informações sobre o fim das opções de suporte, consulte [Fim do suporte](/sql/sql-server/end-of-support/sql-server-end-of-life-overview).

## <a name="provisioning"></a>Provisionamento

Há uma imagem **SQL Server 2008 R2 no Windows Server 2008 R2** disponível no Azure Marketplace.

Os clientes que estão no SQL Server 2008 precisarão se autoinstalar ou atualizar para o SQL Server 2008 R2. Da mesma forma, os clientes no Windows Server 2008 precisarão implantar sua VM a partir de um VHD personalizado ou atualizar para o Windows Server 2008 R2.

As imagens implantadas através do Azure Marketplace vêm com a extensão SQL IaaS pré-instalada. A extensão SQL IaaS é um requisito para licenciamento flexível e patches automatizados. Os clientes que implantarem VMs autoinstaladas precisarão instalar manualmente a extensão SQL IaaS. A extensão SQL IaaS não é suportada no Windows Server 2008.

> [!NOTE]
> Embora as lâminas SQL Server **Create** and **Manage** funcionem com a imagem SQL Server 2008 R2 no portal Azure, os seguintes recursos não são _suportados:_ backups automáticos, integração do Azure Key Vault, Serviços R e configuração de armazenamento.

## <a name="licensing"></a>Licenciamento
As implantações do Pay-as-you-go SQL Server 2008 R2 podem ser convertidas em [Benefícios Híbridos do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Para converter uma licença baseada em Garantia de Software (SA) para pagar como você-vai, os clientes devem se registrar no [provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md)SQL VM . Após esse registro, o tipo de licença SQL será intercambiável entre o Azure Hybrid Benefit e o pay-as-you-go.

As instâncias SQL Server 2008 ou SQL Server 2008 R2 autoinstaladas em uma VM Azure podem se registrar no provedor de recursos SQL VM e converter seu tipo de licença em pay-as-you-go.

## <a name="migration"></a>Migração
Você pode migrar as instâncias do EOS SQL Server para uma VM Azure com métodos manuais de backup/restauração. Este é o método de migração mais comum de on-premises para uma VM Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Para migrações em massa, recomendamos o serviço [de recuperação de sites do Azure.](/azure/site-recovery/site-recovery-overview) Com o Azure Site Recovery, os clientes podem replicar toda a VM, incluindo o SQL Server de on-premises para Azure VM.

O SQL Server requer instantâneos de recuperação do site azure consistentes com o aplicativo para garantir a recuperação. O Azure Site Recovery suporta instantâneos consistentes com aplicativos com um intervalo mínimo de 1 hora. O RPO (minimum recovery point objective, objetivo de ponto de recuperação mínimo) possível para o SQL Server com migrações de recuperação do site do Azure é de 1 hora. O objetivo do tempo de recuperação (RTO) é de 2 horas mais o tempo de recuperação do SQL Server.

### <a name="database-migration-service"></a>Serviço de Migração de Banco de Dados

O [Serviço de Migração](/azure/dms/dms-overview) de Banco de Dados é uma opção para os clientes se eles estiverem migrando de locais para uma VM Azure, atualizando o SQL Server para a versão de 2012 ou posterior.

## <a name="disaster-recovery"></a>Recuperação de desastre

As soluções de recuperação de desastres para o EOS SQL Server em uma VM Azure são as seguintes:

- **Backups do SQL Server**: Use o Backup do Azure para ajudar a proteger o EOS SQL Server 2008 e o 2008 R2 contra ransomware, exclusão acidental e corrupção com RPO de 15 min e recuperação point-in-time. Para mais detalhes, consulte [este artigo](https://docs.microsoft.com/azure/backup/sql-support-matrix#scenario-support).
- **Log de transporte**: Você pode criar uma réplica de log hipping em outra região ou região do Azure com restaurações contínuas para reduzir o RTO. Você precisa configurar manualmente o envio de log.
- **Azure Site Recovery**: Você pode replicar sua VM entre zonas e regiões através da replicação do Azure Site Recovery. O SQL Server requer instantâneos consistentes com aplicativos para garantir a recuperação em caso de desastre. O Azure Site Recovery oferece um RPO mínimo de 1 hora e um RTO de 2 horas (mais o tempo de recuperação do SQL Server) para recuperação de desastres do EOS SQL Server.

## <a name="security-patching"></a>Patches de segurança
Atualizações de segurança estendidas para VMs do SQL Server são fornecidas através dos canais de atualização da Microsoft depois que o VM do SQL Server foi registrado no [provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md)SQL VM . Os patches podem ser baixados manualmente ou automaticamente.

*Automated patching* está habilitada por padrão. A aplicação de patch automatizada permite que o Azure aplique patches automaticamente no SQL Server e no sistema operacional. Você pode especificar um dia da semana, hora e duração de uma janela de manutenção se a extensão SQL Server IaaS estiver instalada. O Azure realiza a aplicação de patch na janela de manutenção. O agendamento do período de manutenção usa a localidade da VM para a hora.  Para obter mais informações, consulte [Patches automatizados para SQL Server em Máquinas Virtuais Do Azure](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Próximas etapas

Migre seu VM do Servidor SQL para o Azure:

* [Migrar um banco de dados do SQL Server para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md)

Comece com o SQL Server em Máquinas Virtuais do Azure:

* [Criar uma VM do SQL Server no portal do Azure](quickstart-sql-vm-create-portal.md)

Obtenha respostas para perguntas comumente feitas sobre VMs do SQL Server:

* [Perguntas frequentes para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-faq.md)

Saiba mais sobre o fim das opções de suporte e atualizações de segurança estendidas:

* [Fim do suporte](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & [Atualizações estendidas](/sql/sql-server/end-of-support/sql-server-extended-security-updates) de segurança