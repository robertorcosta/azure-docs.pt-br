---
title: Matriz de suporte azure Migrate
description: Fornece um resumo das configurações de suporte e limitações para o serviço Azure Migrate.
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: raynew
ms.openlocfilehash: 0f766bf95bb7e26d942e7dde3f315bbef6d5dc5c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535189"
---
# <a name="azure-migrate-support-matrix"></a>Matriz de suporte azure Migrate

Você pode usar o [serviço Azure Migrate](migrate-overview.md) para avaliar e migrar máquinas para a nuvem do Microsoft Azure. Este artigo resume as configurações gerais de suporte e as limitações para cenários e implantações do Azure Migrate.

## <a name="supported-assessmentmigration-scenarios"></a>Cenários de avaliação/migração suportados

A tabela resume os cenários de descoberta, avaliação e migração suportados.

**Implantação** | **Detalhes** 
--- | --- 
**Descoberta específica do aplicativo** | Você pode descobrir aplicativos, funções e recursos em execução em VMS VMware. Atualmente, esse recurso está limitado apenas à descoberta. A avaliação está atualmente no nível da máquina. Ainda não oferecemos avaliação específica de aplicativo, função ou recurso. 
**Avaliação no local** | Avalie as cargas de trabalho e os dados em execução em VMs, VMs hyper-V e servidores físicos. Avalie usando o Azure Migrate Server Assessment e o Microsoft Data Migration Assistant (DMA), bem como outras ferramentas e ofertas de ISV.
**Migração no local para o Azure** | Migrar cargas de trabalho e dados em execução em servidores físicos, VMware VMs, VMs Hyper-V, servidores físicos e VMS baseados em nuvem para o Azure. Migre usando o Azure Migrate Server Assessment e o Azure Database Migration Service (DMS), bem como outras ferramentas e ofertas de ISV.


## <a name="supported-tools"></a>Ferramentas compatíveis

O suporte específico da ferramenta é resumido na tabela.

