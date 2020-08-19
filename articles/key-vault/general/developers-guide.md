---
title: Guia do desenvolvedor do Cofre da Chave do Azure
description: Os desenvolvedores podem usar o Cofre da Chave do Azure para gerenciar chaves de criptografia no ambiente do Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: 2f90ba0bb732930b4cf3b1c832c6954683119f5f
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585858"
---
# <a name="azure-key-vault-developers-guide"></a>Guia do desenvolvedor do Cofre da Chave do Azure

O Key Vault permite acessar com segurança informações confidenciais nos aplicativos:

- As chaves e os segredos são protegidos sem que você mesmo precise escrever código e você pode usá-los facilmente em seus aplicativos.
- Você pode fazer com que seus clientes possuam e gerenciem suas próprias chaves e se concentrar em fornecer os principais recursos do software. Dessa forma, seus aplicativos não terão a responsabilidade ou a responsabilidade potencial das chaves de locatário e dos segredos de seus clientes.
- Seu aplicativo pode usar chaves para assinatura e criptografia, mas ainda manter o gerenciamento de chaves fora de seu aplicativo, permitindo que sua solução seja adequada a um aplicativo distribuído geograficamente.
- Gerenciar certificados de Key Vault. Para obter mais informações, consulte [certificados](../certificates/about-certificates.md)

Para obter mais informações gerais sobre Azure Key Vault, consulte [o que é Key Vault](overview.md)).

## <a name="public-previews"></a>Visualizações públicas

Periodicamente, lançamos uma visualização pública de um novo recurso do Key Vault. Experimente isso e diga-nos o que você acha via azurekeyvault@microsoft.com, nosso endereço de email para comentários.

## <a name="creating-and-managing-key-vaults"></a>Criando e gerenciando Cofres da Chave

O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais e outras chaves e segredos, mas seu código precisa autenticar para o Key Vault para recuperá-los. Identidades gerenciadas para recursos do Azure facilita a solução desse problema, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD (Azure Active Directory). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código. 

Para obter mais informações sobre identidades gerenciadas para recursos do Azure, consulte [visão geral de identidades gerenciadas](../../active-directory/managed-identities-azure-resources/overview.md). Para obter mais informações sobre como trabalhar com o Azure AD, consulte [integrando aplicativos com Azure Active Directory](../../active-directory/develop/active-directory-integrating-applications.md).

Antes de trabalhar com chaves, segredos ou certificados em seu cofre de chaves, você criará e gerenciará seu cofre de chaves por meio da CLI, do PowerShell, de Modelos do Resource Manager ou REST, conforme descrito nos seguintes artigos:

- [Criar e gerenciar Cofres das Chaves com CLI](quick-create-cli.md)
- [Criar e gerenciar Cofres das Chaves com PowerShell](quick-create-powershell.md)
- [Criar e gerenciar cofres de chaves com o portal do Azure](quick-create-portal.md)
- [Criar e gerenciar Cofres de Chaves com REST](/rest/api/keyvault/vaults/createorupdate)

### <a name="set-and-retrieve-secrets"></a>Definir e recuperar segredos

