---
title: Guia do desenvolvedor de Azure Key Vault
description: Os desenvolvedores podem usar Azure Key Vault para gerenciar chaves de criptografia no ambiente de Microsoft Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 1bfd156f9fbdb69766e4588a342a695fbd79ca3a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595309"
---
# <a name="azure-key-vault-developers-guide"></a>Guia do desenvolvedor de Azure Key Vault

Key Vault permite que você acesse com segurança informações confidenciais de dentro de seus aplicativos:

- Chaves e segredos são protegidos sem precisar escrever o código por conta própria e você pode usá-los facilmente de seus aplicativos.
- Você pode ter seus clientes e gerenciar suas próprias chaves para que possa se concentrar em fornecer os principais recursos de software. Dessa forma, os aplicativos não serão responsáveis ou potencialmente responsáveis pelas chaves e segredos do locatário de seus clientes.
- Seu aplicativo pode usar chaves para assinatura e criptografia, mas mantém o gerenciamento de chaves externo do seu aplicativo, permitindo que sua solução seja adequada como um aplicativo distribuído geograficamente.
- A partir da versão de setembro de 2016 do Key Vault, seus aplicativos agora podem gerenciar Key Vault certificados. Para obter mais informações, consulte [sobre chaves, segredos e certificados](/rest/api/keyvault/about-keys--secrets-and-certificates).

Para obter mais informações gerais sobre o Cofre de Chaves do Azure, confira [O que é o Cofre de Chaves](key-vault-overview.md).

## <a name="public-previews"></a>Visualizações públicas

Periodicamente, lançamos uma visualização pública de um novo recurso Key Vault. Experimente-os e diga-nos o que você imagina por meio de azurekeyvault@microsoft.com, nosso endereço de email de comentários.

## <a name="creating-and-managing-key-vaults"></a>Criando e gerenciando cofres de chaves

Azure Key Vault fornece uma maneira de armazenar com segurança credenciais e outras chaves e segredos, mas seu código precisa autenticar-se no Key Vault para recuperá-las. Identidades gerenciadas para recursos do Azure facilitam a solução desse problema, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Key Vault, sem ter nenhuma credencial em seu código. 

