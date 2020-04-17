---
title: Aprenda a auditar o conteúdo de máquinas virtuais
description: Saiba como a Diretiva Azure usa o agente de configuração de hóspedes para auditar as configurações dentro de máquinas virtuais.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: e4899f6b3108cabb4e9cdd36e4b2bc5cd2f1cbd4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538028"
---
# <a name="understand-azure-policys-guest-configuration"></a>Entender a Configuração de Convidado do Azure Policy

Além de auditar e [remediar](../how-to/remediate-resources.md) os recursos do Azure, a Azure Policy pode auditar as configurações dentro de uma máquina. A validação é executada pela extensão e pelo cliente de Configuração de Convidado. A extensão, por meio do cliente, valida as configurações como:

- A configuração do sistema operacional
- Configuração ou presença do aplicativo
- Configurações do ambiente

Neste momento, a maioria das políticas de configuração de hóspedes da diretiva do Azure apenas auditam as configurações dentro da máquina. Eles não aplicam configurações. A exceção é uma política incorporada [referenciada abaixo](#applying-configurations-using-guest-configuration).

## <a name="extension-and-client"></a>Extensão e cliente

Para auditar as configurações dentro de uma máquina, uma [extensão de máquina virtual](../../../virtual-machines/extensions/overview.md) é habilitada. A extensão baixa a atribuição de política aplicável e a definição de configuração correspondente.

### <a name="limits-set-on-the-extension"></a>Limites definidos na extensão

Para limitar a extensão de aplicativos que afetam o funcionamento dentro da máquina, a Configuração do Convidado não pode exceder mais de 5% da CPU. Essa limitação existe para definições incorporadas e personalizadas.

## <a name="register-guest-configuration-resource-provider"></a>Registrar o provedor de recursos de Configuração de Convidado

Antes de usar a Configuração de Convidado, você precisa registrar o provedor de recursos. Você pode se inscrever através do [portal](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) [, Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)ou [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli). O provedor de recursos é registrado automaticamente se a atribuição de uma diretiva de configuração de hóspedes for feita através do portal.

## <a name="validation-tools"></a>Ferramentas de validação

Dentro da máquina, o cliente Configuração do Convidado usa ferramentas locais para executar a auditoria.

A tabela a seguir mostra uma lista das ferramentas locais usadas em cada sistema operacional com suporte:

|Sistema operacional|Ferramenta de validação|Observações|
|-|-|-|
|Windows|[Configuração do estado desejado do Windows PowerShell](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Se Ruby e Python não estiverem na máquina, eles serão instalados pela extensão Configuração do Convidado. |

### <a name="validation-frequency"></a>Frequência de validação

O cliente de Configuração Convidado verifica o novo conteúdo a cada 5 minutos. Uma vez que uma atribuição de hóspedes é recebida, as configurações dessa configuração são remarcadas em um intervalo de 15 minutos.
Os resultados são enviados ao provedor de recursos de configuração de hóspedes quando a auditoria for concluída. Quando ocorre um [gatilho de avaliação](../how-to/get-compliance-data.md#evaluation-triggers) de política, o estado do computador é gravado no provedor de recursos de Configuração do Convidado. Essa atualização faz com que a Política do Azure avalie as propriedades do Azure Resource Manager. Uma avaliação sob demanda da Azure Policy recupera o valor mais recente do provedor de recursos De configuração de hóspedes. No entanto, ele não aciona uma nova auditoria da configuração dentro da máquina.

## <a name="supported-client-types"></a>Tipos de clientes com suporte

A tabela a seguir mostra uma lista de sistemas operacionais com suporte em imagens do Azure:

|Publicador|Nome|Versões|
|-|-|-|
|Canônico|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Windows Client|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5, 7.6, 7.7|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5, 7.6, 7.7, 7.8|
|Suse|SLES|12 SP3|

### <a name="unsupported-client-types"></a>Tipos de clientes sem suporte

O Windows Server Nano Server não é suportado em nenhuma versão.

## <a name="guest-configuration-extension-network-requirements"></a>Requisitos de rede de extensão de configuração de hóspedes

Para se comunicar com o provedor de recursos de configuração de hóspedes no Azure, as máquinas exigem acesso de saída aos data centers do Azure na porta **443**. Se uma rede no Azure não permitir tráfego de saída, configure exceções com as regras [do Network Security Group.](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)
A [tag de serviço](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" pode ser usada para referenciar o serviço de configuração de hóspedes.

## <a name="azure-managed-identity-requirements"></a>Requisitos de identidade gerenciados pelo Azure

As políticas **DeployIfNotExist** que adicionam a extensão a máquinas virtuais também permitem uma identidade gerenciada atribuída ao sistema, se uma não existir.

> [!WARNING]
> Evite habilitar a identidade gerenciada atribuída ao usuário para máquinas virtuais no âmbito de políticas que permitam a identidade gerenciada atribuída ao sistema. A identidade atribuída ao usuário será substituída e a máquina poderá ficar sem resposta.

## <a name="guest-configuration-definition-requirements"></a>Requisitos de definição da Configuração de Convidado

Cada auditoria executada pela Configuração do Convidado requer duas definições de diretiva, uma definição **DeployIfNotExist** e uma **definição AuditIfNotExist.** 

A definição de política **DeployIfNotExists** valida e corrige os seguintes itens:

- Valide que a máquina foi designada para avaliar uma configuração. Se nenhuma atribuição estiver presente no momento, obtenha a atribuição e prepare a máquina por:
  - Autenticação para a máquina usando uma [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalando a versão mais recente da extensão **Microsoft.GuestConfiguration**
  - Instalando [ferramentas de validação](#validation-tools) e dependências se necessário

Se a atribuição **DeployIfNotExist** estiver incompatível, uma tarefa de [remediação](../how-to/remediate-resources.md#create-a-remediation-task) poderá ser usada.

Uma vez que a atribuição **DeployIfNotExist** esteja em conformidade, a atribuição de diretiva **AuditIfNotExist** determina se a atribuição de hóspedes está em conformidade ou não. A ferramenta de validação fornece os resultados para o cliente de Configuração de Convidado. O cliente encaminha os resultados para a Extensão de Convidado, o que os disponibiliza por meio do provedor de recursos da Configuração de Convidado.

O Azure Policy usa a propriedade **complianceStatus** dos provedores de recursos da Configuração de Convidado para relatar a conformidade no nó **Conformidade**. Para obter mais informações, confira [Obtendo dados de conformidade](../how-to/get-compliance-data.md).

> [!NOTE]
> A diretiva **DeployIfNotExists** é necessária para que a diretiva **AuditIfNotExist** reevolva resultados. Sem o **DeployIfNotExists**, a diretiva **AuditIfNotExists** mostra os recursos "0 de 0" como status.

Todas as políticas internas da Configuração de Convidado são incluídas em uma iniciativa para agrupar as definições a serem usadas em atribuições. A iniciativa incorporada _ \[chamada\]Preview : Audit Password security dentro de máquinas Linux e Windows_ contém 18 políticas. Há seis pares de **DeployIfNotExists** e **AuditIfNotExists** para o Windows e três para o Linux. A lógica [de definição de política](definition-structure.md#policy-rule) valida que apenas o sistema operacional alvo é avaliado.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Auditoria das configurações do sistema operacional seguindo as linhas de base do setor

Uma iniciativa da Azure Policy fornece a capacidade de auditar as configurações do sistema operacional após uma "linha de base". A definição, _ \[Preview\]: Audit Windows VMs que não correspondem às configurações da linha de base de segurança do Azure_ inclui um conjunto de regras com base na Política do Grupo de Diretório Ativo.

A maioria das configurações estão disponíveis como parâmetros. Os parâmetros permitem personalizar o que é auditado. Alinhe a política com seus requisitos ou mapeie a política para informações de terceiros, como padrões regulatórios do setor.

Alguns parâmetros suportam uma faixa de valor inteiro. Por exemplo, a configuração Idade máxima de senha pode auditar a configuração de diretiva de grupo eficaz. Uma faixa "1,70" confirmaria que os usuários são obrigados a alterar suas senhas pelo menos a cada 70 dias, mas não menos que um dia.

Se você atribuir a diretiva usando um modelo de implantação do Azure Resource Manager, use um arquivo de parâmetros para gerenciar exceções. Verifique os arquivos em um sistema de controle de versão como o Git. Comentários sobre alterações de arquivos fornecem evidências de que uma atribuição é uma exceção ao valor esperado.

#### <a name="applying-configurations-using-guest-configuration"></a>Aplicando configurações usando a configuração do convidado

O recurso mais recente da Diretiva Azure configura configurações dentro de máquinas. A definição _Configure o fuso horário nas máquinas Windows_ faz alterações na máquina configurando o fuso horário.

Ao atribuir definições que começam com _Configurar,_ você também deve atribuir a definição _Implantar pré-requisitos para ativar a Política de Configuração de Hóspedes em VMs do Windows_. Você pode combinar essas definições em uma iniciativa, se você escolher.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Atribuindo políticas a máquinas fora do Azure

As políticas de auditoria disponíveis para configuração de hóspedes incluem o tipo de recurso **Microsoft.HybridCompute/machines.** Todas as máquinas a bordo [do Azure Arc para servidores](../../../azure-arc/servers/overview.md) que estejam no escopo da atribuição de políticas são automaticamente incluídas.

### <a name="multiple-assignments"></a>Múltiplas atribuições

Atualmente, as políticas de configuração de hóspedes só suportam a atribuição da mesma atribuição de hóspedes uma vez por máquina, mesmo que a atribuição de diretiva use parâmetros diferentes.

## <a name="client-log-files"></a>Arquivos de log do cliente

A extensão Configuração do Convidado grava arquivos de log para os seguintes locais:

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Quando `<version>` se refere ao número da versão atual.

### <a name="collecting-logs-remotely"></a>Coleta ndo registros remotamente

O primeiro passo para solucionar as configurações `Test-GuestConfigurationPackage` ou módulos de configuração do hóspede deve ser usar o cmdlet seguindo as etapas de como [criar uma política de auditoria de configuração de hóspedes personalizada para windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Se isso não for bem sucedido, coletar registros de clientes pode ajudar a diagnosticar problemas.

#### <a name="windows"></a>Windows

Capturar informações de arquivos de log usando [o Azure VM Run Command](../../../virtual-machines/windows/run-command.md), o exemplo a seguir, o script PowerShell pode ser útil.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Capturar informações de arquivos de log usando [o Azure VM Run Command](../../../virtual-machines/linux/run-command.md), o exemplo a seguir O script do Bash pode ser útil.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Amostras de configuração do convidado

As amostras de diretiva incorporada de configuração de hóspedes estão disponíveis nos seguintes locais:

- [Definições de diretiva incorporada - Configuração do convidado](../samples/built-in-policies.md#guest-configuration)
- [Iniciativas incorporadas - Configuração do hóspede](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy amostra srepo gitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Próximas etapas

- Saiba como visualizar os detalhes de cada configuração a partir da exibição de [conformidade de configuração do convidado](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Revisar exemplos em [amostras de política do Azure](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](definition-structure.md).
- Revisar [Compreendendo os efeitos da política](effects.md).
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Aprenda a [remediar recursos não compatíveis.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [organize seus recursos com grupos de gerenciamento do Azure.](../../management-groups/overview.md)
