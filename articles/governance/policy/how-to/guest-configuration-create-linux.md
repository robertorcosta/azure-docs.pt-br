---
title: Como criar políticas de configuração de convidado para Linux
description: Saiba como criar uma política de configuração de convidado Azure Policy para Linux.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 219b38bd81cae8d16241d1ee16cfdd2f400ae91e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024975"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Como criar políticas de configuração de convidado para Linux

Antes de criar políticas personalizadas, leia as informações de visão geral em [Azure Policy configuração de convidado](../concepts/guest-configuration.md).
 
Para saber mais sobre como criar políticas de configuração de convidado para o Windows, consulte a página [como criar políticas de configuração de convidado para o Windows](./guest-configuration-create.md)

Ao auditar o Linux, a configuração de convidado usa o [chefe inspec](https://www.inspec.io/). O perfil inspec define a condição em que o computador deve estar. Se a avaliação da configuração falhar, o efeito de política **auditIfNotExists** será disparado e o computador será considerado **não compatível**.

[Azure Policy configuração de convidado](../concepts/guest-configuration.md) só pode ser usada para auditar configurações dentro de computadores. A correção de configurações dentro de computadores ainda não está disponível.

Use as ações a seguir para criar sua própria configuração para validar o estado de um computador Azure ou não Azure.

> [!IMPORTANT]
> As políticas personalizadas com a configuração de convidado são um recurso de visualização.
>
> A extensão de configuração de convidado é necessária para executar auditorias em máquinas virtuais do Azure.
> Para implantar a extensão em escala em todas as máquinas Linux, atribua a seguinte definição de política:
>   - [Implantar os pré-requisitos para habilitar a Política de Configuração de Convidado nas VMs do Linux.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

Criar um artefato de configuração de convidado, teste automatizado do artefato, criação de uma definição de política e publicação da política, é totalmente autoautomatizado usando o módulo de configuração de convidado no PowerShell. O módulo pode ser instalado em um computador que executa o Windows, o macOS ou o Linux com o PowerShell 6,2 ou posterior em execução localmente, ou com [Azure cloud Shell](https://shell.azure.com)ou com a [imagem do docker de Azure PowerShell Core](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Não há suporte para a compilação de configurações no Linux.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Artefatos de configuração de convidado e política para Linux

Mesmo em ambientes Linux, a configuração de convidado usa a configuração de estado desejado como uma abstração de linguagem. A implementação é baseada em código nativo (C++) para que não exija o carregamento do PowerShell. No entanto, ele requer um MOF de configuração que descreva os detalhes sobre o ambiente. O DSC está agindo como um wrapper para inspec padronizar como ele é executado, como os parâmetros são fornecidos e como a saída é retornada ao serviço. É necessário um pouco de conhecimento do DSC ao trabalhar com conteúdo de inspec personalizado.

#### <a name="configuration-requirements"></a>Requisitos de configuração

O nome da configuração personalizada deve ser consistente em todos os lugares. O nome do arquivo. zip para o pacote de conteúdo, o nome da configuração no arquivo MOF e o nome da atribuição de convidado no modelo do Resource Manager, deve ser o mesmo.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Configuração de convidado personalizada no Linux

A configuração de convidado no Linux `ChefInSpecResource` usa o recurso para fornecer ao mecanismo o nome do [perfil inspec](https://www.inspec.io/docs/reference/profiles/). **Nome** é a única propriedade de recurso necessária. Crie um arquivo YaML e um arquivo de script Ruby, conforme detalhado abaixo.

Primeiro, crie o arquivo YaML usado pelo inspec. O arquivo fornece informações básicas sobre o ambiente. Um exemplo é fornecido abaixo:

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

Salve esse arquivo com o `inspec.yml` nome em uma pasta `linux-path` chamada no diretório do projeto.

Em seguida, crie o arquivo Ruby com a abstração de linguagem inspec usada para auditar o computador.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Salve esse arquivo com o `linux-path.rb` nome em uma nova pasta `controls` chamada dentro `linux-path` do diretório.

Por fim, crie uma configuração, importe o módulo de recurso **PSDesiredStateConfiguration** e Compile a configuração.

```powershell
# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
import-module PSDesiredStateConfiguration
AuditFilePathExists -out ./Config
```

Salve esse arquivo com o `config.ps1` nome na pasta do projeto. Execute-o no PowerShell executando `./config.ps1` no terminal. Um novo arquivo MOF será criado.

O `Node AuditFilePathExists` comando não é tecnicamente necessário, mas produz um arquivo `AuditFilePathExists.mof` chamado em vez do padrão `localhost.mof`,. Ter o nome de arquivo. mof após a configuração facilita a organização de vários arquivos ao operar em escala.



Agora você deve ter uma estrutura de projeto como a seguir:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

Os arquivos de suporte devem ser empacotados juntos. O pacote concluído é usado pela configuração de convidado para criar as definições de Azure Policy.

O `New-GuestConfigurationPackage` cmdlet cria o pacote. Parâmetros do `New-GuestConfigurationPackage` cmdlet ao criar conteúdo do Linux:

- **Nome**: nome do pacote de configuração do convidado.
- **Configuração**: caminho completo do documento de configuração compilada.
- **Caminho**: caminho da pasta de saída. Esse parâmetro é opcional. Se não for especificado, o pacote será criado no diretório atual.
- **ChefProfilePath**: caminho completo para o perfil inspec. Esse parâmetro tem suporte apenas ao criar conteúdo para auditar o Linux.

Execute o comando a seguir para criar um pacote usando a configuração fornecida na etapa anterior:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

Depois de criar o pacote de configuração, mas antes de publicá-lo no Azure, você pode testar o pacote de sua estação de trabalho ou ambiente de CI/CD. O cmdlet `Test-GuestConfigurationPackage` GuestConfiguration inclui o mesmo agente em seu ambiente de desenvolvimento, como é usado dentro de computadores do Azure. Usando essa solução, você pode executar testes de integração localmente antes de liberar para ambientes de nuvem cobrados.

Como o agente está realmente avaliando o ambiente local, na maioria dos casos, você precisa executar o cmdlet Test-na mesma plataforma do sistema operacional que planeja auditar.

Parâmetros do `Test-GuestConfigurationPackage` cmdlet:

- **Nome**: nome da política de configuração de convidado.
- **Parâmetro**: parâmetros de política fornecidos no formato de tabela de hash.
- **Caminho**: caminho completo do pacote de configuração do convidado.

Execute o seguinte comando para testar o pacote criado pela etapa anterior:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

O cmdlet também dá suporte à entrada do pipeline do PowerShell. Direcione a saída do `New-GuestConfigurationPackage` cmdlet para o `Test-GuestConfigurationPackage` cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Os seguintes arquivos são criados por `New-GuestConfigurationPolicy`:

- **auditIfNotExists. JSON**
- **deployIfNotExists. JSON**
- **Initiative. JSON**

A saída do cmdlet retorna um objeto que contém o nome de exibição da iniciativa e o caminho dos arquivos de política.

Por fim, publique as definições de política `Publish-GuestConfigurationPolicy` usando o cmdlet.
O cmdlet tem apenas o parâmetro **path** que aponta para o local dos arquivos JSON criados pelo `New-GuestConfigurationPolicy`.

Para executar o comando Publish, você precisa ter acesso para criar políticas no Azure. Os requisitos de autorização específicos estão documentados na página [visão geral do Azure Policy](../overview.md) . A melhor função interna é colaborador de **política de recursos**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 O `Publish-GuestConfigurationPolicy` cmdlet aceita o caminho do pipeline do PowerShell. Esse recurso significa que você pode criar os arquivos de política e publicá-los em um único conjunto de comandos de pipe.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
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

Com o inspec, os parâmetros normalmente são tratados como entrada em tempo de execução ou como código usando atributos. A configuração de convidado ofusca esse processo para que a entrada possa ser fornecida quando a política é atribuída. Um arquivo de atributos é criado automaticamente no computador. Você não precisa criar e adicionar um arquivo em seu projeto. Há duas etapas para adicionar parâmetros ao seu projeto de auditoria do Linux.

Defina a entrada no arquivo Ruby onde você faz o script do que auditar no computador. Um exemplo é fornecido abaixo.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Os cmdlets `New-GuestConfigurationPolicy` e `Test-GuestConfigurationPolicyPackage` incluem um parâmetro denominado **Parameters**. Esse parâmetro usa uma tabela de hash, incluindo todos os detalhes sobre cada parâmetro e cria automaticamente todas as seções necessárias dos arquivos usados para criar cada definição de Azure Policy.

O exemplo a seguir cria uma definição de política para auditar um caminho de arquivo, em que o usuário fornece o caminho no momento da atribuição de política.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = "File path to be audited."      # Policy parameter description (optional)
        ResourceType = "ChefInSpecResource"           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = "AttributesYmlContent" # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

Para políticas do Linux, inclua a propriedade **AttributesYmlContent** em sua configuração e substitua os valores conforme necessário. O agente de configuração convidado cria automaticamente o arquivo YAML usado por inspec para armazenar atributos. Veja o exemplo abaixo.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "path: /tmp"
        }
    }
}
```

## <a name="policy-lifecycle"></a>Ciclo de vida da política

Para liberar uma atualização para a definição de política, há dois campos que exigem atenção.

- **Versão**: ao executar o `New-GuestConfigurationPolicy` cmdlet, você deve especificar um número de versão maior do que o publicado atualmente. A propriedade atualiza a versão da atribuição de configuração de convidado para que o agente reconheça o pacote atualizado.
- **contentHash**: essa propriedade é atualizada automaticamente pelo `New-GuestConfigurationPolicy` cmdlet. É um valor de hash do pacote criado pelo `New-GuestConfigurationPackage`. A propriedade deve estar correta para o `.zip` arquivo que você publicar. Se apenas a propriedade **contentUri** for atualizada, a extensão não aceitará o pacote de conteúdo.

A maneira mais fácil de liberar um pacote atualizado é repetir o processo descrito neste artigo e fornecer um número de versão atualizado. Esse processo garante que todas as propriedades tenham sido corretamente atualizadas.

## <a name="optional-signing-guest-configuration-packages"></a>Opcional: assinando pacotes de configuração de convidado

As políticas personalizadas de configuração de convidado usam hash SHA256 para validar se o pacote de política não foi alterado.
Opcionalmente, os clientes também podem usar um certificado para assinar pacotes e forçar a extensão de configuração de convidado a permitir somente conteúdo assinado.

Para habilitar esse cenário, há duas etapas que precisam ser concluídas. Execute o cmdlet para assinar o pacote de conteúdo e acrescente uma marca para os computadores que devem exigir que o código seja assinado.

Para usar o recurso de validação de assinatura, `Protect-GuestConfigurationPackage` execute o cmdlet para assinar o pacote antes que ele seja publicado. Este cmdlet requer um certificado de ' assinatura de código '.

Parâmetros do `Protect-GuestConfigurationPackage` cmdlet:

- **Caminho**: caminho completo do pacote de configuração do convidado.
- **PublicGpgKeyPath**: caminho de chave de GPG pública. Esse parâmetro só tem suporte ao assinar conteúdo para Linux.

Uma boa referência para a criação de chaves GPG para usar com computadores Linux é fornecida por um artigo no GitHub, [gerando uma nova chave GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

O agente GuestConfiguration espera que a chave pública do certificado esteja presente no `/usr/local/share/ca-certificates/extra` caminho em computadores Linux. Para o nó verificar o conteúdo assinado, instale a chave pública do certificado no computador antes de aplicar a política personalizada. Esse processo pode ser feito usando qualquer técnica dentro da VM ou usando Azure Policy. Um modelo de exemplo é [fornecido aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
A política de acesso de Key Vault deve permitir que o provedor de recursos de computação acesse certificados durante as implantações. Para obter etapas detalhadas, consulte [configurar Key Vault para máquinas virtuais no Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Depois que o conteúdo for publicado, acrescente uma marca com `GuestConfigPolicyCertificateValidation` nome e `enabled` valor a todas as máquinas virtuais em que a assinatura de código deve ser necessária. Consulte os [exemplos de marca](../samples/built-in-policies.md#tags) de como as marcas podem ser entregues em escala usando Azure Policy. Depois que essa marca estiver em vigor, a definição de política gerada `New-GuestConfigurationPolicy` usando o cmdlet habilita o requisito por meio da extensão de configuração de convidado.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Solucionando problemas de atribuições de política de configuração de convidado (versão prévia)

Uma ferramenta está disponível na visualização para auxiliar na solução de problemas Azure Policy atribuições de configuração de convidado. A ferramenta está em versão prévia e foi publicada no Galeria do PowerShell como nome do módulo [solução de problemas de configuração de convidado](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Para obter mais informações sobre os cmdlets nesta ferramenta, use o comando Get-Help no PowerShell para mostrar as diretrizes internas. Como a ferramenta está obtendo atualizações frequentes, essa é a melhor maneira de obter as informações mais recentes.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as VMs de auditoria com a [configuração de convidado](../concepts/guest-configuration.md).
- Entenda como [criar políticas programaticamente](programmatically-create.md).
- Saiba como [obter dados de conformidade](get-compliance-data.md).
