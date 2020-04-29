---
title: Aprenda a auditar o conteúdo de máquinas virtuais
description: Saiba como Azure Policy usa o agente de configuração do convidado para auditar as configurações nas máquinas virtuais.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 89f7cc3931971d70b441490f77b67ace89434c2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025213"
---
# <a name="understand-azure-policys-guest-configuration"></a>Entender a Configuração de Convidado do Azure Policy

Além da auditoria e da [correção](../how-to/remediate-resources.md) dos recursos do Azure, Azure Policy pode auditar as configurações dentro de um computador. A validação é executada pela extensão e pelo cliente de Configuração de Convidado. A extensão, por meio do cliente, valida as configurações como:

- A configuração do sistema operacional
- Configuração ou presença do aplicativo
- Configurações do ambiente

Neste momento, a maioria Azure Policy políticas de configuração de convidado apenas as configurações de auditoria dentro do computador. Eles não aplicam configurações. A exceção é uma política interna [referenciada abaixo](#applying-configurations-using-guest-configuration).

## <a name="resource-provider"></a>Provedor de recursos

Antes de usar a Configuração de Convidado, você precisa registrar o provedor de recursos. O provedor de recursos será registrado automaticamente se a atribuição de uma política de configuração de convidado for feita por meio do Portal. Você pode registrar manualmente por meio do [portal](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)ou [CLI do Azure](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

## <a name="extension-and-client"></a>Extensão e cliente

Para auditar as configurações dentro de um computador, uma [extensão de máquina virtual](../../../virtual-machines/extensions/overview.md) está habilitada. A extensão baixa a atribuição de política aplicável e a definição de configuração correspondente.

> [!Important]
> A extensão de configuração de convidado é necessária para executar auditorias em máquinas virtuais do Azure.
> Para implantar a extensão em escala, atribua as seguintes definições de política:
>   - [Implantar os pré-requisitos para habilitar a Política de Configuração de Convidado nas VMs do Windows.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
>   - [Implantar os pré-requisitos para habilitar a Política de Configuração de Convidado nas VMs do Linux.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

### <a name="limits-set-on-the-extension"></a>Limites definidos na extensão

Para limitar a extensão de afetar os aplicativos em execução dentro do computador, a configuração de convidado não tem permissão para exceder mais de 5% da CPU. Essa limitação existe para as definições internas e personalizadas.

### <a name="validation-tools"></a>Ferramentas de validação

Dentro do computador, o cliente de configuração de convidado usa ferramentas locais para executar a auditoria.

A tabela a seguir mostra uma lista das ferramentas locais usadas em cada sistema operacional com suporte:

|Sistema operacional|Ferramenta de validação|Anotações|
|-|-|-|
|Windows|[Configuração de estado desejado do Windows PowerShell](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Se Ruby e Python não estiverem no computador, eles serão instalados pela extensão de configuração de convidado. |

### <a name="validation-frequency"></a>Frequência de validação

O cliente de Configuração Convidado verifica o novo conteúdo a cada 5 minutos. Depois que uma atribuição de convidado é recebida, as configurações dessa configuração são verificadas novamente em um intervalo de 15 minutos.
Os resultados são enviados para o provedor de recursos de configuração de convidado quando a auditoria é concluída. Quando ocorre um [gatilho de avaliação](../how-to/get-compliance-data.md#evaluation-triggers) de política, o estado do computador é gravado no provedor de recursos de Configuração do Convidado. Essa atualização faz com que Azure Policy avaliar as propriedades de Azure Resource Manager. Uma avaliação de Azure Policy sob demanda recupera o valor mais recente do provedor de recursos de configuração de convidado. No entanto, ele não aciona uma nova auditoria da configuração no computador.

## <a name="supported-client-types"></a>Tipos de clientes com suporte

As políticas de configuração de convidado são inclusivas de novas versões. As versões mais antigas dos sistemas operacionais disponíveis no Azure Marketplace serão excluídas se o agente de configuração do convidado não for compatível. A tabela a seguir mostra uma lista de sistemas operacionais com suporte em imagens do Azure:

|Editor|Name|Versões|
|-|-|-|
|Canônico|Ubuntu Server|14, 4 e posterior|
|Credativ|Debian|8 e posterior|
|Microsoft|Windows Server|2012 e posterior|
|Microsoft|Windows Client|Windows 10|
|OpenLogic|CentOS|7,3 e posterior|
|Red Hat|Red Hat Enterprise Linux|7,4 e posterior|
|Suse|SLES|12 SP3 e posterior|

As imagens de máquina virtual personalizada têm suporte pelas políticas de configuração de convidado, desde que sejam um dos sistemas operacionais na tabela acima.

### <a name="unsupported-client-types"></a>Tipos de clientes sem suporte

O Windows Server nano Server não tem suporte em nenhuma versão.

## <a name="guest-configuration-extension-network-requirements"></a>Requisitos de rede da extensão de configuração do convidado

Para se comunicar com o provedor de recursos de configuração de convidado no Azure, as máquinas exigem acesso de saída aos datacenters do Azure na porta **443**. Se uma rede no Azure não permitir tráfego de saída, configure exceções com regras de [grupo de segurança de rede](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) .
A [marca de serviço](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" pode ser usada para fazer referência ao serviço de configuração de convidado.

## <a name="azure-managed-identity-requirements"></a>Requisitos de identidade gerenciada do Azure

As políticas de **DeployIfNotExists** que adicionam a extensão às máquinas virtuais também habilitam uma identidade gerenciada atribuída pelo sistema, caso não exista uma.

> [!WARNING]
> Evite habilitar a identidade gerenciada atribuída pelo usuário a máquinas virtuais no escopo para políticas que habilitam a identidade gerenciada atribuída pelo sistema. A identidade atribuída ao usuário será substituída e poderá deixar de responder à máquina.

## <a name="guest-configuration-definition-requirements"></a>Requisitos de definição da Configuração de Convidado

Cada execução de auditoria pela configuração de convidado requer duas definições de política, uma definição de **DeployIfNotExists** e uma definição de **AuditIfNotExists** . 

A definição de política **DeployIfNotExists** valida e corrige os seguintes itens:

- Valide se uma configuração foi atribuída à máquina para avaliação. Se nenhuma atribuição estiver presente no momento, obtenha a atribuição e prepare a máquina da:
  - Autenticando no computador usando uma [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalando a versão mais recente da extensão **Microsoft.GuestConfiguration**
  - Instalando [ferramentas de validação](#validation-tools) e dependências se necessário

Se a atribuição de **DeployIfNotExists** não for compatível, uma [tarefa de correção](../how-to/remediate-resources.md#create-a-remediation-task) poderá ser usada.

Depois que a atribuição de **DeployIfNotExists** é compatível, a atribuição de política **AuditIfNotExists** determina se a atribuição de convidado é compatível ou não compatível. A ferramenta de validação fornece os resultados para o cliente de Configuração de Convidado. O cliente encaminha os resultados para a Extensão de Convidado, o que os disponibiliza por meio do provedor de recursos da Configuração de Convidado.

O Azure Policy usa a propriedade **complianceStatus** dos provedores de recursos da Configuração de Convidado para relatar a conformidade no nó **Conformidade**. Para obter mais informações, confira [Obtendo dados de conformidade](../how-to/get-compliance-data.md).

> [!NOTE]
> A política **DeployIfNotExists** é necessária para que a política **AuditIfNotExists** retorne os resultados. Sem o **DeployIfNotExists**, a política **AuditIfNotExists** mostra "0 de 0" recursos como status.

Todas as políticas internas da Configuração de Convidado são incluídas em uma iniciativa para agrupar as definições a serem usadas em atribuições. A iniciativa interna chamada _ \[Preview\]: auditoria de segurança de senha dentro de computadores Linux e Windows_ contém 18 políticas. Há seis pares de **DeployIfNotExists** e **AuditIfNotExists** para o Windows e três para o Linux. A lógica de [definição de política](definition-structure.md#policy-rule) valida que apenas o sistema operacional de destino é avaliado.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Auditando configurações do sistema operacional seguindo linhas de base do setor

Uma iniciativa no Azure Policy fornece a capacidade de auditar as configurações do sistema operacional após uma "linha de base". A definição, _ \[versão\]prévia: auditar VMs do Windows que não correspondem às configurações de linha de base de segurança do Azure_ inclui um conjunto de regras baseadas em Active Directory política de grupo.

A maioria das configurações está disponível como parâmetros. Os parâmetros permitem que você personalize o que é auditado. Alinhe a política com seus requisitos ou mapeie a política para informações de terceiros, como padrões regulatórios do setor.

Alguns parâmetros dão suporte a um intervalo de valores inteiros. Por exemplo, a configuração duração máxima da senha pode auditar a configuração de Política de Grupo efetiva. Um intervalo de "1, 70" confirmaria que os usuários precisam alterar suas senhas pelo menos a cada 70 dias, mas não menos de um dia.

Se você atribuir a política usando um modelo de implantação Azure Resource Manager, use um arquivo de parâmetros para gerenciar exceções. Faça check-in dos arquivos para um sistema de controle de versão, como git. Comentários sobre alterações de arquivo fornecem evidências por que uma atribuição é uma exceção para o valor esperado.

#### <a name="applying-configurations-using-guest-configuration"></a>Aplicando configurações usando a configuração de convidado

O recurso mais recente do Azure Policy define as configurações dentro de computadores. A definição _Configurar o fuso horário em computadores Windows_ faz alterações no computador Configurando o fuso horário.

Ao atribuir definições que começam com _Configure_, você também deve atribuir os pré-requisitos de implantação de definição _para habilitar a política de configuração de convidado em VMs do Windows_. Você pode combinar essas definições em uma iniciativa se escolher.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Atribuindo políticas a computadores fora do Azure

As políticas de auditoria disponíveis para a configuração de convidado incluem o tipo de recurso **Microsoft. HybridCompute/Machines** . Todos os computadores integrados ao [Azure ARC para servidores](../../../azure-arc/servers/overview.md) que estão no escopo da atribuição de política são incluídos automaticamente.

### <a name="multiple-assignments"></a>Várias atribuições

Atualmente, as políticas de configuração de convidado só dão suporte à atribuição da mesma entrada de convidado uma vez por computador, mesmo que a atribuição de política use parâmetros diferentes.

## <a name="client-log-files"></a>Arquivos de log do cliente

A extensão de configuração de convidado grava arquivos de log nos seguintes locais:

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Em `<version>` que refere-se ao número de versão atual.

### <a name="collecting-logs-remotely"></a>Coletando logs remotamente

A primeira etapa para solucionar problemas de configuração de convidado ou módulos deve ser usar `Test-GuestConfigurationPackage` o cmdlet seguindo as etapas como [criar uma política de auditoria de configuração de convidado personalizada para o Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Se isso não for bem-sucedido, coletar logs do cliente poderá ajudar a diagnosticar problemas.

#### <a name="windows"></a>Windows

Capturar informações de arquivos de log usando o [comando de execução de VM do Azure](../../../virtual-machines/windows/run-command.md), o exemplo de script do PowerShell a seguir pode ser útil.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Capturar informações de arquivos de log usando o [comando de execução de VM do Azure](../../../virtual-machines/linux/run-command.md), o exemplo de script de bash a seguir pode ser útil.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Exemplos de configuração de convidado

Os exemplos de política interna de configuração de convidado estão disponíveis nos seguintes locais:

- [Definições de política internas-configuração de convidado](../samples/built-in-policies.md#guest-configuration)
- [Iniciativas internas-configuração de convidado](../samples/built-in-initiatives.md#guest-configuration)
- [Repositório GitHub de exemplos de Azure Policy](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Próximas etapas

- Saiba como exibir os detalhes de cada configuração do [modo de exibição de conformidade de configuração de convidado](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](definition-structure.md).
- Revisar [Compreendendo os efeitos da política](effects.md).
- Entenda como [criar políticas programaticamente](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](../how-to/remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
