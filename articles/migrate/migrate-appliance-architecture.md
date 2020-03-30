---
title: Arquitetura de aparelhos Azure Migrate
description: Fornece uma visão geral do aparelho Azure Migrate usado na avaliação e migração do servidor.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: d55d123bb056b46b5e78dd8ac836eeaf9b42fe70
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389011"
---
# <a name="azure-migrate-appliance-architecture"></a>Arquitetura de aparelhos Azure Migrate

Este artigo descreve a arquitetura e os processos do aparelho Azure Migrate. O aparelho Azure Migrate é um aparelho leve que é implantado no local, para descobrir VMs e servidores físicos para migração para o Azure. 

## <a name="deployment-scenarios"></a>Cenários de implantação

O aparelho Azure Migrate é usado nos seguintes cenários.

**Cenário** | **Ferramenta** | **Usado para** 
--- | --- | ---
**Avaliação vMware VM** | Migração do Azure:Avaliação do servidor | Descubra VMware VMs.<br/><br/> Descubra aplicativos de máquinas e dependências.<br/><br/> Coletar metadados da máquina e metadados de desempenho e enviar para o Azure.
**Migração vmware VM (sem agente)** | Migração do Azure:Migração do servidor | Descubra VMs VMware<br/><br/>  Replicar VMware VMs com [migração sem agente](server-migrate-overview.md).
**Avaliação de HiperV VM** | Migração do Azure:Avaliação do servidor | Descubra Hiper-V VMs.<br/><br/> Coletar metadados da máquina e metadados de desempenho e enviar para o Azure.
**Computador físico** |  Migração do Azure:Avaliação do servidor |  Descubra servidores físicos.<br/><br/> Coletar metadados da máquina e metadados de desempenho e enviar para o Azure.

## <a name="appliance-components"></a>Componentes do aparelho

O aparelho tem uma série de componentes.

- **Aplicativo de gerenciamento**: Este é um aplicativo web para entrada do usuário durante a implantação do aparelho. Usado na avaliação de máquinas para migração para o Azure.
- **Agente de descoberta**: O agente coleta dados de configuração da máquina. Usado na avaliação de máquinas para migração para o Azure. 
- **Agente de avaliação**: O agente coleta dados de desempenho. Usado na avaliação de máquinas para migração para o Azure.
- **Agente DRA**: Orquestra a replicação da VM e coordena a comunicação entre máquinas replicadas e o Azure. Usado apenas ao replicar VMs VMware para o Azure usando migração sem agente.
- **Gateway**: Envia dados replicados para o Azure. Usado apenas ao replicar VMs VMware para o Azure usando migração sem agente.
- **Serviço de atualização automática**: Atualiza os componentes do aparelho (funciona a cada 24 horas).



## <a name="appliance-deployment"></a>Implantação do aparelho

- O aparelho Azure Migrate pode ser configurado usando um modelo para [Hyper-V](how-to-set-up-appliance-hyper-v.md) ou [VMware](how-to-set-up-appliance-vmware.md) ou usando um instalador de script PowerShell para [VMware/Hyper-V](deploy-appliance-script.md)e para [servidores físicos](how-to-set-up-appliance-physical.md). 
- Os requisitos de suporte ao aparelho e os pré-requisitos de implantação são resumidos na [matriz de suporte](migrate-appliance.md)do aparelho .


## <a name="appliance-registration"></a>Registro de aparelhos

Durante a configuração do aparelho, você registra o aparelho com o Azure Migrate, e as ações resumidas na tabela ocorrem.

