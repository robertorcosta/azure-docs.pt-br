---
title: Segurança do Azure Key Vault | Microsoft Docs
description: Gerencie permissões de acesso para Azure Key Vault, chaves e segredos. Abrange o modelo de autenticação e autorização do Key Vault e descreve como proteger o cofre de chaves.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 8d6dd38c3d1802c3b5bec82edbda4f9d344fa28b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090511"
---
# <a name="azure-key-vault-security"></a>Segurança do Azure Key Vault

É necessário proteger chaves de criptografia e segredos, como certificados, cadeias de conexão e senhas na nuvem quando você está usando o Azure Key Vault. Como você está armazenando dados confidenciais e comercialmente críticos, é necessário tomar medidas para maximizar a segurança de seus cofres e dos dados armazenados neles. Este artigo abordará alguns dos conceitos que você deve considerar ao projetar a segurança do Azure Key Vault.

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

Quando você cria um cofre de chaves em uma assinatura do Azure, ele é automaticamente associado ao locatário do Azure Active Directory da assinatura. Qualquer pessoa que tentar gerenciar ou recuperar conteúdo de um cofre deve ser autenticada pelo Azure AD.

- A autenticação estabelece a identidade do chamador.
- A autorização determina quais operações o chamador pode realizar. A autorização no Key Vault usa uma combinação de [controle de acesso baseado em função](../../role-based-access-control/overview.md) (RBAC) e políticas de acesso do Azure Key Vault.

### <a name="access-model-overview"></a>Visão geral do modelo de acesso

O acesso aos cofres é feito através de duas interfaces ou planos. Os planos são o plano de gerenciamento e o plano de dados.

- O *plano de gerenciamento* é onde você gerencia o Key Vault em si é a interface usada para criar e excluir cofres. Também é possível ler as propriedades do cofre de chaves e gerenciar políticas de acesso.
- O *plano de dados* permite trabalhar com os dados armazenados em um cofre de chaves. Você pode adicionar, excluir e alterar chaves, segredos e certificados.

Para acessar um cofre de chaves em qualquer um dos planos, todos os chamadores (usuários ou aplicativos) devem estar autenticados ou autorizados. Os dois planos usam o Azure AD (Azure Active Directory) para autenticação. Para receber autorização, o plano de gerenciamento usa o RBAC (controle de acesso baseado em função) e o plano de dados usa uma política de acesso ao Key Vault.

O modelo de mecanismo único para autenticação em ambos os planos tem vários benefícios:

- As organizações podem controlar centralmente o acesso a todos os cofres de chaves em sua organização.
- Se um usuário parte, ele perde instantaneamente o acesso a todos os cofres de chaves na organização.
- As organizações podem personalizar a autenticação usando as opções no Azure Active Directory, como habilitar a autenticação multifator para obter maior segurança.

### <a name="managing-administrative-access-to-key-vault"></a>Gerenciamento de acesso administrativo ao Key Vault

Quando criar um cofre de chaves em um grupo de recursos, gerencie o acesso usando o Azure AD. Conceda a usuários ou grupos a capacidade de gerenciar os cofres de chaves em um grupo de recursos. Conceda o acesso em um nível de escopo específico atribuindo funções RBAC apropriadas. Para conceder acesso a um usuário para gerenciar os cofres de chaves, atribua uma função `key vault Contributor` predefinida ao usuário em um escopo específico. Os seguintes níveis de escopos podem ser atribuídos a uma função RBAC:

- **Assinatura**: uma função RBAC atribuída no nível da assinatura que se aplica a todos os grupos de recursos e recursos dentro dessa assinatura.
- **Grupo de recursos**: uma função RBAC atribuída no nível do grupo de recursos que se aplica a todos os recursos nesse grupo de recursos.
- **Recursos específicos**: uma função atribuída a um recurso específico que se aplica a esse recurso. Nesse caso, o recurso é um cofre de chaves específico.

