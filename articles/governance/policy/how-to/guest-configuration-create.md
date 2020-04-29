---
title: Como criar políticas de configuração de convidado para o Windows
description: Saiba como criar uma política de configuração de convidado Azure Policy para o Windows.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: f09bb543f73e37bd211a55e2238808f57585bb18
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024890"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Como criar políticas de configuração de convidado para o Windows

Antes de criar políticas personalizadas, é uma boa ideia ler as informações de visão geral conceitual na página [Azure Policy configuração de convidado](../concepts/guest-configuration.md).
 
Para saber mais sobre como criar políticas de configuração de convidado para o Linux, consulte a página [como criar políticas de configuração de convidado para Linux](./guest-configuration-create-linux.md)

Ao auditar o Windows, a configuração de convidado usa um módulo de recurso de [configuração de estado desejado](/powershell/scripting/dsc/overview/overview) (DSC) para o arquivo de configuração. A configuração DSC define a condição em que o computador deve estar.
Se a avaliação da configuração falhar, o efeito de política **auditIfNotExists** será disparado e o computador será considerado **não compatível**.

[Azure Policy configuração de convidado](../concepts/guest-configuration.md) só pode ser usada para auditar configurações dentro de computadores. A correção de configurações dentro de computadores ainda não está disponível.

Use as ações a seguir para criar sua própria configuração para validar o estado de um computador Azure ou não Azure.

