---
title: Aprenda a auditar o conteúdo de máquinas virtuais
description: Saiba como Azure Policy usa o agente de configuração do convidado para auditar as configurações nas máquinas virtuais.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 128baa0df1d3e555fe9629005529710202e1bbf6
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169537"
---
# <a name="understand-azure-policys-guest-configuration"></a>Entender a Configuração de Convidado do Azure Policy

Além da auditoria e da [correção](../how-to/remediate-resources.md) dos recursos do Azure, Azure Policy pode auditar as configurações dentro de um computador. A validação é executada pela extensão e pelo cliente de Configuração de Convidado. A extensão, por meio do cliente, valida as configurações como:

- A configuração do sistema operacional
- Configuração ou presença do aplicativo
- Configurações do ambiente

Neste momento, a Configuração de Convidado do Azure Policy audita somente as configurações dentro do computador. Ela não aplica configurações.

## <a name="extension-and-client"></a>Extensão e cliente

Para auditar as configurações dentro de um computador, uma [extensão de máquina virtual](../../../virtual-machines/extensions/overview.md) está habilitada. A extensão baixa a atribuição de política aplicável e a definição de configuração correspondente.

### <a name="limits-set-on-the-extension"></a>Limites definidos na extensão

Para limitar a extensão de afetar os aplicativos em execução dentro do computador, a configuração de convidado não tem permissão para exceder mais de 5% da utilização da CPU. Essa limitação existe para as definições internas e personalizadas.

## <a name="register-guest-configuration-resource-provider"></a>Registrar o provedor de recursos de Configuração de Convidado

Antes de usar a Configuração de Convidado, você precisa registrar o provedor de recursos. Registre-se por meio do portal ou do PowerShell. O provedor de recursos será registrado automaticamente se a atribuição de uma política de configuração de convidado for feita por meio do Portal.

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

Dentro do computador, o cliente de configuração de convidado usa ferramentas locais para executar a auditoria.

A tabela a seguir mostra uma lista das ferramentas locais usadas em cada sistema operacional com suporte:

