---
title: Guia do desenvolvedor do Cofre da Chave do Azure
description: Os desenvolvedores podem usar o Cofre da Chave do Azure para gerenciar chaves de criptografia no ambiente do Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: a8cb0ea9fb3c6e8388271c4274baf4ecc7282cda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247224"
---
# <a name="azure-key-vault-developers-guide"></a>Guia do desenvolvedor do Cofre da Chave do Azure

O Key Vault permite acessar com segurança informações confidenciais nos aplicativos:

- As chaves e os segredos são protegidos sem que você mesmo precise escrever código e você pode usá-los facilmente em seus aplicativos.
- Você pode fazer com que seus clientes possuam e gerenciem suas próprias chaves e se concentrar em fornecer os principais recursos do software. Desta forma, seus aplicativos não serão os donos da responsabilidade ou responsabilidade potencial pelas chaves e segredos dos seus clientes.
- Seu aplicativo pode usar chaves para assinatura e criptografia, mas ainda manter o gerenciamento de chaves fora de seu aplicativo, permitindo que sua solução seja adequada a um aplicativo distribuído geograficamente.
- Gerenciar certificados do Cofre chave. Para obter mais informações, consulte [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md).

Para obter mais informações gerais sobre o Cofre de Chaves do Azure, confira [O que é o Cofre de Chaves](key-vault-overview.md).

## <a name="public-previews"></a>Visualizações públicas

Periodicamente, lançamos uma visualização pública de um novo recurso do Key Vault. Experimente isso e diga-nos o que você acha via azurekeyvault@microsoft.com, nosso endereço de email para comentários.

## <a name="creating-and-managing-key-vaults"></a>Criando e gerenciando Cofres da Chave

O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais e outras chaves e segredos, mas seu código precisa autenticar para o Key Vault para recuperá-los. Identidades gerenciadas para recursos do Azure facilita a solução desse problema, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD (Azure Active Directory). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código. 

