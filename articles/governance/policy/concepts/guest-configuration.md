---
title: Aprenda a auditar o conteúdo de máquinas virtuais
description: Saiba como a Diretiva Azure usa o agente de configuração de hóspedes para auditar as configurações dentro de máquinas virtuais.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 889e99e94b2c81a6654fcbe7851e93c40163a0c6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985313"
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

Para limitar a extensão de aplicativos que afetam o funcionamento da máquina, a Configuração do Convidado não pode exceder mais de 5% da utilização da CPU. Essa limitação existe para definições incorporadas e personalizadas.

## <a name="register-guest-configuration-resource-provider"></a>Registrar o provedor de recursos de Configuração de Convidado

Antes de usar a Configuração de Convidado, você precisa registrar o provedor de recursos. Registre-se por meio do portal ou do PowerShell. O provedor de recursos é registrado automaticamente se a atribuição de uma diretiva de configuração de hóspedes for feita através do portal.

### <a name="registration---portal"></a>Registro – Portal

Para registrar o provedor de recursos da Configuração de Convidado por meio do portal do Azure, siga estas etapas:

1. Inicie o portal do Azure e clique em **Todos os serviços**. Pesquise e selecione **Assinaturas**.

1. Localize e clique na assinatura para a qual você deseja habilitar a Configuração de Convidado.

1. No menu à esquerda da página **Assinatura**, clique em **Provedores de recursos**.

1. Filtre ou role até localizar **Microsoft.GuestConfiguration** e, em seguida, clique em **Registrar** na mesma linha.

### <a name="registration---powershell"></a>Registro – PowerShell

Para registrar o provedor de recursos da Configuração de Convidado por meio do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

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
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5, 7.6, 7.7|
|Suse|SLES|12 SP3|

### <a name="unsupported-client-types"></a>Tipos de clientes sem suporte

O Windows Server Nano Server não é suportado em nenhuma versão.

## <a name="guest-configuration-extension-network-requirements"></a>Requisitos de rede de extensão de configuração de hóspedes

Para se comunicar com o provedor de recursos de configuração de hóspedes no Azure, as máquinas exigem acesso de saída aos data centers do Azure na porta **443**. Se você estiver usando uma rede virtual privada no Azure que não permite tráfego de saída, configure exceções com as regras [do Network Security Group.](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)
A [tag de serviço](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" pode ser usada para referenciar o serviço de configuração de hóspedes.

## <a name="guest-configuration-definition-requirements"></a>Requisitos de definição da Configuração de Convidado

Cada auditoria executada pela Configuração do Convidado requer duas definições de diretiva, uma definição **DeployIfNotExist** e uma **definição AuditIfNotExist.** A definição **DeployIfNotExiste** é usada para preparar a máquina com o agente de configuração de hóspedes e outros componentes para suportar as [ferramentas de validação](#validation-tools).

A definição de política **DeployIfNotExists** valida e corrige os seguintes itens:

- Valide que a máquina foi designada para avaliar uma configuração. Se nenhuma atribuição estiver presente no momento, obtenha a atribuição e prepare a máquina por:
  - Autenticação para a máquina usando uma [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalando a versão mais recente da extensão **Microsoft.GuestConfiguration**
  - Instalando [ferramentas de validação](#validation-tools) e dependências se necessário

Se a atribuição **DeployIfNotExist** estiver incompatível, uma tarefa de [remediação](../how-to/remediate-resources.md#create-a-remediation-task) poderá ser usada.

Uma vez que a atribuição **DeployIfNotExist** esteja em conformidade, a atribuição de diretiva **AuditIfNotExist** usa as ferramentas de validação locais para determinar se a atribuição de configuração está em conformidade ou não. A ferramenta de validação fornece os resultados para o cliente de Configuração de Convidado. O cliente encaminha os resultados para a Extensão de Convidado, o que os disponibiliza por meio do provedor de recursos da Configuração de Convidado.

O Azure Policy usa a propriedade **complianceStatus** dos provedores de recursos da Configuração de Convidado para relatar a conformidade no nó **Conformidade**. Para obter mais informações, confira [Obtendo dados de conformidade](../how-to/get-compliance-data.md).

> [!NOTE]
> A diretiva **DeployIfNotExists** é necessária para que a diretiva **AuditIfNotExist** reevolva resultados. Sem o **DeployIfNotExists**, a diretiva **AuditIfNotExists** mostra os recursos "0 de 0" como status.

Todas as políticas internas da Configuração de Convidado são incluídas em uma iniciativa para agrupar as definições a serem usadas em atribuições. A iniciativa incorporada _ \[chamada\]Preview : Audit Password configurações de segurança dentro de máquinas Linux e Windows_ contém 18 políticas. Há seis pares de **DeployIfNotExists** e **AuditIfNotExists** para o Windows e três para o Linux. A lógica [de definição de política](definition-structure.md#policy-rule) valida que apenas o sistema operacional alvo é avaliado.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Auditoria das configurações do sistema operacional seguindo as linhas de base do setor

Uma das iniciativas disponíveis no Azure Policy fornece a capacidade de auditar as configurações do sistema operacional dentro de máquinas virtuais seguindo uma "linha de base" da Microsoft. A definição, _ \[\]Preview : Audit Windows VMs que não correspondem às configurações da linha de base de segurança do Azure_ inclui um conjunto completo de regras de auditoria com base nas configurações da Active Directory Group Policy.

A maioria das configurações estão disponíveis como parâmetros. Essa funcionalidade permite personalizar o que é auditado para alinhar a política com seus requisitos organizacionais ou para mapear a política para informações de terceiros, como padrões regulatórios do setor.

Alguns parâmetros suportam uma faixa de valor inteiro. Por exemplo, o parâmetro Idade máxima de senha pode ser definido usando um operador de intervalo para dar flexibilidade aos proprietários de máquinas. Você pode auditar que a configuração eficaz da Diretiva de Grupo que exige que os usuários alterem suas senhas não deve ser superior a 70 dias, mas não deve ser inferior a um dia. Conforme descrito na bolha de informações para o parâmetro, para fazer dessa política de negócios o valor efetivo da auditoria, definir o valor para "1,70".

Se você atribuir a diretiva usando um modelo de implantação do Azure Resource Manager, poderá usar um arquivo de parâmetros para gerenciar essas configurações a partir do controle de origem. Usar uma ferramenta como o Git para gerenciar alterações nas políticas de auditoria com comentários em cada documento de check-in prova por que uma atribuição deve ser uma exceção ao valor esperado.

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

Para usar o recurso Azure VM Run Command para capturar informações de arquivos de log em máquinas Windows, o exemplo a seguir do script PowerShell pode ser útil. Para obter mais informações, consulte [Executar scripts PowerShell em sua VM do Windows com comando Run](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Para usar o recurso Azure VM Run Command para capturar informações de arquivos log em máquinas Linux, o exemplo a seguir o script bash pode ser útil. Para obter mais informações, consulte [Executar scripts shell em sua VM Linux com comando Run](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Amostras de configuração do convidado

A fonte para as iniciativas de configuração de configuração de hóspedes de diretiva está disponível nos seguintes locais:

- [Definições de diretiva incorporada - Configuração do convidado](../samples/built-in-policies.md#guest-configuration)
- [Iniciativas incorporadas - Configuração do hóspede](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy amostra srepo gitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Próximas etapas

- Revisar exemplos em [amostras de política do Azure](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](definition-structure.md).
- Revisar [Compreendendo os efeitos da política](effects.md).
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Aprenda a [remediar recursos não compatíveis.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [organize seus recursos com grupos de gerenciamento do Azure.](../../management-groups/overview.md)
