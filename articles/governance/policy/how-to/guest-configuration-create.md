---
title: Como criar políticas de configuração de hóspedes para Windows
description: Saiba como criar uma política de configuração de hóspedes de política do Azure para Windows.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 24069ff6518c4244026378e48216d4568fffeb8a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365478"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Como criar políticas de configuração de hóspedes para Windows

Antes de criar políticas personalizadas, é uma boa ideia ler as informações de visão geral conceituais na página [Configuração de hóspedes da política do Azure](../concepts/guest-configuration.md).
 
Para saber mais sobre a criação de políticas de configuração de hóspedes para Linux, consulte a página [Como criar políticas de configuração de hóspedes para Linux](./guest-configuration-create-linux.md)

Ao auditar o Windows, a Configuração do Convidado usa um módulo de recurso DSC [(Desired State Configuration, configuração](/powershell/scripting/dsc/overview/overview) do estado desejado) para e para o arquivo de configuração. A configuração do DSC define a condição em que a máquina deve estar.
Se a avaliação da configuração falhar, a auditoria de efeito de **políticaIfNotExists** será acionada e a máquina será considerada **incompatível**.

[A configuração do hóspede da política do Azure](../concepts/guest-configuration.md) só pode ser usada para auditar as configurações dentro das máquinas. A remediação das configurações dentro das máquinas ainda não está disponível.

Use as seguintes ações para criar sua própria configuração para validar o estado de uma máquina Azure ou não-Azure.

> [!IMPORTANT]
> Políticas personalizadas com configuração de hóspedes é um recurso de visualização.

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