**Ação** | **Detalhes** | **Permissões**
--- | --- | ---
**Registre provedores de origem** | Esses provedores de recursos estão registrados na assinatura escolhida durante a configuração do aparelho: Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault.<br/><br/> O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. | Para registrar os provedores de recursos, você precisa de uma função de Colaborador ou Proprietário na assinatura.
**Criar a comunicação de aplicativos Azure AD** | O Azure Migrate cria um aplicativo Azure Active Directory (Azure AD) para comunicação (autenticação e autorização) entre os agentes que estão executando no aparelho e seus respectivos serviços em execução no Azure.<br/><br/> Este aplicativo não tem privilégios para fazer chamadas do Azure Resource Manager ou acesso rbac em qualquer recurso. | Você precisa [dessas permissões](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) para o Azure Migrar criar o aplicativo.
**Criar azure AD apps-Key vault** | Este aplicativo é criado apenas para migração sem agente de VMware VMs para o Azure.<br/><br/> Ele é usado exclusivamente para acessar o cofre de chaves criado na assinatura do usuário para migração sem agente.<br/><br/> Ele tem acesso RBAC no cofre de chaves do Azure (criado no inquilino do cliente), quando a descoberta é iniciada a partir do aparelho. | Você precisa [dessas permissões](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) para o Azure Migrar criar o aplicativo.



## <a name="collected-data"></a>Dados coletados

Os dados coletados pelo cliente para todos os cenários de implantação são resumidos na [matriz de suporte](migrate-appliance.md)do aparelho .

## <a name="discovery-and-collection-process"></a>Processo de descoberta e coleta

![Arquitetura](./media/migrate-appliance-architecture/architecture.png)

O aparelho se comunica com os hosts/cluster vCenter Servers e Hyper-V usando o seguinte processo.

1. **Iniciar a descoberta**:
    - Quando você inicia a descoberta no aparelho Hyper-V, ele se comunica com os hosts Hyper-V nas portas WinRM 5985 (HTTP) e 5986 (HTTPS).
    - Quando você inicia a descoberta no aparelho VMware, ele se comunica com o servidor vCenter na porta TCP 443 por padrão. Se o servidor vCenter ouvir em uma porta diferente, você pode configurá-lo no aplicativo web do aparelho.
2. **Coletar metadados e dados de desempenho:**
    - O aparelho usa uma sessão CIM (Common Information Model, modelo de informações comuns) para coletar dados do Hyper-VM do host Hyper-V nas portas 5985 e 5986.
    - O aparelho se comunica com a porta 443 por padrão, para coletar dados VMware VM do vCenter Server.
3. **Enviar dados**: O aparelho envia os dados coletados para a Avaliação do Servidor Migração do Azure e para a migração do servidor Azure Migrate sobre a porta SSL 443. O aparelho pode se conectar ao Azure pela internet, ou você pode usar o ExpressRoute com peering público/Microsoft.
    - Para dados de desempenho, o aparelho coleta dados de utilização em tempo real.
        - Os dados de desempenho são coletados a cada 20 segundos para o VMware e a cada 30 segundos para o Hyper-V, para cada métrica de desempenho.
        - Os dados coletados são enrolados para criar um único ponto de dados por 10 minutos.
        - O valor de utilização de pico é selecionado a partir de todos os pontos de dados de 20/30 segundos e enviado ao Azure para cálculo de avaliação.
        - Com base no valor do percentil especificado nas propriedades de avaliação (50th/90th/95th/99th), os pontos de dez minutos são classificados em ordem crescente, e o valor percental apropriado é usado para calcular a avaliação
    - Para migração de servidor, o aparelho começa a coletar dados de VM e os replica no Azure.
4. **Avaliar e migrar**: Agora você pode criar avaliações a partir dos metadados coletados pelo aparelho usando a Avaliação do Servidor Migração do Azure. Além disso, você também pode começar a migrar VMware VMs usando a Migração do Servidor Azure Migrate para orquestrar a replicação de VM sem agente.





## <a name="appliance-upgrades"></a>Atualizações de aparelhos

O aparelho é atualizado à medida que os agentes do Azure Migrate em execução no aparelho são atualizados. Isso acontece automaticamente porque a atualização automática é ativada no aparelho por padrão. Você pode alterar essa configuração padrão para atualizar os agentes manualmente.

Desative a atualização automática no registro definindo a tecla HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "AutoUpdate" para 0 (DWORD).

 

## <a name="next-steps"></a>Próximas etapas

[Revise](migrate-appliance.md) a matriz de suporte do aparelho.