|Sistema operacional|Ferramenta de validação|Observações|
|-|-|-|
|Windows|[Configuração de estado desejado do Windows PowerShell](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| O Ruby e o Python são instalados pela extensão de Configuração de Convidado. |

### <a name="validation-frequency"></a>Frequência de validação

O cliente de Configuração Convidado verifica o novo conteúdo a cada 5 minutos. Depois que uma atribuição de convidado for recebida, as configurações serão verificadas em um intervalo de 15 minutos. Os resultados são enviados ao provedor de recursos de configuração do convidado assim que a auditoria é concluída. Quando ocorre um [gatilho de avaliação](../how-to/get-compliance-data.md#evaluation-triggers) de política, o estado do computador é gravado no provedor de recursos de Configuração do Convidado. Essa atualização faz com que Azure Policy avaliar as propriedades de Azure Resource Manager. Uma avaliação de Azure Policy sob demanda recupera o valor mais recente do provedor de recursos de configuração de convidado. No entanto, ele não aciona uma nova auditoria da configuração no computador.

## <a name="supported-client-types"></a>Tipos de clientes com suporte

A tabela a seguir mostra uma lista de sistemas operacionais com suporte em imagens do Azure:

|Publicador|Nome|Versões|
|-|-|-|
|Canônico|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 datacenter, 2012 R2 Datacenter, 2016 Data Center, 2019 datacenter|
|Microsoft|Windows Client|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> A configuração de convidado pode auditar OS nós que executam um sistema operacional com suporte. Se você quiser auditar as máquinas virtuais que usam uma imagem personalizada, será necessário duplicar a definição de **DeployIfNotExists** e modificar a seção **If** para incluir suas propriedades de imagem.

### <a name="unsupported-client-types"></a>Tipos de clientes sem suporte

O Windows Server nano Server não tem suporte em nenhuma versão.

## <a name="guest-configuration-extension-network-requirements"></a>Requisitos de rede da extensão de configuração do convidado

Para se comunicar com o provedor de recursos de configuração de convidado no Azure, as máquinas exigem acesso de saída aos datacenters do Azure na porta **443**. Se você estiver usando uma rede virtual privada no Azure que não permita o tráfego de saída, configure exceções com regras de [grupo de segurança de rede](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) .
A [marca de serviço](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" pode ser usada para fazer referência ao serviço de configuração de convidado.

## <a name="guest-configuration-definition-requirements"></a>Requisitos de definição da Configuração de Convidado

Cada execução de auditoria pela configuração de convidado requer duas definições de política, uma definição de **DeployIfNotExists** e uma definição de **AuditIfNotExists** . A definição de **DeployIfNotExists** é usada para preparar a máquina com o agente de configuração convidado e outros componentes para dar suporte às [ferramentas de validação](#validation-tools).

A definição de política **DeployIfNotExists** valida e corrige os seguintes itens:

- Valide se uma configuração foi atribuída à máquina para avaliação. Se nenhuma atribuição estiver presente no momento, obtenha a atribuição e prepare a máquina da:
  - Autenticando no computador usando uma [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalando a versão mais recente da extensão **Microsoft.GuestConfiguration**
  - Instalando [ferramentas de validação](#validation-tools) e dependências se necessário

Se a atribuição de **DeployIfNotExists** não for compatível, uma [tarefa de correção](../how-to/remediate-resources.md#create-a-remediation-task) poderá ser usada.

Depois que a atribuição de **DeployIfNotExists** é compatível, a atribuição de política **AuditIfNotExists** usa as ferramentas de validação locais para determinar se a atribuição de configuração é compatível ou não compatível. A ferramenta de validação fornece os resultados para o cliente de Configuração de Convidado. O cliente encaminha os resultados para a Extensão de Convidado, o que os disponibiliza por meio do provedor de recursos da Configuração de Convidado.

O Azure Policy usa a propriedade **complianceStatus** dos provedores de recursos da Configuração de Convidado para relatar a conformidade no nó **Conformidade**. Para obter mais informações, confira [Obtendo dados de conformidade](../how-to/get-compliance-data.md).

> [!NOTE]
> A política **DeployIfNotExists** é necessária para que a política **AuditIfNotExists** retorne os resultados. Sem o **DeployIfNotExists**, a política **AuditIfNotExists** mostra "0 de 0" recursos como status.

Todas as políticas internas da Configuração de Convidado são incluídas em uma iniciativa para agrupar as definições a serem usadas em atribuições. A iniciativa interna chamada _\[Preview\]: auditoria de configurações de segurança de senha dentro de computadores Linux e Windows_ contém 18 políticas. Há seis pares de **DeployIfNotExists** e **AuditIfNotExists** para o Windows e três para o Linux. A lógica de [definição de política](definition-structure.md#policy-rule) valida que apenas o sistema operacional de destino é avaliado.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Auditando configurações do sistema operacional seguindo linhas de base do setor

Uma das iniciativas disponíveis no Azure Policy fornece a capacidade de auditar configurações do sistema operacional dentro de máquinas virtuais após uma "linha de base" da Microsoft. A definição, _\[visualização\]: auditar VMs do Windows que não correspondem às configurações de linha de base de segurança do Azure_ inclui um conjunto completo de regras de auditoria com base nas configurações de Active Directory política de grupo.

A maioria das configurações está disponível como parâmetros. Essa funcionalidade permite que você personalize o que é auditado para alinhar a política com seus requisitos organizacionais ou mapear a política para informações de terceiros, como padrões regulatórios do setor.

Alguns parâmetros dão suporte a um intervalo de valores inteiros. Por exemplo, o parâmetro duração máxima da senha pode ser definido usando um operador de intervalo para dar flexibilidade aos proprietários do computador. Você pode auditar que a configuração de Política de Grupo efetiva que exige que os usuários alterem suas senhas não deve ter mais de 70 dias, mas não deve ser inferior a um dia. Conforme descrito na bolha de informações do parâmetro, para tornar essa política de negócios o valor de auditoria efetivo, defina o valor como "1, 70".

Se você atribuir a política usando um modelo de implantação Azure Resource Manager, poderá usar um arquivo de parâmetros para gerenciar essas configurações do controle do código-fonte. Usar uma ferramenta como o Git para gerenciar alterações em políticas de auditoria com comentários em cada verificação de documentos de check-in, por que uma atribuição deve ser uma exceção para o valor esperado.

#### <a name="applying-configurations-using-guest-configuration"></a>Aplicando configurações usando a configuração de convidado

O recurso mais recente do Azure Policy define as configurações dentro de computadores. A definição _Configurar o fuso horário em computadores Windows_ faz alterações no computador Configurando o fuso horário.

Ao atribuir definições que começam com _Configure_, você também deve atribuir os pré-requisitos de implantação de definição _para habilitar a política de configuração de convidado em VMs do Windows_. Você pode combinar essas definições em uma iniciativa se escolher.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Atribuindo políticas a computadores fora do Azure

As políticas de auditoria disponíveis para a configuração de convidado incluem o tipo de recurso **Microsoft. HybridCompute/Machines** . Todos os computadores integrados ao [Azure ARC para servidores](../../../azure-arc/servers/overview.md) que estão no escopo da atribuição de política são incluídos automaticamente.

### <a name="multiple-assignments"></a>Várias atribuições

Atualmente, as políticas de configuração de convidado só dão suporte à atribuição da mesma entrada de convidado uma vez por computador, mesmo que a atribuição de política use parâmetros diferentes.

## <a name="built-in-resource-modules"></a>Módulos de recursos internos

Ao instalar a extensão de configuração de convidado, o módulo do PowerShell ' GuestConfiguration ' é incluído com a versão mais recente dos módulos de recursos de DSC. Esse módulo pode ser baixado do Galeria do PowerShell usando o link ' download manual ' na página do módulo [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/). O formato de arquivo '. nupkg ' pode ser renomeado para '. zip ' para descompactar e examinar.

## <a name="client-log-files"></a>Arquivos de log do cliente

A extensão de configuração de convidado grava arquivos de log nos seguintes locais:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Onde `<version>` se refere ao número de versão atual.

### <a name="collecting-logs-remotely"></a>Coletando logs remotamente

A primeira etapa na solução de problemas de configurações de convidado ou módulos deve ser usar o cmdlet `Test-GuestConfigurationPackage` seguindo as etapas em [testar um pacote de configuração de convidado](../how-to/guest-configuration-create.md#test-a-guest-configuration-package).
Se isso não for bem-sucedido, coletar logs do cliente poderá ajudar a diagnosticar problemas.

#### <a name="windows"></a>Windows

Para usar a capacidade de comando de execução de VM do Azure para capturar informações de arquivos de log em computadores Windows, o exemplo de script do PowerShell a seguir pode ser útil. Para obter mais informações, consulte [executar scripts do PowerShell em sua VM do Windows com o comando executar](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Para usar a capacidade de comando de execução de VM do Azure para capturar informações de arquivos de log em computadores Linux, o exemplo de script de bash a seguir pode ser útil. Para obter mais informações, consulte [executar scripts de Shell em sua VM do Linux com o comando executar](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>Exemplos de configuração de convidado

A origem das iniciativas internas de configuração de convidado de política está disponível nos seguintes locais:

- [Iniciativas internas-configuração de convidado](../samples/built-in-initiatives.md#guest-configuration)
- [Repositório GitHub de exemplos de Azure Policy](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest Configuration)

## <a name="next-steps"></a>Próximas etapas

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](definition-structure.md).
- Revisar [Compreendendo os efeitos da política](effects.md).
- Entenda como [criar políticas programaticamente](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](../how-to/remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
