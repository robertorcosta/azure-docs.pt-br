---
title: Matriz de suporte das Migrações para Azure
description: Fornece um resumo de configurações compatíveis e limitações do serviço de Migrações para Azure.
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: raynew
ms.openlocfilehash: 8350f557efd9224d92388835f55871cb861eda25
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108746"
---
# <a name="azure-migrate-support-matrix"></a>Matriz de suporte das Migrações para Azure

Você pode usar o [serviço de Migrações para Azure](./migrate-services-overview.md) para avaliar e migrar computadores para a nuvem do Microsoft Azure. Este artigo resume as configurações e limitações de suporte geral para cenários e implantações das Migrações para Azure.

## <a name="supported-assessmentmigration-scenarios"></a>Cenários de avaliação/migração com suporte

A tabela resume os cenários de descoberta, avaliação e migração com suporte.

**Implantação** | **Detalhes** 
--- | --- 
**Descoberta específica do aplicativo** | Você pode descobrir aplicativos, funções e recursos em execução em VMs do VMware. Atualmente, esse recurso está limitado apenas à descoberta. A avaliação está atualmente no nível do computador. Ainda não oferecemos avaliação específica a aplicativo, papel ou recurso. 
**Avaliação locais** | Avalie cargas de trabalho locais e dados em execução em VMs do VMware, VMs do Hyper-V e servidores físicos. Avalie o uso da Avaliação de Servidor de Migrações para Azure e do Assistente de Migração de Dados da Microsoft (DMA), além de outras ferramentas e ofertas de ISV.
**Migração local para o Azure** | Migre cargas de trabalho e dados em execução em servidores físicos, VMs do VMware, VMs do Hyper-V, servidores físicos e VMS baseadas em nuvem para o Azure. Use a Avaliação de Servidor de Migrações para Azure e o Serviço de Migração de Banco de Dados do Azure (DMS), além de outras ferramentas e ofertas de ISV, para migração.

> [!NOTE]
> Atualmente, as ferramentas de ISV não podem enviar dados para Migrações para Azure no Azure Governamental. Você pode usar as ferramentas integradas da Microsoft ou as ferramentas de parceiro de forma independente.

## <a name="supported-tools"></a>Ferramentas com suporte

O suporte a ferramentas específicas é resumido na tabela.