Criar um artefato de configuração de hóspedes, testar o artefato automatizado, criar uma definição de política e publicar a política, é totalmente automatizado usando o módulo de configuração de hóspedes no PowerShell. O módulo pode ser instalado em uma máquina executando Windows, macOS ou Linux com o PowerShell 6.2 ou posteriorem-se executados localmente, ou com [o Azure Cloud Shell,](https://shell.azure.com)ou com a [imagem Do Azure PowerShell Core Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> A compilação de configurações ainda não é suportada no Linux.

### <a name="base-requirements"></a>Requisitos base

Sistemas operacionais onde o módulo pode ser instalado:

- Linux
- macOS
- Windows

O módulo de recurso Configuração do Convidado requer o seguinte software:

- PowerShell 6.2 ou posterior. Se ainda não estiver instalado, siga [estas instruções](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 ou superior. Se ainda não estiver instalado, siga [estas instruções](/powershell/azure/install-az-ps).
  - Apenas os módulos AZ 'Az.Accounts' e 'Az.Resources' são necessários.

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

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Artefatos de configuração de hóspedes e política para Windows

Configuração do convidado usa a configuração de estado desejado do PowerShell como uma abstração de idioma para escrever o que auditar no Windows. O agente carrega uma instância autônoma do PowerShell 6.2, portanto não há conflito com o uso do PowerShell DSC no Windows PowerShell 5.1, e não há necessidade de pré-instalar o PowerShell 6.2 ou posterior.

Para obter uma visão geral dos conceitos e terminologia do DSC, consulte [Visão geral do PowerShell DSC](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Como os módulos de configuração do convidado diferem dos módulos DSC do Windows PowerShell

Quando a configuração do convidado `Test-TargetResource` audita uma máquina, ela é executada primeiro para determinar se ela está no estado correto. O valor booleano retornado pela função determina se o status do Gerenciador de Recursos do Azure para a Atribuição de Hóspedes deve ser compatível/não compatível. Em seguida, `Get-TargetResource` o provedor executa para retornar o estado atual de cada configuração para que os detalhes estejam disponíveis tanto sobre por que uma máquina não está em conformidade, quanto para confirmar se o estado atual está em conformidade.

### <a name="get-targetresource-requirements"></a>Requisitos de recursos de destino de entrada

A `Get-TargetResource` função tem requisitos especiais para configuração de hóspedes que não foram necessários para a configuração do estado desejado do Windows.

- O hashtable que é devolvido deve incluir uma propriedade chamada **Razões**.
- A propriedade Reasons deve ser uma matriz.
- Cada item na matriz deve ser uma hashtable com teclas denominadas **Código** e **Frase**.

A propriedade Reasons é usada pelo serviço para padronizar como as informações são apresentadas quando uma máquina está fora de conformidade. Você pode pensar em cada item em Razões como uma "razão" de que o recurso não está em conformidade. A propriedade é uma matriz porque um recurso pode estar fora de conformidade por mais de uma razão.

As propriedades **Código** e **Frase** são esperadas pelo serviço. Ao criar um recurso personalizado, defina o texto (tipicamente stdout) que você gostaria de mostrar como a razão pela qual o recurso não está em conformidade como o valor para **Frase**. **O código** tem requisitos específicos de formatação para que os relatórios possam exibir claramente informações sobre o recurso usado para fazer a auditoria. Esta solução torna a configuração do hóspede extensível. Qualquer comando pode ser executado desde que a saída possa ser devolvida como um valor de string para a propriedade **Frase.**

- **Código** (string): O nome do recurso, repetido e, em seguida, um nome curto sem espaços como identificador para o motivo. Esses três valores devem ser delimitados por cólon sem espaços.
  - Um exemplo seria`registry:registry:keynotpresent`
- **Frase** (string): Texto legível por humanos para explicar por que a configuração não está em conformidade.
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
### <a name="configuration-requirements"></a>Requisitos de configuração

O nome da configuração personalizada deve ser consistente em todos os lugares. O nome do arquivo .zip para o pacote de conteúdo, o nome de configuração no arquivo MOF e o nome de atribuição de convidado no modelo Gerenciador de recursos, deve ser o mesmo.

### <a name="scaffolding-a-guest-configuration-project"></a>Andaimes de um projeto de configuração de hóspedes

Os desenvolvedores que gostariam de acelerar o processo de início e trabalho a partir do código de amostra podem instalar um projeto comunitário chamado **Guest Configuration Project**. O projeto instala um modelo para o módulo [Plaster](https://github.com/powershell/plaster) PowerShell. Esta ferramenta pode ser usada para andaime de um projeto, incluindo uma configuração de trabalho e recurso de amostra, e um conjunto de testes [pester](https://github.com/pester/pester) para validar o projeto. O modelo também inclui corredores de tarefas para o Visual Studio Code para automatizar a construção e validar o pacote de configuração do convidado. Para obter mais informações, consulte o projeto GitHub [Guest Configuration Project](https://github.com/microsoft/guestconfigurationproject).

Para obter mais informações sobre como trabalhar com configurações em geral, consulte [Gravar, Compilar e Aplicar uma configuração](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Conteúdo esperado de um artefato de configuração de hóspedes

O pacote concluído é usado pela Configuração do Convidado para criar as definições de Política do Azure. O pacote consiste em:

- A configuração dSC compilada como um MOF
- Pasta módulos
  - Módulo de configuração do hóspede
  - Módulo DscNativeResources
  - (Windows) Módulos de recursos DSC exigidos pelo MOF

Os cmdlets PowerShell auxiliam na criação do pacote.
Nenhuma pasta de nível raiz ou pasta de versão é necessária.
O formato do pacote deve ser um arquivo .zip.

### <a name="storing-guest-configuration-artifacts"></a>Armazenamento de artefatos de configuração de hóspedes

O pacote .zip deve ser armazenado em um local acessível pelas máquinas virtuais gerenciadas.
Exemplos incluem repositórios do GitHub, um Azure Repo ou armazenamento Azure. Se você preferir não tornar o pacote público, você pode incluir um [token SAS](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) na URL.
Você também pode implementar [o ponto final de serviço](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) para máquinas em uma rede privada, embora essa configuração se aplique apenas ao acesso ao pacote e não à comunicação com o serviço.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Passo a passo, criando uma política de auditoria de configuração de hóspedes personalizada para windows

Crie uma configuração DSC. O exemplo de script do PowerShell a seguir cria uma configuração chamada **AuditBitLocker,** importa o módulo de recursos **PsDscResources** e usa o `Service` recurso para auditar para um serviço em execução. O script de configuração pode ser executado a partir de uma máquina Windows ou macOS.

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
AuditBitLocker -out ./Config
```

O `Node AuditBitlocker` comando não é tecnicamente necessário, mas `AuditBitlocker.mof` produz um `localhost.mof`arquivo nomeado em vez do padrão, . Ter o nome do arquivo .mof seguir a configuração facilita a organização de muitos arquivos ao operar em escala.

Uma vez que o MOF é compilado, os arquivos de suporte devem ser embalados juntos. O pacote concluído é usado pela Configuração do Convidado para criar as definições de Política do Azure.

O `New-GuestConfigurationPackage` cmdlet cria o pacote. Parâmetros `New-GuestConfigurationPackage` do cmdlet ao criar conteúdo do Windows:

- **Nome**: Nome do pacote de configuração do convidado.
- **Configuração:** Compilou o caminho completo do documento de configuração do DSC.
- **Caminho**: Caminho da pasta de saída. Esse parâmetro é opcional. Se não for especificado, o pacote é criado no diretório atual.

Execute o seguinte comando para criar um pacote usando a configuração dada na etapa anterior:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

Depois de criar o pacote de configuração, mas antes de publicá-lo no Azure, você pode testar o pacote a partir de sua estação de trabalho ou ambiente CI/CD. O cmdlet `Test-GuestConfigurationPackage` GuestConfiguration inclui o mesmo agente em seu ambiente de desenvolvimento que é usado dentro de máquinas Azure. Usando essa solução, você pode fazer testes de integração localmente antes de liberar para ambientes em nuvem faturados.

Uma vez que o agente está realmente avaliando o ambiente local, na maioria dos casos você precisa executar o Test-cmdlet na mesma plataforma de SO que você planeja auditar.

Parâmetros `Test-GuestConfigurationPackage` do cmdlet:

- **Nome**: Nome da política de configuração do convidado.
- **Parâmetro**: Parâmetros de políticas fornecidos em formato hashtable.
- **Caminho**: Caminho completo do pacote de configuração do convidado.

Execute o seguinte comando para testar o pacote criado pela etapa anterior:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

O cmdlet também suporta a entrada do gasoduto PowerShell. Encane `New-GuestConfigurationPackage` a saída de `Test-GuestConfigurationPackage` cmdlet para o cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

O próximo passo é publicar o arquivo para o armazenamento blob. O script abaixo contém uma função que você pode usar para automatizar essa tarefa. Os comandos utilizados `publish` na `Az.Storage` função requerem o módulo.

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

Uma vez que um pacote de diretiva personalizada de configuração de hóspedes tenha sido criado e carregado, crie a definição de diretiva de configuração do convidado. O `New-GuestConfigurationPolicy` cmdlet pega um pacote de políticas personalizados e cria uma definição de política.

Parâmetros `New-GuestConfigurationPolicy` do cmdlet:

- **ContentUri**: Public http(s) uri do pacote de conteúdo de configuração de hóspedes.
- **Nome do display**: Nome de exibição da diretiva.
- **Descrição**: Descrição da política.
- **Parâmetro**: Parâmetros de políticas fornecidos em formato hashtable.
- **Versão**: Versão política.
- **Caminho**: Caminho de destino onde as definições de políticas são criadas.
- **Plataforma**: Plataforma alvo (Windows/Linux) para política de configuração de hóspedes e pacote de conteúdo.

O exemplo a seguir cria as definições de diretiva em um caminho especificado a partir de um pacote de diretiva personalizado:

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

Os seguintes arquivos `New-GuestConfigurationPolicy`são criados por:

- **auditIfNotExists.json**
- **implantarIfNotExists.json**
- **Iniciativa.json**

A saída cmdlet retorna um objeto contendo o nome de exibição da iniciativa e o caminho dos arquivos de diretiva.

Por fim, publique as `Publish-GuestConfigurationPolicy` definições de política usando o cmdlet. O cmdlet só tem o parâmetro **Caminho** que aponta para a `New-GuestConfigurationPolicy`localização dos arquivos JSON criados por .

Para executar o comando Publicar, você precisa acessar para criar políticas no Azure. Os requisitos específicos de autorização estão documentados na página Visão Geral da Política do [Azure.](../overview.md) O melhor papel incorporado é o **Resource Policy Contributor**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

O `Publish-GuestConfigurationPolicy` cmdlet aceita o caminho do gasoduto PowerShell. Esse recurso significa que você pode criar os arquivos de diretiva e publicá-los em um único conjunto de comandos encanados.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Com a política criada no Azure, o último passo é atribuir a iniciativa. Veja como atribuir a iniciativa com [portal,](../assign-policy-portal.md) [Azure CLI](../assign-policy-azurecli.md)e [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> As políticas de configuração de hóspedes devem **ser sempre** atribuídas usando a iniciativa que combina as políticas _AuditIfNotExist_ e _DeployIfNotExist._ Se apenas a diretiva _AuditIfNotExist_ for atribuída, os pré-requisitos não serão implantados e a diretiva sempre mostrará que os servidores '0' estão em conformidade.

Atribuir uma definição de diretiva com efeito _DeployIfNotExiste_ requer um nível adicional de acesso. Para conceder o menor privilégio, você pode criar uma definição de função personalizada que amplia **o Contribuinte de Políticas de Recursos**. O exemplo abaixo cria uma função chamada **Resource Policy Contributor DINE** com a permissão adicional _Microsoft.Authorization/roleAssignments/write_.

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

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Usando parâmetros em políticas personalizadas de configuração de hóspedes

A configuração do hóspede suporta propriedades de substituição de uma configuração em tempo de execução. Esse recurso significa que os valores no arquivo MOF no pacote não devem ser considerados estáticos. Os valores de substituição são fornecidos através da Política Do Azure e não afetam a forma como as configurações são de autoria ou compiladas.

Os `New-GuestConfigurationPolicy` cmdlets `Test-GuestConfigurationPolicyPackage` e incluem um parâmetro chamado **Parâmetros**. Este parâmetro toma uma definição hashtable, incluindo todos os detalhes sobre cada parâmetro e cria as seções necessárias de cada arquivo usado para a definição de Política do Azure.

O exemplo a seguir cria uma definição de diretiva para auditar um serviço, onde o usuário seleciona a partir de uma lista no momento da atribuição da diretiva.

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

Se você quiser liberar uma atualização para a política, existem dois campos que requerem atenção.

- **Versão:** Quando você `New-GuestConfigurationPolicy` executa o cmdlet, você deve especificar um número de versão maior do que o que está publicado atualmente. A propriedade atualiza a versão da atribuição Configuração do Convidado para que o agente reconheça o pacote atualizado.
- **conteúdoHash**: Esta propriedade é `New-GuestConfigurationPolicy` atualizada automaticamente pelo cmdlet. É um valor de hash do `New-GuestConfigurationPackage`pacote criado por . A propriedade deve estar `.zip` correta para o arquivo que você publica. Se apenas a propriedade **contentUri** for atualizada, a Extensão não aceitará o pacote de conteúdo.

A maneira mais fácil de liberar um pacote atualizado é repetir o processo descrito neste artigo e fornecer um número de versão atualizado. Esse processo garante que todas as propriedades tenham sido corretamente atualizadas.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Convertendo o conteúdo da diretiva do Grupo Windows para a configuração do convidado da política do Azure

Configuração do hóspede, ao auditar máquinas Windows, é uma implementação da sintaxe de configuração do estado desejado powershell. A comunidade DSC publicou ferramentas para converter modelos de política de grupo exportados para o formato DSC. Ao usar esta ferramenta juntamente com os cmdlets de configuração de convidados descritos acima, você pode converter o conteúdo da Diretiva do Grupo Windows e empacotá-lo/publicá-lo para a política do Azure para auditoria. Para obter detalhes sobre o uso da ferramenta, consulte o artigo [Quickstart: Converta a Política de Grupo em DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).
Uma vez que o conteúdo tenha sido convertido, as etapas acima para criar um pacote e publicá-lo como Política Azure são as mesmas de qualquer conteúdo DSC.

## <a name="optional-signing-guest-configuration-packages"></a>Opcional: Assinatura de pacotes de configuração de hóspedes

As políticas personalizadas de configuração do convidado usam hash SHA256 para validar o pacote de políticas não foi alterado.
Opcionalmente, os clientes também podem usar um certificado para assinar pacotes e forçar a extensão de Configuração do Convidado a permitir apenas conteúdo assinado.

Para habilitar este cenário, há dois passos que você precisa completar. Execute o cmdlet para assinar o pacote de conteúdo e aempenhe uma tag às máquinas que devem exigir que o código seja assinado.

Para usar o recurso Validação `Protect-GuestConfigurationPackage` de assinatura, execute o cmdlet para assinar o pacote antes de ser publicado. Este cmdlet requer um certificado 'Assinatura de Código'.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parâmetros `Protect-GuestConfigurationPackage` do cmdlet:

- **Caminho**: Caminho completo do pacote de configuração do convidado.
- **Certificado**: Certificado de assinatura de código para assinar o pacote. Este parâmetro só é suportado ao assinar conteúdo para Windows.

O agente GuestConfiguration espera que a chave pública do certificado esteja presente em `/usr/local/share/ca-certificates/extra` "Autoridades de certificado raiz confiáveis" em máquinas Windows e no caminho em máquinas Linux. Para que o nó verifique o conteúdo assinado, instale a chave pública do certificado na máquina antes de aplicar a política personalizada. Esse processo pode ser feito usando qualquer técnica dentro da VM ou usando a Política do Azure. Um modelo de exemplo é [fornecido aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
A política de acesso do Key Vault deve permitir que o provedor de recursos Compute acesse certificados durante as implantações. Para obter etapas detalhadas, consulte [Configurar o Key Vault para máquinas virtuais no Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

A seguir, um exemplo para exportar a chave pública a partir de um certificado de assinatura, para importar para a máquina.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Depois que seu conteúdo for publicado, `GuestConfigPolicyCertificateValidation` anexar `enabled` uma tag com nome e valor a todas as máquinas virtuais onde a assinatura de código deve ser necessária. Consulte as [amostras de tag](../samples/built-in-policies.md#tags) para saber como as tags podem ser entregues em escala usando a Política do Azure. Uma vez que esta tag esteja em `New-GuestConfigurationPolicy` vigor, a definição de diretiva gerada usando o cmdlet permite a exigência através da extensão Configuração do Convidado.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Solução de problemas De configuração de configuração de hóspedes (Visualização)

Uma ferramenta está disponível na pré-visualização para ajudar na solução de problemas adepções de configuração de hóspedes da política do Azure. A ferramenta está em pré-visualização e foi publicada na PowerShell Gallery como nome do módulo [Guest Configuration Troubleshooter](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Para obter mais informações sobre os cmdlets nesta ferramenta, use o comando Get-Help no PowerShell para mostrar a orientação incorporada. Como a ferramenta está recebendo atualizações frequentes, essa é a melhor maneira de obter informações mais recentes.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a auditoria de VMs com [configuração de hóspedes](../concepts/guest-configuration.md).
- Entenda como [criar políticas programáticas.](programmatically-create.md)
- Saiba como [obter dados de conformidade](get-compliance-data.md).