Para obter mais informações sobre identidades gerenciadas para recursos do Azure, consulte [visão geral de identidades gerenciadas](../active-directory/managed-identities-azure-resources/overview.md). Para saber mais sobre como trabalhar com AAD, confira [Integrando aplicativos com o Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

Antes de trabalhar com chaves, segredos ou certificados em seu cofre de chaves, você criará e gerenciará seu cofre de chaves por meio da CLI, do PowerShell, de Modelos do Resource Manager ou REST, conforme descrito nos seguintes artigos:

- [Criar e gerenciar Cofres das Chaves com CLI](quick-create-cli.md)
- [Criar e gerenciar Cofres das Chaves com PowerShell](quick-create-powershell.md)
- [Crie e gerencie os cofres-chave com a porta Azure](quick-create-portal.md)
- [Crie e gerencie os cofres-chave com python](quick-create-python.md)
- [Crie e gerencie cofres-chave com Java](quick-create-java.md)
- [Crie e gerencie cofres-chave com node.js](quick-create-node.md)
- [Criar e gerenciar cofres-chave com .NET (v4 SDK)](quick-create-net.md)
- [Criar um cofre de chaves e adicionar um segredo por meio de um modelo do Azure Resource Manager](quick-create-template.md)
- [Criar e gerenciar Cofres de Chaves com REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Codificação com o Cofre da Chave

O sistema de gerenciamento do Key Vault para programadores consiste em várias interfaces. Esta seção contém links para todas as linguagens, bem como alguns exemplos de código. 

### <a name="supported-programming-and-scripting-languages"></a>Linguagens de script e programação com suporte

#### <a name="rest"></a>REST

Todos os recursos do Key Vault são acessíveis por meio da interface REST; cofres, chaves, segredos etc. 

[Referência da API REST do Key Vault](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Referência da API .NET para o Keu Vault](/dotnet/api/overview/azure/key-vault?view=azure-dotnet).

Para obter mais informações sobre a versão 2.x do SDK do .NET, consulte as [Notas de versão](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK para Key Vault](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

No Node.js, a API de gerenciamento do Key Vault e a API de objeto do Key Vault são separadas. O seguinte artigo de visão geral dá acesso a ambas. 

[Módulos do Azure Key Vault para Node.js](/javascript/api/overview/azure/key-vault?view=azure-node-latest)

#### <a name="python"></a>Python

[Bibliotecas do Azure Key Vault para Python](/python/api/overview/azure/key-vault?view=azure-python)

#### <a name="azure-cli"></a>CLI do Azure

[CLI do Azure para Key Vault](/cli/azure/keyvault?view=azure-cli-latest)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell para Key Vault](/powershell/module/az.keyvault/?view=azps-3.6.1#key_vault)

### <a name="code-examples"></a>Exemplos de código

Para obter exemplos completos sobre como usar o Cofre de Chaves com seus aplicativos, confira:

- [Exemplos de código do Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) -exemplos de código para o Azure Key Vault. 
- [Usar Azure Key Vault de um aplicativo Web](quick-create-net.md) – tutorial para ajudar você a aprender a usar o Azure Key Vault em um aplicativo Web no Azure. 

## <a name="how-tos"></a>Instruções

Os artigos e cenários a seguir fornecem diretrizes específicas da tarefa para trabalhar com o Azure Key Vault:

- [Alterar a ID de locatário do cofre de chaves depois de mover a assinatura](key-vault-subscription-move-fix.md) - quando você mover sua assinatura do Azure do locatário A para o locatário B, os cofres de chaves existentes não poderão ser acessados pelas entidades (usuários e aplicativos) no locatário B. Corrija isso usando este guia.
- [Acessando o Cofre de Chaves por trás do firewall](key-vault-access-behind-firewall.md) - para acessar um cofre de chaves, seu aplicativo cliente do cofre de chaves deve ser capaz de acessar vários pontos de extremidade para várias funcionalidades.
- [Como gerar e transferir chaves protegidas pelo HSM para o Cofre de Chaves do Azure](key-vault-hsm-protected-keys.md) - isso vai ajudá-lo a planejar, gerar e transferir suas próprias chaves protegidas pelo HSM a serem usadas com o Cofre de Chaves do Azure.
- [Como transmitir valores seguros (como senhas) durante a implantação](../azure-resource-manager/templates/key-vault-parameter.md) - Quando você precisa transmitir um valor seguro (como uma senha) como um parâmetro durante a implantação, é possível armazenar esse valor como um segredo em um Cofre de Chaves do Azure e fazer referência ao valor em outros modelos do Resource Manager.
- [Como usar o Cofre de Chaves para o gerenciamento extensível de chaves com o SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) - O Conector do SQL Server para o Cofre de Chaves do Azure permite que o SQL Server e o SQL em uma VM utilizem o serviço do Cofre de Chaves do Azure como um provedor EKM (gerenciamento extensível de chaves) para proteger suas chaves de criptografia para o vínculo de aplicativos; Transparent Data Encryption, Criptografia de Backup e Criptografia de Nível de Coluna.
- [Como implantar certificados em VMs do Cofre de Chaves](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - Um aplicativo em nuvem em execução em uma VM no Azure precisa de um certificado. Como você obtém esse certificado para essa VM atualmente?
- [Configurar o Key Vault com a rotação de chaves e auditoria de ponta a ponta](key-vault-key-rotation-log-monitoring.md) – Explica como configurar a auditoria e a rotação de chaves com o Azure Key Vault.
- [Deploying Azure Web App Certificate through Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) (Implantando o certificado de aplicativo Web do Azure por meio do Key Vault) fornece instruções passo a passo para implantar certificados armazenados no Key Vault como parte da oferta do [Certificado do Serviço de Aplicativo](https://azure.microsoft.com/blog/internals-of-app-service-certificate/).
- [Conceder permissão para vários aplicativos acessarem um key vault](key-vault-group-permissions-for-apps.md) política de controle de acesso do Key Vault dá suporte a até 1024 entradas. No entanto, você pode criar um grupo de segurança do Azure Active Directory. Adicione todas as entidades de serviço associadas a esse grupo de segurança e, em seguida, conceda a esse grupo de segurança acesso ao Key Vault.
- Para obter mais diretrizes específicas da tarefa sobre como integrar e usar os Cofres de Chaves com o Azure, consulte os [Exemplos de modelo do Azure Resource Manager do Ryan Jones para o Cofre de Chaves](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Como usar a exclusão reversível do Key Vault com CLI](key-vault-soft-delete-cli.md) orienta pela utilização e ciclo de vida de um cofre de chaves e de vários objetos de cofre de chaves com a exclusão reversível habilitada.
- [Como usar a exclusão reversível do Key Vault com PowerShell](key-vault-soft-delete-powershell.md) orienta pela utilização e ciclo de vida de um cofre de chaves e de vários objetos de cofre de chaves com a exclusão reversível habilitada.

## <a name="integrated-with-key-vault"></a>Introdução ao Cofre de Chaves

Estes artigos abordam outros cenários e serviços que usam ou se integram ao Key Vault.

- [O Azure Disk Encryption](../security/fundamentals/encryption-overview.md) aproveita o recurso padrão do [setor BitLocker](https://technet.microsoft.com/library/cc732774.aspx) do Windows e o recurso [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o SO e os discos de dados. A solução é integrada ao Cofre de Chaves do Azure para ajudá-lo a controlar e a gerenciar as chaves de criptografia de disco e segredos em sua assinatura de cofre de chaves, garantindo ao mesmo tempo que todos os dados nos discos de máquina virtual sejam criptografados em repouso no armazenamento do Azure.
- O [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) fornece a opção de criptografia dos dados que são armazenados na conta. Em relação ao gerenciamento de chaves, o Data Lake Store fornece dois modos para gerenciar suas chaves-mestras de criptografia (MEKs), que são necessárias para descriptografar os dados armazenados no Data Lake Store. Você também pode deixar o Data Lake Store gerenciar as MEKs para você ou optar por manter a propriedade das MEKs usando sua conta do Cofre de Chaves do Azure. Você pode especificar o modo de gerenciamento de chaves ao criar uma conta do Data Lake Store.
- [Proteção de Informações do Azure](/azure/information-protection/plan-implement-tenant-key) permite a você gerenciar sua própria chave de locatário. Por exemplo, em vez de a Microsoft gerenciar sua chave de locatário (o padrão), você pode gerenciá-la para cumprir os regulamentos específicos que se aplicam à sua organização. Gerenciar sua própria chave de locatário também é conhecido como "traga sua própria chave", ou BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Visões gerais e conceitos do Key Vault

- [Comportamento de exclusão reversível do Key Vault](key-vault-ovw-soft-delete.md) descreve um recurso que permite a recuperação de objetos excluídos, seja a exclusão acidental ou intencional.
- [Limitação de cliente do Key Vault](key-vault-ovw-throttling.md) orienta você sobre os conceitos básicos de limitação e oferece uma abordagem para seu aplicativo.
- [Visão geral de chaves de conta de armazenamento do Key Vault](key-vault-ovw-storage-keys.md) descreve as chaves de Contas de Armazenamento do Azure de integração do Key Vault.
- [Mundos de segurança do Key Vault](key-vault-ovw-security-worlds.md) descreve as relações entre regiões e áreas de segurança.

## <a name="social"></a>Redes sociais

- [Blog do Cofre de Chaves](https://aka.ms/kvblog)
- [Fórum do Cofre de Chaves](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Bibliotecas de Suporte

- A [Biblioteca Principal do Microsoft Azure Key Vault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) fornece as interfaces **IKey** e **IKeyResolver** para localizar chaves com base em identificadores e realizar operações com chaves.
- As [Extensões do Cofre de Chaves do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fornecem recursos estendidos para o Cofre de Chaves do Azure.