Para obter mais informações sobre identidades gerenciadas para recursos do Azure, consulte [a visão geral de identidades gerenciadas](../active-directory/managed-identities-azure-resources/overview.md). Para obter mais informações sobre como trabalhar com o AAD, consulte [integrando aplicativos com Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

Antes de trabalhar com chaves, segredos ou certificados no cofre de chaves, você criará e gerenciará seu cofre de chaves por meio da CLI, do PowerShell, dos modelos do Resource Manager ou do REST, conforme descrito nos seguintes artigos:

- [Criar e gerenciar cofres de chaves com a CLI](key-vault-manage-with-cli2.md)
- [Criar e gerenciar cofres de chaves com o PowerShell](key-vault-overview.md)
- [Criar um cofre de chaves e adicionar um segredo por meio de um modelo do Azure Resource Manager](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Criar e gerenciar cofres de chaves com REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Codificando com Key Vault

O sistema de gerenciamento de Key Vault para programadores consiste em várias interfaces. Esta seção contém links para todos os idiomas, bem como alguns exemplos de código. 

### <a name="supported-programming-and-scripting-languages"></a>Linguagens de script e programação com suporte

#### <a name="rest"></a>REST

Todos os seus recursos de Key Vault podem ser acessados por meio da interface REST; cofres, chaves, segredos, etc. 

[Key Vault referência da API REST](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Referência da API do .net para Key Vault](/dotnet/api/microsoft.azure.keyvault).

Para obter mais informações sobre a versão 2.x do SDK do .NET, consulte as [Notas de versão](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[SDK do Java para Key Vault](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

No node. js, a API de gerenciamento de Key Vault e a API de objeto de Key Vault são separadas. O seguinte artigo de visão geral fornece acesso a ambos. 

[Módulos Azure Key Vault para node. js](/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Bibliotecas de Azure Key Vault para Python](/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>CLI do Azure 2

[CLI do Azure para Key Vault](/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell para Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

### <a name="quickstart-guides"></a>Guias de início rápido

- [Criar Cofre da Chave](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Introdução ao Key Vault no node. js](https://github.com/Azure-Samples/key-vault-node-getting-started)

### <a name="code-examples"></a>Exemplos de código

Para obter exemplos completos usando Key Vault com seus aplicativos, consulte:

- [Exemplos de código de Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) -exemplos de código para Azure Key Vault. 
- [Use Azure Key Vault de um aplicativo Web](quick-create-net.md) -tutorial para ajudá-lo a aprender a usar Azure Key Vault de um aplicativo Web no Azure. 

## <a name="how-tos"></a>Instruções

Os seguintes artigos e cenários fornecem diretrizes específicas para tarefas para trabalhar com Azure Key Vault:

- [Alterar a ID de locatário do cofre de chaves depois de mover a assinatura](key-vault-subscription-move-fix.md) - quando você mover sua assinatura do Azure do locatário A para o locatário B, os cofres de chaves existentes não poderão ser acessados pelas entidades (usuários e aplicativos) no locatário B. Corrija isso usando este guia.
- [Acessando o Cofre de Chaves por trás do firewall](key-vault-access-behind-firewall.md) - para acessar um cofre de chaves, seu aplicativo cliente do cofre de chaves deve ser capaz de acessar vários pontos de extremidade para várias funcionalidades.
- [Como gerar e transferir chaves protegidas pelo HSM para o Cofre de Chaves do Azure](key-vault-hsm-protected-keys.md) - isso vai ajudá-lo a planejar, gerar e transferir suas próprias chaves protegidas pelo HSM a serem usadas com o Cofre de Chaves do Azure.
- [Como transmitir valores seguros (como senhas) durante a implantação](../azure-resource-manager/resource-manager-keyvault-parameter.md) - Quando você precisa transmitir um valor seguro (como uma senha) como um parâmetro durante a implantação, é possível armazenar esse valor como um segredo em um Cofre de Chaves do Azure e fazer referência ao valor em outros modelos do Resource Manager.
- [Como usar o Cofre de Chaves para o gerenciamento extensível de chaves com o SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) - O Conector do SQL Server para o Cofre de Chaves do Azure permite que o SQL Server e o SQL em uma VM utilizem o serviço do Cofre de Chaves do Azure como um provedor EKM (gerenciamento extensível de chaves) para proteger suas chaves de criptografia para o vínculo de aplicativos; Transparent Data Encryption, Criptografia de Backup e Criptografia de Nível de Coluna.
- [Como implantar certificados em VMs do Cofre de Chaves](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - Um aplicativo em nuvem em execução em uma VM no Azure precisa de um certificado. Como você obtém esse certificado para essa VM atualmente?
- [Como configurar Key Vault com a rotação de chaves e auditoria de ponta a ponta](key-vault-key-rotation-log-monitoring.md) – explica como configurar a rotação de chaves e a auditoria com Azure Key Vault.
- [A implantação de um certificado de aplicativo Web do Azure por meio de Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) fornece instruções passo a passo para implantar certificados armazenados no Key Vault como parte da oferta de [certificado do serviço de aplicativo](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) .
- [Conceder permissão a muitos aplicativos para acessar um cofre de chaves](key-vault-group-permissions-for-apps.md) Key Vault política de controle de acesso dá suporte a até 1024 entradas. No entanto, você pode criar um grupo de segurança Azure Active Directory. Adicione todas as entidades de serviço associadas a esse grupo de segurança e, em seguida, conceda acesso a esse grupo de segurança para Key Vault.
- Para obter mais diretrizes específicas da tarefa sobre como integrar e usar os Cofres de Chaves com o Azure, consulte os [Exemplos de modelo do Azure Resource Manager do Ryan Jones para o Cofre de Chaves](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Como usar Key Vault exclusão reversível com a CLI](key-vault-soft-delete-cli.md) orienta o uso e o ciclo de vida de um cofre de chaves e de vários objetos do cofre de chaves com a exclusão reversível habilitada.
- [Como usar Key Vault exclusão reversível com o PowerShell](key-vault-soft-delete-powershell.md) orienta o uso e o ciclo de vida de um cofre de chaves e de vários objetos do cofre de chaves com a exclusão reversível habilitada.

## <a name="integrated-with-key-vault"></a>Integrado com o Key Vault

Estes artigos são sobre outros cenários e serviços que usam ou integram com Key Vault.

- A [Azure Disk Encryption](../security/fundamentals/encryption-overview.md) aproveita o recurso padrão da indústria [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) do Windows e o recurso [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o SO e os discos de dados. A solução é integrada ao Cofre de Chaves do Azure para ajudá-lo a controlar e a gerenciar as chaves de criptografia de disco e segredos em sua assinatura de cofre de chaves, garantindo ao mesmo tempo que todos os dados nos discos de máquina virtual sejam criptografados em repouso no armazenamento do Azure.
- [Azure data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) fornece a opção para a criptografia de dados armazenados na conta. Para o gerenciamento de chaves, Data Lake Store fornece dois modos para gerenciar suas chaves de criptografia mestras (MEKs), que são necessárias para descriptografar todos os dados armazenados no Data Lake Store. Você pode permitir que Data Lake Store gerenciar o MEKs para você ou optar por manter a propriedade do MEKs usando sua conta de Azure Key Vault. Você especifica o modo de gerenciamento de chaves ao criar uma conta de Data Lake Store.
- A [proteção de informações do Azure](/azure/information-protection/plan-implement-tenant-key) permite que você Manager sua própria chave de locatário. Por exemplo, em vez de Microsoft gerenciar sua chave de locatário (o padrão), você pode gerenciar sua própria chave de locatário para obedecer a regulamentos específicos que se aplicam à sua organização. Gerenciar sua própria chave de locatário também é conhecido como trazer sua própria chave, ou BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Visões gerais e conceitos do Key Vault

- [Key Vault comportamento de exclusão reversível](key-vault-ovw-soft-delete.md) descreve um recurso que permite a recuperação de objetos excluídos, independentemente de a exclusão ser acidental ou intencional.
- [Key Vault limitação de cliente](key-vault-ovw-throttling.md) orienta você aos conceitos básicos de limitação e oferece uma abordagem para seu aplicativo.
- [Key Vault visão geral das chaves de conta de armazenamento](key-vault-ovw-storage-keys.md) descreve as chaves de contas de armazenamento do Azure de integração do Key Vault.
- [Key Vault mundos de segurança](key-vault-ovw-security-worlds.md) descreve as relações entre regiões e áreas de segurança.

## <a name="social"></a>Redes Sociais

- [Blog do Cofre de Chaves](https://aka.ms/kvblog)
- [Fórum do Cofre de Chaves](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Bibliotecas de Suporte

- [Microsoft Azure biblioteca de Key Vault Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) fornece interfaces **iKey** e **IKeyResolver** para localizar chaves de identificadores e executar operações com chaves.
- As [Extensões do Cofre de Chaves do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fornecem recursos estendidos para o Cofre de Chaves do Azure.
