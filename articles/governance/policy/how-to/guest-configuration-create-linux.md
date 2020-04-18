---
title: Como criar políticas de configuração de hóspedes para Linux
description: Saiba como criar uma política de configuração de hóspedes de política do Azure para Linux.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 65e0082f87f05104e9a57ff0342cd3d2950b63e8
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617939"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Como criar políticas de configuração de hóspedes para Linux

Antes de criar políticas personalizadas, leia as informações de visão geral na [Configuração de hóspedes da diretiva do Azure](../concepts/guest-configuration.md).
 
Para saber mais sobre a criação de políticas de configuração de hóspedes para Windows, consulte a página [Como criar políticas de configuração de hóspedes para Windows](./guest-configuration-create.md)

Ao auditar o Linux, a configuração do convidado usa [o Chef InSpec](https://www.inspec.io/). O perfil do InSpec define a condição em que a máquina deve estar. Se a avaliação da configuração falhar, a auditoria de efeito de **políticaIfNotExists** será acionada e a máquina será considerada **incompatível**.

[A configuração do hóspede da política do Azure](../concepts/guest-configuration.md) só pode ser usada para auditar as configurações dentro das máquinas. A remediação das configurações dentro das máquinas ainda não está disponível.

Use as seguintes ações para criar sua própria configuração para validar o estado de uma máquina Azure ou não-Azure.

> [!IMPORTANT]
> Políticas personalizadas com configuração de hóspedes é um recurso de visualização.

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

Criar um artefato de configuração de hóspedes, testar o artefato automatizado, criar uma definição de política e publicar a política, é totalmente automatizado usando o módulo de configuração de hóspedes no PowerShell. O módulo pode ser instalado em uma máquina executando Windows, macOS ou Linux com o PowerShell 6.2 ou posteriorem-se executados localmente, ou com [o Azure Cloud Shell,](https://shell.azure.com)ou com a [imagem Do Azure PowerShell Core Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Compilação de configurações não é suportada no Linux.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Artefatos de configuração de hóspedes e política para Linux

Mesmo em ambientes Linux, a Configuração do Convidado usa a Configuração de Estado Desejado como uma abstração de idioma. A implementação é baseada em código nativo (C++) para que não exija carregamento do PowerShell. No entanto, ele requer uma configuração MOF descrevendo detalhes sobre o ambiente. O DSC está agindo como um invólucro para o InSpec para padronizar como ele é executado, como os parâmetros são fornecidos e como a saída é devolvida ao serviço. Pouco conhecimento do DSC é necessário ao trabalhar com conteúdo inSpec personalizado.

#### <a name="configuration-requirements"></a>Requisitos de configuração

O nome da configuração personalizada deve ser consistente em todos os lugares. O nome do arquivo .zip para o pacote de conteúdo, o nome de configuração no arquivo MOF e o nome de atribuição de convidado no modelo Gerenciador de recursos, deve ser o mesmo.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Configuração personalizada de configuração de hóspedes no Linux

Configuração do convidado `ChefInSpecResource` no Linux usa o recurso para fornecer ao mecanismo o nome do [perfil InSpec](https://www.inspec.io/docs/reference/profiles/). **O nome** é a única propriedade de recursos necessária. Crie um arquivo YaML e um arquivo de script Ruby, conforme detalhado abaixo.

Primeiro, crie o arquivo YaML usado pelo InSpec. O arquivo fornece informações básicas sobre o meio ambiente. Um exemplo é dado abaixo:

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

Salve este arquivo `inspec.yml` com nome `linux-path` em uma pasta nomeada em seu diretório de projeto.

Em seguida, crie o arquivo Ruby com a abstração de linguagem InSpec usada para auditar a máquina.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Salve este arquivo `linux-path.rb` com nome `controls` em `linux-path` uma nova pasta nomeada dentro do diretório.

Finalmente, crie uma configuração, importe o `ChefInSpecResource` módulo de recurso **GuestConfiguration** e use o recurso para definir o nome do perfil InSpec.

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
AuditFilePathExists -out ./Config
```

O `Node AuditFilePathExists` comando não é tecnicamente necessário, mas `AuditFilePathExists.mof` produz um `localhost.mof`arquivo nomeado em vez do padrão, . Ter o nome do arquivo .mof seguir a configuração facilita a organização de muitos arquivos ao operar em escala.

Você deve agora ter uma estrutura de projeto como abaixo:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

Os arquivos de suporte devem ser embalados juntos. O pacote concluído é usado pela Configuração do Convidado para criar as definições de Política do Azure.

O `New-GuestConfigurationPackage` cmdlet cria o pacote. Parâmetros `New-GuestConfigurationPackage` do cmdlet ao criar conteúdo Linux:

- **Nome**: Nome do pacote de configuração do convidado.
- **Configuração:** Compilou o caminho completo do documento de configuração.
- **Caminho**: Caminho da pasta de saída. Esse parâmetro é opcional. Se não for especificado, o pacote é criado no diretório atual.
- **ChefProfilePath**: Caminho completo para o perfil do InSpec. Esse parâmetro é suportado apenas ao criar conteúdo para auditar o Linux.

Execute o seguinte comando para criar um pacote usando a configuração dada na etapa anterior:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof'
  -ChefProfilePath './'
```

Depois de criar o pacote de configuração, mas antes de publicá-lo no Azure, você pode testar o pacote a partir de sua estação de trabalho ou ambiente CI/CD. O cmdlet `Test-GuestConfigurationPackage` GuestConfiguration inclui o mesmo agente em seu ambiente de desenvolvimento que é usado dentro de máquinas Azure. Usando essa solução, você pode realizar testes de integração localmente antes de liberar para ambientes em nuvem faturados.

Uma vez que o agente está realmente avaliando o ambiente local, na maioria dos casos você precisa executar o Test-cmdlet na mesma plataforma de SO que você planeja auditar.

Parâmetros `Test-GuestConfigurationPackage` do cmdlet:

- **Nome**: Nome da política de configuração do convidado.
- **Parâmetro**: Parâmetros de políticas fornecidos em formato hashtable.
- **Caminho**: Caminho completo do pacote de configuração do convidado.

Execute o seguinte comando para testar o pacote criado pela etapa anterior:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists.zip
```

O cmdlet também suporta a entrada do gasoduto PowerShell. Encane `New-GuestConfigurationPackage` a saída de `Test-GuestConfigurationPackage` cmdlet para o cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Os seguintes arquivos `New-GuestConfigurationPolicy`são criados por:

- **auditIfNotExists.json**
- **implantarIfNotExists.json**
- **Iniciativa.json**

A saída cmdlet retorna um objeto contendo o nome de exibição da iniciativa e o caminho dos arquivos de diretiva.

Por fim, publique as `Publish-GuestConfigurationPolicy` definições de política usando o cmdlet.
O cmdlet só tem o parâmetro **Caminho** que aponta para a `New-GuestConfigurationPolicy`localização dos arquivos JSON criados por .

Para executar o comando Publicar, você precisa acessar para criar políticas no Azure. Os requisitos específicos de autorização estão documentados na página Visão Geral da Política do [Azure.](../overview.md) O melhor papel incorporado é o **Resource Policy Contributor**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 O `Publish-GuestConfigurationPolicy` cmdlet aceita o caminho do gasoduto PowerShell. Esse recurso significa que você pode criar os arquivos de diretiva e publicá-los em um único conjunto de comandos encanados.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
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

Com o InSpec, os parâmetros são normalmente tratados como entrada em tempo de execução ou como código usando atributos. A configuração do convidado ofuscar esse processo para que a entrada possa ser fornecida quando a diretiva for atribuída. Um arquivo de atributos é criado automaticamente dentro da máquina. Você não precisa criar e adicionar um arquivo em seu projeto. Existem duas etapas para adicionar parâmetros ao seu projeto de auditoria Linux.

Defina a entrada no arquivo Ruby onde você roteirizar o que auditar na máquina. Um exemplo é dado abaixo.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Os `New-GuestConfigurationPolicy` cmdlets `Test-GuestConfigurationPolicyPackage` e incluem um parâmetro chamado **Parâmetros**. Este parâmetro leva um hashtable incluindo todos os detalhes sobre cada parâmetro e cria automaticamente todas as seções necessárias dos arquivos usados para criar cada definição de Política do Azure.

O exemplo a seguir cria uma definição de diretiva para auditar um caminho de arquivo, onde o usuário fornece o caminho no momento da atribuição da diretiva.

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

Para políticas Linux, inclua a propriedade **AttributesYmlContent** em sua configuração e substitua os valores conforme necessário. O agente de configuração do convidado cria automaticamente o arquivo YAML usado pelo InSpec para armazenar atributos. Veja o exemplo abaixo.

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

Para liberar uma atualização para a definição da diretiva, existem dois campos que requerem atenção.

- **Versão:** Quando você `New-GuestConfigurationPolicy` executa o cmdlet, você deve especificar um número de versão maior do que o que está publicado atualmente. A propriedade atualiza a versão da atribuição Configuração do Convidado para que o agente reconheça o pacote atualizado.
- **conteúdoHash**: Esta propriedade é `New-GuestConfigurationPolicy` atualizada automaticamente pelo cmdlet. É um valor de hash do `New-GuestConfigurationPackage`pacote criado por . A propriedade deve estar `.zip` correta para o arquivo que você publica. Se apenas a propriedade **contentUri** for atualizada, a Extensão não aceitará o pacote de conteúdo.

A maneira mais fácil de liberar um pacote atualizado é repetir o processo descrito neste artigo e fornecer um número de versão atualizado. Esse processo garante que todas as propriedades tenham sido corretamente atualizadas.

## <a name="optional-signing-guest-configuration-packages"></a>Opcional: Assinatura de pacotes de configuração de hóspedes

As políticas personalizadas de configuração do convidado usam hash SHA256 para validar o pacote de políticas não foi alterado.
Opcionalmente, os clientes também podem usar um certificado para assinar pacotes e forçar a extensão de Configuração do Convidado a permitir apenas conteúdo assinado.

Para habilitar este cenário, há dois passos que você precisa completar. Execute o cmdlet para assinar o pacote de conteúdo e aempenhe uma tag às máquinas que devem exigir que o código seja assinado.

Para usar o recurso Validação `Protect-GuestConfigurationPackage` de assinatura, execute o cmdlet para assinar o pacote antes de ser publicado. Este cmdlet requer um certificado 'Assinatura de Código'.

Parâmetros `Protect-GuestConfigurationPackage` do cmdlet:

- **Caminho**: Caminho completo do pacote de configuração do convidado.
- **PublicGpgKeyPath**: Caminho-chave do GPG público. Este parâmetro só é suportado ao assinar conteúdo para Linux.

Uma boa referência para criar chaves GPG para usar com máquinas Linux é fornecida por um artigo no GitHub, [gerando uma nova chave GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

O agente GuestConfiguration espera que a chave `/usr/local/share/ca-certificates/extra` pública do certificado esteja presente no caminho das máquinas Linux. Para que o nó verifique o conteúdo assinado, instale a chave pública do certificado na máquina antes de aplicar a política personalizada. Esse processo pode ser feito usando qualquer técnica dentro da VM, ou usando a Política do Azure. Um modelo de exemplo é [fornecido aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
A política de acesso do Key Vault deve permitir que o provedor de recursos Compute acesse certificados durante as implantações. Para obter etapas detalhadas, consulte [Configurar o Key Vault para máquinas virtuais no Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Depois que seu conteúdo for publicado, `GuestConfigPolicyCertificateValidation` anexar `enabled` uma tag com nome e valor a todas as máquinas virtuais onde a assinatura de código deve ser necessária. Consulte as [amostras de tag](../samples/built-in-policies.md#tags) para saber como as tags podem ser entregues em escala usando a Política do Azure. Uma vez que esta tag esteja em `New-GuestConfigurationPolicy` vigor, a definição de diretiva gerada usando o cmdlet permite a exigência através da extensão Configuração do Convidado.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Solução de problemas De configuração de configuração de hóspedes (Visualização)

Uma ferramenta está disponível na pré-visualização para ajudar na solução de problemas adepções de configuração de hóspedes da política do Azure. A ferramenta está em pré-visualização e foi publicada na PowerShell Gallery como nome do módulo [Guest Configuration Troubleshooter](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Para obter mais informações sobre os cmdlets nesta ferramenta, use o comando Get-Help no PowerShell para mostrar a orientação incorporada. Como a ferramenta está recebendo atualizações frequentes, essa é a melhor maneira de obter informações mais recentes.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a auditoria de VMs com [configuração de hóspedes](../concepts/guest-configuration.md).
- Entenda como [criar políticas programáticas.](programmatically-create.md)
- Saiba como [obter dados de conformidade](get-compliance-data.md).