**Ferramenta** | **Avaliar** | **Migrar** 
--- | --- | ---
Avaliação de Servidor das Migrações para Azure | Avalie [VMs do VMware](tutorial-prepare-vmware.md), [VMs do Hyper-V](tutorial-prepare-hyper-v.md) e [servidores físicos](tutorial-prepare-physical.md). |  Não disponível (ND)
Migração de Servidor das Migrações para Azure | NA | Migre [VMs do VMware](tutorial-migrate-vmware.md), [VMs do Hyper-V](tutorial-migrate-hyper-v.md) e [servidores físicos](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | NA | Migrar VMs do VMware, VMs do Hyper-V, servidores físicos e cargas de trabalho de nuvem pública. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Acessar VMs do VMware, VMs do Hyper-V, servidores físicos e cargas de trabalho de nuvem pública. | NA
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Avaliar e migrar VMs do VMware, VMs do Hyper-V, servidores físicos e cargas de trabalho de nuvem pública. |  Migrar VMs do VMware, VMs do Hyper-V, servidores físicos e cargas de trabalho de nuvem pública.
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Acessar VMs do VMware, VMs do Hyper-V, servidores físicos e cargas de trabalho de nuvem pública.| NA
[DMA](/sql/dma/dma-overview?view=sql-server-2017) | Avaliar SQL Server bancos de dados. | NA
[DMS](../dms/dms-overview.md) | NA | Migrar SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avaliar a infraestrutura de área de trabalho virtual (VDI) | NA
[Movere](https://www.movere.io/) | Avaliar VMs do VMWare, VMs do Hyper-V, VMs do Xen, computadores físicos, estações de trabalho (incluindo VDI), cargas de trabalho de nuvem pública | NA
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | NA | Migrar VMs do VMware, VMs do Hyper-V, VMs do Xen, VMs do KVM, computadores físicos, cargas de trabalho de nuvem pública 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Acessar VMs do VMware, VMs do Hyper-V, servidores físicos e cargas de trabalho de nuvem pública. | NA
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Acessar VMs do VMware, VMs do Hyper-V, servidores físicos, cargas de trabalho de nuvem pública e bancos de dados do SQL Server. | NA
[Assistente de Migração de aplicativo Web](https://appmigration.microsoft.com/) | Avaliar aplicativos Web | Migrar aplicativos Web.


## <a name="azure-migrate-projects"></a>Projetos das Migrações para Azure

**Suporte** | **Detalhes**
--- | ---
Subscription | Você pode ter vários projetos das Migrações para Azure em uma assinatura.
Permissões do Azure | Você precisa de permissões de colaborador ou de proprietário na assinatura para criar um projeto das Migrações para Azure.
VMs VMware  | Avalie até 35.000 VMs do VMware em um único projeto.
VMs Hyper-V    | Avalie até 35.000 VMs do Hyper-V em um único projeto.

Um projeto pode conter VMs do VMware e do Hyper-V, até os limites da avaliação.

## <a name="azure-permissions"></a>Permissões do Azure

Para que as Migrações para Azure funcionem com o Azure, você precisa dessas permissões antes de começar a avaliar e migrar computadores.

**Tarefa** | **Permissões** | **Detalhes**
--- | --- | ---
Criar um projeto das Migrações para Azure | Sua conta do Azure precisa de permissões para criar um projeto. | Configurar para [VMware](tutorial-prepare-vmware.md#assign-permissions-to-create-project), [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project) ou [servidores físicos](tutorial-prepare-physical.md#assign-permissions-to-create-project).
Registrar o dispositivo de Migrações para Azure| As Migrações para Azure usam um [dispositivo leve de Migrações para Azure](migrate-appliance.md) para avaliar computadores com a avaliação de servidor de Migrações para Azure e executar a [migração sem agente](server-migrate-overview.md) de VMs do VMware com a migração de servidor das Migrações para Azure. Esse dispositivo executa a descoberta de computadores e envia os metadados e os dados de desempenho para as Migrações para Azure.<br/><br/> Durante o registro do dispositivo, os provedores de recursos (Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault) são registrados com a assinatura escolhida no dispositivo para que a assinatura funcione com o provedor de recursos. Para se registrar, você precisa de acesso de colaborador ou proprietário na assinatura.<br/><br/> **VMware** – Durante a integração, as Migrações para Azure criam dois aplicativos do Azure Active Directory. O primeiro aplicativo faz a comunicação entre os agentes do dispositivo e o serviço de Migrações para Azure. O aplicativo não tem permissões para fazer chamadas de gerenciamento de recursos do Azure ou ter acesso de RBAC para recursos. O segundo aplicativo acessa um Azure Key Vault criado na assinatura do usuário somente para a migração do VMware sem agente. Na migração sem agente, as Migrações para Azure criam um cofre de chaves para gerenciar chaves de acesso para a conta de armazenamento de replicação na sua assinatura. Ele tem acesso RBAC no Azure Key Vault (no locatário do cliente) quando a descoberta é iniciada no dispositivo.<br/><br/> **Hyper-V** - Durante a integração. As Migrações para Azure criam um aplicativo do Azure Active Directory. O aplicativo faz a comunicação entre os agentes do dispositivo e o serviço de Migrações para Azure. O aplicativo não tem permissões para fazer chamadas de gerenciamento de recursos do Azure ou ter acesso de RBAC para recursos. | Configurar para [VMware](tutorial-prepare-vmware.md#assign-permissions-to-create-azure-ad-apps), [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-azure-ad-apps) ou [servidores físicos](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance).
Criar um cofre de chaves para migração sem agente do VMware | Para migrar VMs do VMware usando a Migração de Servidor de Migrações para Azure sem agente, as Migrações para Azure criam um cofre de chaves para gerenciar chaves de acesso para a conta de armazenamento de replicação na sua assinatura. Para criar o cofre, defina as permissões (proprietário ou colaborador e o administrador de acesso de usuários) no grupo de recursos no qual o projeto de Migrações para Azure reside. | [Configurar](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) permissões.

## <a name="supported-geographies-public-cloud"></a>Regiões geográficas com suporte (nuvem pública)

Você pode criar um projeto de Migrações para Azure em várias regiões geográficas na nuvem pública. Embora os projetos possam ser criados apenas nessas regiões geográficas, você pode avaliar ou migrar computadores para outros locais de destino. A região geográfica do projeto é usada apenas para armazenar os metadados descobertos.

**Geografia** | **Local de armazenamento de metadados**
--- | ---
Pacífico Asiático | Leste da Ásia ou Sudeste da Ásia
Austrália | Leste da Austrália ou Sudeste da Austrália
Brasil | Sul do Brasil
Canada | Canadá Central ou Leste do Canadá
Europa | Norte da Europa ou Europa Ocidental
França | França Central
Índia | Índia Central ou Sul da Índia
Japão |  Leste do Japão ou Oeste do Japão
Coreia do Sul | Coreia Central ou Sul da Coreia
United Kingdom | Sul do Reino Unido ou Oeste do Reino Unido
Estados Unidos | EUA Central ou Oeste dos EUA 2


## <a name="supported-geographies-azure-government"></a>Geografias com suporte (Azure Governamental)

**Tarefa** | **Geografia** | **Detalhes**
--- | --- | ---
Criar projeto | Estados Unidos | Os metadados são armazenados em US Gov - Arizona, US Gov - Virgínia
Avaliação do destino | Estados Unidos | Regiões de destino: US Gov - Arizona, US Gov - Virgínia, US Gov - Texas
Replicação de destino | Estados Unidos | Regiões de destino: US DoD Central, Leste do US DoD, US Gov - Arizona, US Gov Iowa,US Gov - Texas, US Gov - Virgínia


## <a name="vmware-assessment-and-migration"></a>Avaliação e migração do VMware

[Examine](migrate-support-matrix-vmware.md) a matriz de suporte da migração de servidor e avaliação de servidor das Migrações para Azure para VMs do VMware.

## <a name="hyper-v-assessment-and-migration"></a>Avaliação e migração do Hyper-V

[Examine](migrate-support-matrix-hyper-v.md) a matriz de suporte da migração de servidor e avaliação de servidor das Migrações para Azure para VMs do Hyper-V.



## <a name="azure-migrate-versions"></a>Versões das Migrações para Azure

Há duas versões do serviço de Migrações para Azure:

- **Versão atual**: use esta versão para criar novos projetos de Migrações para Azure, descobrir avaliações locais e orquestrar avaliações e migrações. [Saiba mais](whats-new.md).
- **Versão anterior**: para o cliente que usa a versão anterior das Migrações para Azure (havia suporte apenas a avaliação de VMs do VMware locais), agora você deve usar a versão atual. Na versão anterior, você não pode criar novos projetos de Migrações para Azure ou executar novas descobertas.

## <a name="next-steps"></a>Próximas etapas

- [Avaliar VMs do VMware](tutorial-assess-vmware.md) para migração.
- [Avaliar VMs do Hyper-V](tutorial-assess-hyper-v.md) para migração.
