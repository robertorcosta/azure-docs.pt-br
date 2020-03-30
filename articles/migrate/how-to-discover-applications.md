---
title: Descubra aplicativos, funções e recursos em servidores locais com o Azure Migrate
description: Saiba como descobrir aplicativos, funções e recursos em servidores locais com a Avaliação do Servidor Migração do Azure.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: e8ce279afc845ebf37ad4ab8b2ce7236cb18137a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453575"
---
# <a name="discover-machine-apps-roles-and-features"></a>Descubra aplicativos, funções e recursos da máquina

Este artigo descreve como descobrir aplicativos, funções e recursos em servidores locais, usando o Azure Migrate: Server Assessment.

Descobrir o inventário de aplicativos e funções/recursos em execução em suas máquinas locais ajuda você a identificar e planejar um caminho de migração para o Azure que é adaptado para suas cargas de trabalho.

> [!NOTE]
> A descoberta do aplicativo está atualmente em pré-visualização apenas para VMware VMs, e está limitada apenas à descoberta. Ainda não oferecemos avaliação baseada em aplicativos. Avaliação baseada em máquina para VMMs VMware no local, VMs Hyper-V e servidores físicos.

A descoberta do aplicativo usando o Azure Migrate: A avaliação do servidor não tem agente. Nada é instalado em máquinas e VMs. A Avaliação do Servidor usa o aparelho Azure Migrate para realizar a descoberta juntamente com as credenciais de hóspedes da máquina. O aparelho acessa remotamente as máquinas VMware usando APIs VMware.


## <a name="before-you-start"></a>Antes de começar

1. Certifique-se de [ter criado](how-to-add-tool-first-time.md) um projeto azure Migrate.
2. Certifique-se de ter [adicionado](how-to-assess.md) a ferramenta Azure Migrate: Server Assessment a um projeto.
4. Verifique os requisitos da [VMware](migrate-support-matrix-vmware.md#vmware-requirements) para descobrir e avaliar VMs VMware com o aparelho Azure Migrate.
5. Verifique os [requisitos](migrate-appliance.md) para implantar o aparelho Azure Migrate.
6. [Verifique o suporte e os requisitos](migrate-support-matrix-vmware.md#application-discovery) para a detecção do aplicativo.

## <a name="prepare-for-app-discovery"></a>Prepare-se para a descoberta do aplicativo

1. [Prepare-se para a implantação do aparelho](tutorial-prepare-vmware.md). A preparação inclui verificar as configurações do aparelho e configurar uma conta que o aparelho usará para acessar o vCenter Server.
2. Certifique-se de ter uma conta de usuário (uma para servidores Windows e Linux) com permissões de administrador para máquinas nas quais você deseja descobrir aplicativos, funções e recursos.
3. [Implante o aparelho Azure Migrate](how-to-set-up-appliance-vmware.md) para iniciar a descoberta. Para implantar o aparelho, você baixa e importa um modelo OVA no VMware para criar o aparelho como um VMware VM. Você configura o aparelho e, em seguida, registrá-lo com o Azure Migrate.
2. Ao implantar o aparelho, para iniciar a descoberta contínua, você especifica o seguinte:
    - O nome do servidor vCenter ao qual você deseja conectar.
    - Credenciais que você criou para que o aparelho se conecte ao vCenter Server.
    - As credenciais de conta que você criou para o aparelho se conectar às VMs Windows/Linux.

Depois que o aparelho é implantado e você fornece credenciais, o aparelho inicia a descoberta contínua de metadados e dados de desempenho da VM, juntamente com a descoberta de aplicativos, recursos e funções.  A duração da descoberta do aplicativo depende de quantas VMs você tem. Normalmente leva uma hora para a descoberta de 500 VMs.

## <a name="review-and-export-the-inventory"></a>Revisar e exportar o inventário

Após o término da descoberta, se você forneceu credenciais para a descoberta do aplicativo, você pode rever e exportar o inventário do aplicativo no portal Azure.

1. No **Azure Migrate - Servers** > **Azure Migrate: Server Assessment**, clique na contagem exibida para abrir a página servidores **descobertos.**

    > [!NOTE]
    > Nesta fase, você também pode configurar opcionalmente o mapeamento de dependência para máquinas descobertas, para que você possa visualizar dependências entre máquinas que deseja avaliar. [Saiba mais](how-to-create-group-machine-dependencies.md).

2. Em **Aplicativos descobertos,** clique na contagem exibida.
3. No **inventário do aplicativo,** você pode rever os aplicativos, funções e recursos descobertos.
4. Para exportar o inventário, em **Servidores Descobertos,** clique em **Exportar inventário do aplicativo**.

O inventário do aplicativo é exportado e baixado no formato Excel. A folha **Inventário de aplicativos** exibe todos os aplicativos descobertos em todas as máquinas.

## <a name="next-steps"></a>Próximas etapas

- [Crie uma avaliação](how-to-create-assessment.md) para migração de elevadores e turnos dos servidores descobertos.
- Avalie um banco de dados SQL Server usando [o Azure Migrate: Avaliação de banco de dados](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