> [!IMPORTANT]
> As políticas personalizadas com a configuração de convidado são um recurso de visualização.
>
> A extensão de configuração de convidado é necessária para executar auditorias em máquinas virtuais do Azure.
> Para implantar a extensão em escala em todas as máquinas com Windows, atribua as seguintes definições de política:
>   - [Implantar os pré-requisitos para habilitar a Política de Configuração de Convidado nas VMs do Windows.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

Criar um artefato de configuração de convidado, teste automatizado do artefato, criação de uma definição de política e publicação da política, é totalmente autoautomatizado usando o módulo de configuração de convidado no PowerShell. O módulo pode ser instalado em um computador que executa o Windows, o macOS ou o Linux com o PowerShell 6,2 ou posterior em execução localmente, ou com [Azure cloud Shell](https://shell.azure.com)ou com a [imagem do docker de Azure PowerShell Core](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> A compilação de configurações ainda não tem suporte no Linux.

### <a name="base-requirements"></a>Requisitos base

Sistemas operacionais em que o módulo pode ser instalado:

- Linux
- macOS
- Windows

O módulo de recurso de configuração de convidado requer o seguinte software:

- PowerShell 6,2 ou posterior. Se ainda não estiver instalado, siga [estas instruções](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 ou superior. Se ainda não estiver instalado, siga [estas instruções](/powershell/azure/install-az-ps).
  - Somente os módulos AZ ' AZ. Accounts ' e ' AZ. Resources ' são necessários.

### <a name="install-the-module"></a>Instalar o módulo

Para instalar o módulo **GuestConfiguration** no PowerShell:

1. Em um prompt do PowerShell, execute o seguinte comando:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Valide se o módulo foi importado:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Artefatos de configuração de convidado e política para Windows

A configuração de convidado usa a configuração de estado desejado do PowerShell como uma abstração de linguagem para escrever o que auditar no Windows. O agente carrega uma instância autônoma do PowerShell 6,2, portanto, não há nenhum conflito com o uso da DSC do PowerShell no Windows PowerShell 5,1, e não há nenhum requisito para pré-instalar o PowerShell 6,2 ou posterior.

Para obter uma visão geral dos conceitos e da terminologia do DSC, consulte [visão geral do DSC do PowerShell](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Como os módulos de configuração do convidado diferem dos módulos DSC do Windows PowerShell

Quando a configuração de convidado audita uma máquina:

1. O agente é executado `Test-TargetResource` pela primeira vez para determinar se a configuração está no estado correto.
1. O valor booliano retornado pela função determina se o status de Azure Resource Manager para a atribuição de convidado deve ser compatível/não em conformidade.
1. O provedor é `Get-TargetResource` executado para retornar o estado atual de cada configuração, de modo que os detalhes estejam disponíveis sobre por que um computador não está em conformidade e para confirmar se o estado atual está em conformidade.

### <a name="get-targetresource-requirements"></a>Requisitos de Get-TargetResource

A função `Get-TargetResource` tem requisitos especiais para a configuração de convidado que não foram necessários para a configuração de estado desejado do Windows.

- A tabela de hash retornada deve incluir uma propriedade chamada **Reasons**.
- A propriedade Reasons deve ser uma matriz.
- Cada item na matriz deve ser uma tabela de hash com chaves denominadas **Code** e **frase**.

A propriedade Reasons é usada pelo serviço para padronizar a forma como as informações são apresentadas quando um computador está fora de conformidade. Você pode considerar cada item por motivos como um "motivo" pelo qual o recurso não está em conformidade. A propriedade é uma matriz porque um recurso pode estar fora de conformidade por mais de um motivo.

O **código** e a **frase** das propriedades são esperados pelo serviço. Ao criar um recurso personalizado, defina o texto (normalmente stdout) que você gostaria de mostrar como o motivo pelo qual o recurso não está em conformidade como o valor de **frase**. O **código** tem requisitos de formatação específicos para que os relatórios possam exibir claramente as informações sobre o recurso usado para fazer a auditoria. Essa solução torna a configuração de convidado extensível. Qualquer comando pode ser executado contanto que a saída possa ser retornada como um valor de cadeia de caracteres para a propriedade de **frase** .

- **Code** (String): o nome do recurso, repetido e, em seguida, um nome curto sem espaços como um identificador para o motivo. Esses três valores devem ser delimitados por dois-pontos sem espaços.
  - Um exemplo seria`registry:registry:keynotpresent`
- **Frase** (cadeia de caracteres): texto legível por humanos para explicar por que a configuração não está em conformidade.
  - Um exemplo seria`The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

A propriedade Reasons também deve ser adicionada ao Schema MOF para o recurso como uma classe incorporada.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>Requisitos de configuração

O nome da configuração personalizada deve ser consistente em todos os lugares. O nome do arquivo. zip para o pacote de conteúdo, o nome da configuração no arquivo MOF e o nome da atribuição de convidado no modelo do Resource Manager, deve ser o mesmo.

### <a name="scaffolding-a-guest-configuration-project"></a>Scaffolding um projeto de configuração de convidado

Os desenvolvedores que gostariam de acelerar o processo de introdução e trabalho a partir de um código de exemplo podem instalar um projeto de comunidade chamado **projeto de configuração de convidado**. O projeto instala um modelo para o módulo do PowerShell do [Plaster](https://github.com/powershell/plaster) . Essa ferramenta pode ser usada para Scaffold um projeto, incluindo uma configuração de trabalho e um recurso de exemplo, e um conjunto de testes de [Pester](https://github.com/pester/pester) para validar o projeto. O modelo também inclui executores de tarefas para Visual Studio Code para automatizar a criação e a validação do pacote de configuração do convidado. Para obter mais informações, consulte o [projeto de configuração de convidado](https://github.com/microsoft/guestconfigurationproject)do projeto do github.

Para obter mais informações sobre como trabalhar com configurações em geral, consulte [gravar, compilar e aplicar uma configuração](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Conteúdo esperado de um artefato de configuração de convidado

O pacote concluído é usado pela configuração de convidado para criar as definições de Azure Policy. O pacote consiste em:

- A configuração de DSC compilada como um MOF
- Pasta modules
  - Módulo GuestConfiguration
  - Módulo DscNativeResources
  - Windows Módulos de recursos de DSC exigidos pelo MOF

Os cmdlets do PowerShell auxiliam na criação do pacote.
Nenhuma pasta de nível de raiz ou de versão é necessária.
O formato do pacote deve ser um arquivo. zip.

### <a name="storing-guest-configuration-artifacts"></a>Armazenando artefatos de configuração de convidado

O pacote. zip deve ser armazenado em um local que possa ser acessado pelas máquinas virtuais gerenciadas.
Os exemplos incluem repositórios do GitHub, um repositório do Azure ou um armazenamento do Azure. Se preferir não tornar o pacote público, você pode incluir um [token SAS](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) na URL.
Você também pode implementar o [ponto de extremidade de serviço](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) para computadores em uma rede privada, embora essa configuração se aplique somente ao acesso ao pacote e não à comunicação com o serviço.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Passo a passo, criando uma política de auditoria de configuração de convidado personalizada para Windows

Crie uma configuração DSC para as configurações de auditoria. O exemplo de script do PowerShell a seguir cria uma configuração chamada **AuditBitLocker**, importa o módulo de recurso **PsDscResources** e usa o `Service` recurso para auditar um serviço em execução. O script de configuração pode ser executado em um computador Windows ou macOS.

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker ./Config
```

Salve esse arquivo com o `config.ps1` nome na pasta do projeto. Execute-o no PowerShell executando `./config.ps1` no terminal. Um novo arquivo MOF será criado.

O `Node AuditBitlocker` comando não é tecnicamente necessário, mas produz um arquivo `AuditBitlocker.mof` chamado em vez do padrão `localhost.mof`,. Ter o nome de arquivo. mof após a configuração facilita a organização de vários arquivos ao operar em escala.

Depois que o MOF é compilado, os arquivos de suporte devem ser empacotados juntos. O pacote concluído é usado pela configuração de convidado para criar as definições de Azure Policy.

O `New-GuestConfigurationPackage` cmdlet cria o pacote. Os módulos que são necessários para a configuração devem estar disponíveis em `$Env:PSModulePath`. Parâmetros do `New-GuestConfigurationPackage` cmdlet ao criar o conteúdo do Windows:

- **Nome**: nome do pacote de configuração do convidado.
- **Configuração**: caminho completo do documento de configuração DSC compilada.
- **Caminho**: caminho da pasta de saída. Esse parâmetro é opcional. Se não for especificado, o pacote será criado no diretório atual.

Execute o comando a seguir para criar um pacote usando a configuração fornecida na etapa anterior:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

Depois de criar o pacote de configuração, mas antes de publicá-lo no Azure, você pode testar o pacote de sua estação de trabalho ou ambiente de CI/CD. O cmdlet `Test-GuestConfigurationPackage` GuestConfiguration inclui o mesmo agente em seu ambiente de desenvolvimento, como é usado dentro de computadores do Azure. Usando essa solução, você pode fazer testes de integração localmente antes de liberar para ambientes de nuvem cobrados.

Como o agente está realmente avaliando o ambiente local, na maioria dos casos, você precisa executar o cmdlet Test-na mesma plataforma do sistema operacional que planeja auditar. O teste usará apenas módulos que estão incluídos no pacote de conteúdo.

Parâmetros do `Test-GuestConfigurationPackage` cmdlet:

- **Nome**: nome da política de configuração de convidado.
- **Parâmetro**: parâmetros de política fornecidos no formato de tabela de hash.
- **Caminho**: caminho completo do pacote de configuração do convidado.

Execute o seguinte comando para testar o pacote criado pela etapa anterior:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

O cmdlet também dá suporte à entrada do pipeline do PowerShell. Direcione a saída do `New-GuestConfigurationPackage` cmdlet para o `Test-GuestConfigurationPackage` cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

A próxima etapa é publicar o arquivo no armazenamento de BLOBs. O script a seguir contém uma função que você pode usar para automatizar essa tarefa. Os comandos usados na `publish` função exigem o `Az.Storage` módulo.

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
```

Depois que um pacote de política personalizada de configuração de convidado tiver sido criado e carregado, crie a definição de política de configuração de convidado. O `New-GuestConfigurationPolicy` cmdlet usa um pacote de política personalizado e cria uma definição de política.

Parâmetros do `New-GuestConfigurationPolicy` cmdlet:

- **ContentUri**: URI de http público (s) de pacote de conteúdo de configuração do convidado.
- **DisplayName**: nome de exibição da política.
- **Descrição**: Descrição da política.
- **Parâmetro**: parâmetros de política fornecidos no formato de tabela de hash.
- **Versão**: versão da política.
- **Caminho**: caminho de destino em que as definições de política são criadas.
- **Plataforma**: plataforma de destino (Windows/Linux) para a política de configuração de convidado e o pacote de conteúdo.

O exemplo a seguir cria as definições de política em um caminho especificado de um pacote de política personalizado:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

Os seguintes arquivos são criados por `New-GuestConfigurationPolicy`:

- **auditIfNotExists. JSON**
- **deployIfNotExists. JSON**
- **Initiative. JSON**

A saída do cmdlet retorna um objeto que contém o nome de exibição da iniciativa e o caminho dos arquivos de política.

Por fim, publique as definições de política `Publish-GuestConfigurationPolicy` usando o cmdlet. O cmdlet tem apenas o parâmetro **path** que aponta para o local dos arquivos JSON criados pelo `New-GuestConfigurationPolicy`.

Para executar o comando Publish, você precisa ter acesso para criar políticas no Azure. Os requisitos de autorização específicos estão documentados na página [visão geral do Azure Policy](../overview.md) . A melhor função interna é colaborador de **política de recursos**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

O `Publish-GuestConfigurationPolicy` cmdlet aceita o caminho do pipeline do PowerShell. Esse recurso significa que você pode criar os arquivos de política e publicá-los em um único conjunto de comandos de pipe.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Com a política criada no Azure, a última etapa é atribuir a iniciativa. Veja como atribuir a iniciativa com o [portal](../assign-policy-portal.md), [CLI do Azure](../assign-policy-azurecli.md)e [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> As políticas de configuração de convidado **sempre** devem ser atribuídas usando a iniciativa que combina as políticas _AuditIfNotExists_ e _DeployIfNotExists_ . Se apenas a política _AuditIfNotExists_ for atribuída, os pré-requisitos não serão implantados e a política sempre mostrará que os servidores ' 0 ' estão em conformidade.

A atribuição de uma definição de política com o efeito _DeployIfNotExists_ requer um nível adicional de acesso. Para conceder o privilégio mínimo, você pode criar uma definição de função personalizada que estenda o **colaborador de política de recursos**. O exemplo a seguir cria uma função chamada **colaborador de política de recursos restaurante** com a permissão adicional _Microsoft. Authorization/roleAssignments/Write_.

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Usando parâmetros em políticas de configuração de convidado personalizadas

A configuração de convidado dá suporte à substituição de propriedades de uma configuração em tempo de execução. Esse recurso significa que os valores no arquivo MOF no pacote não precisam ser considerados estáticos. Os valores de substituição são fornecidos por meio de Azure Policy e não afetam como as configurações são criadas ou compiladas.

Os cmdlets `New-GuestConfigurationPolicy` e `Test-GuestConfigurationPolicyPackage` incluem um parâmetro denominado **Parameters**. Esse parâmetro usa uma definição de Hashtable, incluindo todos os detalhes sobre cada parâmetro e cria as seções necessárias de cada arquivo usado para a definição de Azure Policy.

O exemplo a seguir cria uma definição de política para auditar um serviço, onde o usuário seleciona em uma lista no momento da atribuição de política.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="policy-lifecycle"></a>Ciclo de vida da política

Se você quiser liberar uma atualização para a política, há dois campos que exigem atenção.

- **Versão**: ao executar o `New-GuestConfigurationPolicy` cmdlet, você deve especificar um número de versão maior do que o publicado atualmente. A propriedade atualiza a versão da atribuição de configuração de convidado para que o agente reconheça o pacote atualizado.
- **contentHash**: essa propriedade é atualizada automaticamente pelo `New-GuestConfigurationPolicy` cmdlet. É um valor de hash do pacote criado pelo `New-GuestConfigurationPackage`. A propriedade deve estar correta para o `.zip` arquivo que você publicar. Se apenas a propriedade **contentUri** for atualizada, a extensão não aceitará o pacote de conteúdo.

A maneira mais fácil de liberar um pacote atualizado é repetir o processo descrito neste artigo e fornecer um número de versão atualizado. Esse processo garante que todas as propriedades tenham sido corretamente atualizadas.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Convertendo conteúdo do Windows Política de Grupo para Azure Policy configuração de convidado

A configuração de convidado, ao auditar computadores Windows, é uma implementação da sintaxe de configuração de estado desejado do PowerShell. A comunidade de DSC publicou ferramentas para converter modelos de Política de Grupo exportados para o formato DSC. Usando essa ferramenta junto com os cmdlets de configuração de convidado descritos acima, você pode converter o conteúdo do Windows Política de Grupo e o pacote/publicá-lo para Azure Policy para auditoria. Para obter detalhes sobre como usar a ferramenta, consulte o artigo [início rápido: converter política de grupo em DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).
Depois que o conteúdo tiver sido convertido, as etapas acima para criar um pacote e publicá-lo como Azure Policy são as mesmas para qualquer conteúdo DSC.

## <a name="optional-signing-guest-configuration-packages"></a>Opcional: assinando pacotes de configuração de convidado

As políticas personalizadas de configuração de convidado usam hash SHA256 para validar se o pacote de política não foi alterado.
Opcionalmente, os clientes também podem usar um certificado para assinar pacotes e forçar a extensão de configuração de convidado a permitir somente conteúdo assinado.

Para habilitar esse cenário, há duas etapas que precisam ser concluídas. Execute o cmdlet para assinar o pacote de conteúdo e acrescente uma marca para os computadores que devem exigir que o código seja assinado.

Para usar o recurso de validação de assinatura, `Protect-GuestConfigurationPackage` execute o cmdlet para assinar o pacote antes que ele seja publicado. Este cmdlet requer um certificado de ' assinatura de código '.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parâmetros do `Protect-GuestConfigurationPackage` cmdlet:

- **Caminho**: caminho completo do pacote de configuração do convidado.
- **Certificado**: certificado de assinatura de código para assinar o pacote. Esse parâmetro só tem suporte ao assinar conteúdo para o Windows.

O agente GuestConfiguration espera que a chave pública do certificado esteja presente em "autoridades de certificação raiz confiáveis" em computadores Windows e `/usr/local/share/ca-certificates/extra` no caminho em computadores Linux. Para o nó verificar o conteúdo assinado, instale a chave pública do certificado no computador antes de aplicar a política personalizada. Esse processo pode ser feito usando qualquer técnica dentro da VM ou usando Azure Policy. Um modelo de exemplo é [fornecido aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
A política de acesso de Key Vault deve permitir que o provedor de recursos de computação acesse certificados durante as implantações. Para obter etapas detalhadas, consulte [configurar Key Vault para máquinas virtuais no Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Veja a seguir um exemplo para exportar a chave pública de um certificado de autenticação, para importar para o computador.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Depois que o conteúdo for publicado, acrescente uma marca com `GuestConfigPolicyCertificateValidation` nome e `enabled` valor a todas as máquinas virtuais em que a assinatura de código deve ser necessária. Consulte os [exemplos de marca](../samples/built-in-policies.md#tags) de como as marcas podem ser entregues em escala usando Azure Policy. Depois que essa marca estiver em vigor, a definição de política gerada `New-GuestConfigurationPolicy` usando o cmdlet habilita o requisito por meio da extensão de configuração de convidado.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Solucionando problemas de atribuições de política de configuração de convidado (versão prévia)

Uma ferramenta está disponível na visualização para auxiliar na solução de problemas Azure Policy atribuições de configuração de convidado. A ferramenta está em versão prévia e foi publicada no Galeria do PowerShell como nome do módulo [solução de problemas de configuração de convidado](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Para obter mais informações sobre os cmdlets nesta ferramenta, use o comando Get-Help no PowerShell para mostrar as diretrizes internas. Como a ferramenta está obtendo atualizações frequentes, essa é a melhor maneira de obter as informações mais recentes.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as VMs de auditoria com a [configuração de convidado](../concepts/guest-configuration.md).
- Entenda como [criar políticas programaticamente](programmatically-create.md).
- Saiba como [obter dados de conformidade](get-compliance-data.md).