**Ferramenta** | **Avaliar** | **Migrar** 
--- | --- | ---
Avaliação de Servidor das Migrações para Azure | Avalie [vms vmware,](tutorial-prepare-vmware.md) [VMs hyper-v](tutorial-prepare-hyper-v.md)e [servidores físicos](tutorial-prepare-physical.md). |  Não disponível (NA)
Migração de Servidor das Migrações para Azure | NA | Migrar [VMs VMware,](tutorial-migrate-vmware.md) [VMs Hyper-V](tutorial-migrate-hyper-v.md)e [servidores físicos](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | NA | Migrar VMs VMware, VMs Hyper-V, servidores físicos, cargas de trabalho em nuvem pública. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Avalie VMs VMware, VMs hyper-V, servidores físicos, cargas de trabalho em nuvem pública. | NA
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Avalie e migre VMs VMware, VMs Hyper-V, servidores físicos, cargas de trabalho em nuvem pública. |  Migrar VMs VMware, VMs Hyper-V, servidores físicos, cargas de trabalho em nuvem pública.
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Avalie VMs VMware, VMs hyper-V, servidores físicos, cargas de trabalho em nuvem pública.| NA
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Avalie os bancos de dados SQL Server no local. | NA
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | NA | Migrar SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avalie a infra-estrutura de desktop virtual (VDI) | NA
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | Avalie VMs VMs, VMs Hyper-V, Xen VMs, máquinas físicas, estações de trabalho (incluindo VDI), cargas de trabalho em nuvem pública | NA
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | NA | Migrar VMS VMs, VMs Hyper-V, Xen VMs, VMs KVMs, máquinas físicas, cargas de trabalho em nuvem pública 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Avalie VMs VMware, VMs hyper-V, servidores físicos, cargas de trabalho em nuvem pública. | NA
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Avalie vms vms, VMs hyper-v, servidores físicos, cargas de trabalho em nuvem pública e bancos de dados SQL Server. | NA
[Assistente de migração do Webapp](https://appmigration.microsoft.com/) | Avalie os aplicativos web | Migrar aplicativos web.


## <a name="azure-migrate-projects"></a>Projetos do Azure Migrate

**Suporte** | **Detalhes**
--- | ---
Subscription | Você pode ter vários projetos do Azure Migrate em uma assinatura.
Permissões do Azure | Você precisa de permissões de Contribuinte ou Proprietário na assinatura para criar um projeto Do Zure Migrate.
VMs VMware  | Avalie até 35.000 VMs VMware em um único projeto.
VMs Hyper-V    | Avalie até 35.000 VMs Hiper-V em um único projeto.

Um projeto pode incluir vms VMware e Hyper-VMs, até os limites de avaliação.

## <a name="azure-permissions"></a>Permissões do Azure

Para o Azure Migrar para trabalhar com o Azure, você precisa dessas permissões antes de começar a avaliar e migrar máquinas.

**Tarefa** | **Permissões** | **Detalhes**
--- | --- | ---
Criar um projeto das Migrações para Azure | Sua conta do Azure precisa de permissões para criar um projeto. | Configuração para [servidores VMware,](tutorial-prepare-vmware.md#assign-permissions-to-create-project) [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)ou [físicos.](tutorial-prepare-physical.md#assign-permissions-to-create-project)
Registrar o dispositivo de Migrações para Azure| O Azure Migrate usa um [aparelho leve do Azure Migrate](migrate-appliance.md) para avaliar máquinas com o Azure Migrate Server Assessment e executar a [migração sem agente](server-migrate-overview.md) de VMs do VMware com o Azure Migrate Server Migration. Este aparelho descobre máquinas e envia metadados e dados de desempenho para o Azure Migrate.<br/><br/> Durante o registro, os provedores de registro (Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault) são registrados com a assinatura escolhida no aparelho, para que a assinatura funcione com o provedor de recursos. Para se cadastrar, você precisa acessar o Contribuinte ou proprietário na assinatura.<br/><br/> **VMware**-Durante o onboarding, o Azure Migrate cria dois aplicativos do Azure Active Directory (Azure AD). O primeiro aplicativo se comunica entre os agentes do aparelho e o serviço Azure Migrate. O aplicativo não tem permissões para fazer chamadas de gerenciamento de recursos do Azure ou ter acesso ao RBAC para recursos. O segundo aplicativo acessa um Azure Key Vault criado na assinatura do usuário apenas para migração de VMware sem agente. Na migração sem agente, o Azure Migrate cria um Key Vault para gerenciar as chaves de acesso à conta de armazenamento de replicação em sua assinatura. Ele tem acesso RBAC no Azure Key Vault (no inquilino do cliente) quando a descoberta é iniciada a partir do aparelho.<br/><br/> **Hiper-V**- Durante o embarque. O Azure Migrate cria um aplicativo Azure AD. O aplicativo se comunica entre os agentes do aparelho e o serviço Azure Migrate. O aplicativo não tem permissões para fazer chamadas de gerenciamento de recursos do Azure ou ter acesso ao RBAC para recursos. | Configuração para [servidores VMware,](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)ou [físicos.](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance)
Crie um cofre-chave para migração sem agente VMware | Para migrar vMs vmware com migração de servidor Azure Migrate sem agente, o Azure Migrate cria um Key Vault para gerenciar chaves de acesso à conta de armazenamento de replicação em sua assinatura. Para criar o cofre, você define permissões (Proprietário, ou Administrador de Acesso ao Usuário) no grupo de recursos em que o projeto Azure Migrate reside. | [Configure](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) permissões.

## <a name="supported-geographies-public-cloud"></a>Geografias suportadas (nuvem pública)

Você pode criar um projeto Azure Migrate em várias geografias na nuvem pública. Embora você só possa criar projetos nessas geografias, você pode avaliar ou migrar máquinas para outros locais-alvo. A região geográfica do projeto é usada apenas para armazenar os metadados descobertos.

**Geografia** | **Local de armazenamento de metadados**
--- | ---
Pacífico Asiático | Leste da Ásia ou Sudeste Asiático
Austrália | Austrália Leste ou Austrália Sudeste
Brasil | Sul do Brasil
Canada | Canadá Central ou Canadá Leste
Europa | Norte da Europa ou Europa Ocidental
França | França Central
Índia | Índia Central ou Índia do Sul
Japão |  Japão Leste ou Japão Oeste
Coreia do Sul | Coreia Central ou Coreia do Sul
United Kingdom | Sul do Reino Unido ou Oeste do Reino Unido
Estados Unidos | Eua Central ou Oeste dos EUA 2


## <a name="supported-geographies-azure-government"></a>Geografias apoiadas (Governo Azure)

**Tarefa** | **Geografia** | **Detalhes**
--- | --- | ---
Criar projeto | Estados Unidos | Metadados são armazenados em US Gov Arizona, US Gov Virginia
Avaliação de metas | Estados Unidos | Regiões alvo: US Gov Arizona, US Gov Virginia/US Gov Texas
Replicação de destino | Estados Unidos | Regiões alvo: US DoD Central, US DoD East, US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia


## <a name="vmware-assessment-and-migration"></a>Avaliação e migração de VMware

[Revise](migrate-support-matrix-vmware.md) a matriz de suporte de avaliação e migração de servidor do Azure Migrate para VMs VMware.

## <a name="hyper-v-assessment-and-migration"></a>Avaliação e migração do Hyper-V

[Revise](migrate-support-matrix-hyper-v.md) a matriz de suporte de avaliação e migração de servidor do Azure Migrate para VMs hiper-V.



## <a name="azure-migrate-versions"></a>Versões das Migrações para Azure

Há duas versões do serviço de Migrações para Azure:

- **Versão atual**: Usando esta versão, você pode criar novos projetos do Azure Migrate, descobrir avaliações no local e orquestrar avaliações e migrações. [Saiba mais](whats-new.md).
- **Versão anterior**: Para o cliente que usa a versão anterior do Azure Migrate (apenas a avaliação das VMms VMware no local foi suportada), agora você deve usar a versão atual. Na versão anterior, você não pode criar novos projetos do Azure Migrate ou realizar novas descobertas.

## <a name="next-steps"></a>Próximas etapas

- [Avalie as VMs VMware](tutorial-assess-vmware.md) para migração.
- [Avalie os VMs hyper-v](tutorial-assess-hyper-v.md) para migração.