Há várias funções predefinidas. Se uma função predefinida não atender às suas necessidades, você poderá definir sua própria função. Para saber mais, confira [RBAC: funções internas](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se um usuário tem permissões `Contributor` para um plano de gerenciamento de cofre de chaves, pode conceder a si mesmo o acesso ao plano de dados definindo a política de acesso do Key Vault. Você deve controlar rigorosamente quem tem função de acesso `Contributor` aos cofres de chaves. Certifique-se de que apenas pessoas autorizadas possam acessar e gerenciar seus cofres de chaves, chaves, segredos e certificados.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Controle do acesso aos dados do Key Vault

As políticas de acesso ao Key Vault concedem separadamente as permissões a chaves, segredos ou certificados. Você pode conceder a um usuário o acesso apenas às chaves e não aos segredos. As permissões de acesso para chaves, segredos e certificados são gerenciadas no nível do cofre.

> [!IMPORTANT]
> As políticas de acesso do Key Vault não aceitam permissões granulares no nível do objeto, como uma chave, um segredo ou um certificado específicos. Quando um usuário tem permissão para criar e excluir chaves, ele pode executar essas operações em todas as chaves no cofre de chaves.

Para definir as políticas de acesso para um cofre de chaves, use o [Portal do Microsoft Azure](https://portal.azure.com/), a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest), o [Azure PowerShell](/powershell/azure/) ou as [APIs REST de gerenciamento do Key Vault](/rest/api/keyvault/).

Você pode restringir o acesso ao plano de dados usando [pontos de extremidade de serviço de rede virtual no Azure Key Vault](overview-vnet-service-endpoints.md)). É possível configurar [regras de firewalls e da rede virtual](network-security.md) para ter uma camada adicional de segurança.

## <a name="network-access"></a>Acesso de rede

Reduza a exposição de seus cofres especificando quais endereços IP têm acesso a eles. Os pontos de extremidade de serviço de rede virtual para o Azure Key Vault permitem restringir o acesso a uma rede virtual especificada. Os pontos de extremidade também permitem restringir o acesso a uma lista de intervalos de endereços IPv4 (protocolo de Internet versão 4). Qualquer usuário que conecte o cofre de chaves de fora dessas fontes terá acesso negado.

Depois que as regras de firewall estiverem ativas, os usuários podem apenas ler os dados do Key Vault quando as solicitações deles originarem-se de redes virtuais ou intervalos de endereços IPv4 permitidos. Isso também aplica-se ao acessar o Key Vault a partir do portal do Azure. Embora os usuários possam navegar em um cofre de chaves a partir do portal do Azure, eles talvez não consigam listar chaves, segredos ou certificados se o computador cliente deles não estiver na lista permitida. Isso também afeta o Seletor do Cofre de Chaves de outros serviços do Azure. Os usuários poderão ver a lista de cofres de chaves, mas não listar chaves, se as regras de firewall impedirem o computador cliente.

Para obter mais informações sobre o endereço de rede do Azure Key Vault, veja [Pontos de extremidade de serviço de rede virtual para Azure Key Vault](overview-vnet-service-endpoints.md))

## <a name="monitoring"></a>Monitoramento

O registrar em log do Key Vault salva informações sobre as atividades executadas em seu cofre. Logs do Key Vault:

- Todas as solicitações da API REST autenticadas, incluindo solicitações com falha
  - Operações no próprio cofre de chaves. Essas operações incluem a criação, a exclusão, a configuração de políticas de acesso e a atualização de atributos do cofre de chaves, como as marcas.
  - Operações em chaves e segredos no cofre de chaves, incluindo:
    - A criação, modificação ou exclusão dessas chaves ou segredos.
    - A assinatura, verificação, criptografia, descriptografia, encapsulamento e desencapsulamento de chaves, obtenção de segredos e listagem de chaves e segredos (e suas versões).
- Solicitações não autenticadas que resultam em uma resposta 401. Por exemplo, solicitações que não têm um token de portador estão malformadas ou expiradas ou têm um token inválido.

As informações de log podem ser acessadas até 10 minutos após a operação do cofre de chaves. Cabe a você gerenciar os logs em sua conta de armazenamento.

- Use os métodos de controle de acesso padrão do Azure para proteger seus logs ao restringir quem pode acessá-los.
- Exclua os logs que você não deseja manter em sua conta de armazenamento.

Para obter recomendações sobre o gerenciamento seguro de contas de armazenamento, veja o [Guia de segurança do Armazenamento do Azure](../../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Próximas etapas

- [Pontos de extremidade de serviço de rede virtual para o Azure Key Vault](overview-vnet-service-endpoints.md)
- [RBAC: funções internas](../../role-based-access-control/built-in-roles.md)
- [pontos de extremidade de serviço de rede virtual para o Azure Key Vault](overview-vnet-service-endpoints.md)