- [Definir e recuperar um segredo com a CLI](../secrets/quick-create-cli.md)
- [Definir e recuperar um segredo com o PowerShell](../secrets/quick-create-powershell.md)
- [Definir e recuperar um segredo com o portal do Azure](../secrets/quick-create-portal.md)
- [Operações de segredos com REST](/rest/api/keyvault/#secret-operations)
- [Definir e recuperar um segredo com o Python](../secrets/quick-create-python.md)
- [Definir e recuperar um segredo com Java](../secrets/quick-create-java.md)
- [Definir e recuperar um segredo com Node.js](../secrets/quick-create-node.md)
- [Definir e recuperar um segredo com o .NET (SDK do v4)](../secrets/quick-create-net.md)
- [Criar um cofre de chaves e adicionar um segredo por meio de um modelo do Azure Resource Manager](../secrets/quick-create-template.md)

### <a name="set-and-retrieve-keys"></a>Definir e recuperar chaves

- [Definir e recuperar uma chave com a CLI](../keys/quick-create-cli.md)
- [Definir e recuperar uma chave com o PowerShell](../keys/quick-create-powershell.md)
- [Definir e recuperar uma chave com o portal do Azure](../keys/quick-create-portal.md)
- [Operações de chaves com REST](/rest/api/keyvault/#key-operations)
- [Definir e recuperar uma chave com Python](../secrets/quick-create-python.md)

### <a name="set-and-retrieve-certificates"></a>Definir e recuperar certificados
- [Definir e recuperar um certificado com a CLI](../certificates/quick-create-cli.md)
- [Definir e recuperar um certificado com o PowerShell](../certificates/quick-create-powershell.md)
- [Definir e recuperar um certificado com o portal do Azure](../certificates/quick-create-portal.md)
- [Operações de chaves com REST](/rest/api/keyvault/#certificate-operations)
- [Definir e recuperar um certificado com Python](../certificates/quick-create-python.md)

## <a name="coding-with-key-vault"></a>Codificação com o Cofre da Chave

O sistema de gerenciamento do Key Vault para programadores consiste em várias interfaces. Esta seção contém links para todas as linguagens, bem como alguns exemplos de código. 

### <a name="supported-programming-and-scripting-languages"></a>Linguagens de script e programação com suporte

#### <a name="rest"></a>REST

Todos os recursos do Key Vault são acessíveis por meio da interface REST; cofres, chaves, segredos etc. 

[Referência da API REST do Key Vault](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Referência da API .NET para o Keu Vault](/dotnet/api/overview/azure/key-vault?view=azure-dotnet).

#### <a name="java"></a>Java

[Java SDK para Key Vault](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

No Node.js, a API de gerenciamento do Key Vault e a API de objeto do Key Vault são separadas. O seguinte artigo de visão geral dá acesso a ambas. 

[Módulos do Azure Key Vault para Node.js](https://docs.microsoft.com/javascript/api/overview/azure/key-vault-index?view=azure-node-latest)

#### <a name="python"></a>Python

[Bibliotecas do Azure Key Vault para Python](https://docs.microsoft.com/python/api/overview/azure/key-vault-index?view=azure-python)

#### <a name="azure-cli"></a>CLI do Azure

[CLI do Azure para Key Vault](/cli/azure/keyvault?view=azure-cli-latest)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell para Key Vault](/powershell/module/az.keyvault/?view=azps-3.6.1#key_vault)

### <a name="code-examples"></a>Exemplos de código

Para obter exemplos completos sobre como usar o Cofre de Chaves com seus aplicativos, confira:

- [Exemplos de código do Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) -exemplos de código para o Azure Key Vault. 

## <a name="how-tos"></a>Instruções

Os artigos e cenários a seguir fornecem diretrizes específicas da tarefa para trabalhar com o Azure Key Vault:

- [Alterar a ID de locatário do cofre de chaves depois de mover a assinatura](move-subscription.md) - quando você mover sua assinatura do Azure do locatário A para o locatário B, os cofres de chaves existentes não poderão ser acessados pelas entidades (usuários e aplicativos) no locatário B. Corrija isso usando este guia.
- [Acessando o Cofre de Chaves por trás do firewall](access-behind-firewall.md) - para acessar um cofre de chaves, seu aplicativo cliente do cofre de chaves deve ser capaz de acessar vários pontos de extremidade para várias funcionalidades.
- [Como gerar e transferir chaves protegidas pelo HSM para o Cofre de Chaves do Azure](../keys/hsm-protected-keys.md) - isso vai ajudá-lo a planejar, gerar e transferir suas próprias chaves protegidas pelo HSM a serem usadas com o Cofre de Chaves do Azure.
- [Como transmitir valores seguros (como senhas) durante a implantação](../../azure-resource-manager/templates/key-vault-parameter.md) - Quando você precisa transmitir um valor seguro (como uma senha) como um parâmetro durante a implantação, é possível armazenar esse valor como um segredo em um Cofre de Chaves do Azure e fazer referência ao valor em outros modelos do Resource Manager.
- [Como usar o Cofre de Chaves para o gerenciamento extensível de chaves com o SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) - O Conector do SQL Server para o Cofre de Chaves do Azure permite que o SQL Server e o SQL em uma VM utilizem o serviço do Cofre de Chaves do Azure como um provedor EKM (gerenciamento extensível de chaves) para proteger suas chaves de criptografia para o vínculo de aplicativos; Transparent Data Encryption, Criptografia de Backup e Criptografia de Nível de Coluna.
- [Como implantar certificados em VMs do Cofre de Chaves](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - Um aplicativo em nuvem em execução em uma VM no Azure precisa de um certificado. Como você obtém esse certificado para essa VM atualmente?
- [Deploying Azure Web App Certificate through Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) (Implantando o certificado de aplicativo Web do Azure por meio do Key Vault) fornece instruções passo a passo para implantar certificados armazenados no Key Vault como parte da oferta do [Certificado do Serviço de Aplicativo](https://azure.microsoft.com/blog/internals-of-app-service-certificate/).
- [Conceder permissão para vários aplicativos acessarem um key vault](group-permissions-for-apps.md) política de controle de acesso do Key Vault dá suporte a até 1024 entradas. No entanto, você pode criar um grupo de segurança do Azure Active Directory. Adicione todas as entidades de serviço associadas a esse grupo de segurança e, em seguida, conceda a esse grupo de segurança acesso ao Key Vault.
- Para obter mais diretrizes específicas da tarefa sobre como integrar e usar os Cofres de Chaves com o Azure, consulte os [Exemplos de modelo do Azure Resource Manager do Ryan Jones para o Cofre de Chaves](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Como usar a exclusão reversível do Key Vault com CLI](soft-delete-cli.md) orienta pela utilização e ciclo de vida de um cofre de chaves e de vários objetos de cofre de chaves com a exclusão reversível habilitada.
- [Como usar a exclusão reversível do Key Vault com PowerShell](soft-delete-powershell.md) orienta pela utilização e ciclo de vida de um cofre de chaves e de vários objetos de cofre de chaves com a exclusão reversível habilitada.

## <a name="integrated-with-key-vault"></a>Introdução ao Cofre de Chaves

Estes artigos abordam outros cenários e serviços que usam ou se integram ao Key Vault.

- [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md) aproveita o recurso de [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão do setor do Windows e o recurso [DM-cript](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o sistema operacional e os discos de dados. A solução é integrada ao Cofre de Chaves do Azure para ajudá-lo a controlar e a gerenciar as chaves de criptografia de disco e segredos em sua assinatura de cofre de chaves, garantindo ao mesmo tempo que todos os dados nos discos de máquina virtual sejam criptografados em repouso no armazenamento do Azure.
- O [Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md) fornece a opção de criptografia dos dados que são armazenados na conta. Em relação ao gerenciamento de chaves, o Data Lake Store fornece dois modos para gerenciar suas chaves-mestras de criptografia (MEKs), que são necessárias para descriptografar os dados armazenados no Data Lake Store. Você também pode deixar o Data Lake Store gerenciar as MEKs para você ou optar por manter a propriedade das MEKs usando sua conta do Cofre de Chaves do Azure. Você pode especificar o modo de gerenciamento de chaves ao criar uma conta do Data Lake Store.
- [Proteção de Informações do Azure](/azure/information-protection/plan-implement-tenant-key) permite a você gerenciar sua própria chave de locatário. Por exemplo, em vez de a Microsoft gerenciar sua chave de locatário (o padrão), você pode gerenciá-la para cumprir os regulamentos específicos que se aplicam à sua organização. Gerenciar sua própria chave de locatário também é conhecido como "traga sua própria chave", ou BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Visões gerais e conceitos do Key Vault

- [Key Vault comportamento de exclusão reversível](soft-delete-overview.md)) Descreve um recurso que permite a recuperação de objetos excluídos, independentemente de a exclusão ser acidental ou intencional.
- [Limitação de cliente do Key Vault](overview-throttling.md) orienta você sobre os conceitos básicos de limitação e oferece uma abordagem para seu aplicativo.
- [Mundos de segurança do Key Vault](overview-security-worlds.md) descreve as relações entre regiões e áreas de segurança.

## <a name="social"></a>Social

- [Blog do Cofre de Chaves](https://aka.ms/kvblog)
- [Fórum do Cofre de Chaves](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Bibliotecas de Suporte

- A [Biblioteca Principal do Microsoft Azure Key Vault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) fornece as interfaces **IKey** e **IKeyResolver** para localizar chaves com base em identificadores e realizar operações com chaves.
- As [Extensões do Cofre de Chaves do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fornecem recursos estendidos para o Cofre de Chaves do Azure.
