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
ms.openlocfilehash: dbe13b2d1291f212af7da9d1176bc3d90997978b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428960"
---
# <a name="azure-key-vault-security"></a>Segurança do Azure Key Vault

Você precisa proteger chaves de criptografia e segredos como certificados, cadeias de conexão e senhas na nuvem para que você esteja usando Azure Key Vault. Como você está armazenando dados confidenciais e críticos para os negócios, você precisa tomar medidas para maximizar a segurança de seus cofres e os dados armazenados neles. Este artigo abordará alguns dos conceitos que você deve considerar ao projetar sua segurança de Azure Key Vault.

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

Quando você cria um cofre de chaves em uma assinatura do Azure, ele é automaticamente associado ao locatário do Azure Active Directory da assinatura. Qualquer pessoa que tentar gerenciar ou recuperar conteúdo de um cofre deve ser autenticada pelo Azure AD.

- A autenticação estabelece a identidade do chamador.
- A autorização determina quais operações o chamador pode executar. A autorização no Key Vault usa uma combinação de RBAC ( [controle de acesso baseado em função](../../role-based-access-control/overview.md) ) e políticas de acesso de Azure Key Vault.

### <a name="access-model-overview"></a>Visão geral do modelo de acesso

O acesso aos cofres ocorre por meio de duas interfaces ou planos. Esses planos são o plano de gerenciamento e o plano de dados.

- O *plano de gerenciamento* é onde você gerencia Key Vault ele mesmo e é a interface usada para criar e excluir cofres. Você também pode ler as propriedades do cofre de chaves e gerenciar políticas de acesso.
- O *plano de dados* permite que você trabalhe com os dados armazenados em um cofre de chaves. Você pode adicionar, excluir e alterar chaves, segredos e certificados.

Para acessar um cofre de chaves em qualquer plano, todos os chamadores (usuários ou aplicativos) devem ser autenticados e autorizados. Os dois planos usam o Azure AD (Azure Active Directory) para autenticação. Para receber autorização, o plano de gerenciamento usa o RBAC (controle de acesso baseado em função) e o plano de dados usa uma política de acesso ao Key Vault.

O modelo de mecanismo único para autenticação em ambos os planos tem vários benefícios:

- As organizações podem controlar centralmente o acesso a todos os cofres de chaves em sua organização.
- Se um usuário parte, ele perde instantaneamente o acesso a todos os cofres de chaves na organização.
- As organizações podem personalizar a autenticação usando as opções no Azure AD, como para habilitar a autenticação multifator para aumentar a segurança

### <a name="managing-administrative-access-to-key-vault"></a>Gerenciando o acesso administrativo ao Key Vault

Ao criar um cofre de chaves em um grupo de recursos, você gerencia o acesso usando o Azure AD. Conceda a usuários ou grupos a capacidade de gerenciar os cofres de chaves em um grupo de recursos. Você pode conceder acesso em um nível de escopo específico atribuindo as funções RBAC apropriadas. Para conceder acesso a um usuário para gerenciar os cofres de chaves, atribua uma função `key vault Contributor` predefinida ao usuário em um escopo específico. Os seguintes níveis de escopos podem ser atribuídos a uma função RBAC:

- **Assinatura**: uma função RBAC atribuída no nível de assinatura se aplica a todos os grupos de recursos e recursos dentro dessa assinatura.
- **Grupo de recursos**: uma função RBAC atribuída no nível do grupo de recursos se aplica a todos os recursos nesse grupo de recursos.
- **Recurso específico**: uma função RBAC atribuída a um recurso específico se aplica a esse recurso. Nesse caso, o recurso é um cofre de chaves específico.

Há várias funções predefinidas. Se uma função predefinida não atender às suas necessidades, você poderá definir sua própria função. Para obter mais informações, consulte [RBAC: funções internas](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se um usuário tem permissões `Contributor` para um plano de gerenciamento de cofre de chaves, pode conceder a si mesmo o acesso ao plano de dados definindo a política de acesso do Key Vault. Você deve controlar rigorosamente quem tem função de acesso `Contributor` aos cofres de chaves. Certifique-se de que apenas pessoas autorizadas possam acessar e gerenciar seus cofres de chaves, chaves, segredos e certificados.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Controlando o acesso a dados de Key Vault

Key Vault políticas de acesso concedem permissões separadamente a chaves, segredos ou certificado. Você pode conceder a um usuário o acesso apenas às chaves e não aos segredos. Permissões de acesso para chaves, segredos e certificados são gerenciadas no nível do cofre.

> [!IMPORTANT]
> As políticas de acesso do Key Vault não aceitam permissões granulares no nível do objeto, como uma chave, um segredo ou um certificado específicos. Quando um usuário tem permissão para criar e excluir chaves, ele pode executar essas operações em todas as chaves no cofre de chaves.

Para definir as políticas de acesso para um cofre de chaves, use o [Portal do Microsoft Azure](https://portal.azure.com/), a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest), o [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou as [APIs REST de gerenciamento do Key Vault](/rest/api/keyvault/).

Você pode restringir o acesso ao plano de dados usando [pontos de extremidade de serviço de rede virtual para Azure Key Vault](overview-vnet-service-endpoints.md)). É possível configurar [regras de firewalls e da rede virtual](network-security.md) para ter uma camada adicional de segurança.

## <a name="network-access"></a>Acesso de rede

Você pode reduzir a exposição de seus cofres especificando quais endereços IP têm acesso a eles. Os pontos de extremidade de serviço de rede virtual para o Azure Key Vault permitem restringir o acesso a uma rede virtual especificada. Os pontos de extremidade também permitem restringir o acesso a uma lista de intervalos de endereços IPv4 (protocolo de Internet versão 4). Qualquer usuário que conecte o cofre de chaves de fora dessas fontes terá acesso negado.

Depois que as regras de firewall estiverem em vigor, os usuários só poderão ler dados de Key Vault quando suas solicitações se originarem de redes virtuais permitidas ou intervalos de endereços IPv4. Isso também aplica-se ao acessar o Key Vault a partir do portal do Azure. Embora os usuários possam navegar em um cofre de chaves a partir do portal do Azure, eles talvez não consigam listar chaves, segredos ou certificados se o computador cliente deles não estiver na lista permitida. Isso também afeta o Seletor do Cofre de Chaves de outros serviços do Azure. Os usuários poderão ver a lista de cofres de chaves, mas não listar chaves, se as regras de firewall impedirem o computador cliente.

Para obter mais informações sobre Azure Key Vault endereço de rede revisar [pontos de extremidade de serviço de rede virtual para Azure Key Vault](overview-vnet-service-endpoints.md))

## <a name="monitoring"></a>Monitoramento

Key Vault log salva informações sobre as atividades executadas em seu cofre. Logs de Key Vault:

- Todas as solicitações da API REST autenticadas, incluindo solicitações com falha
  - Operações no próprio cofre de chaves. Essas operações incluem criação, exclusão, definição de políticas de acesso e atualização de atributos do Key Vault, como marcas.
  - Operações em chaves e segredos no cofre de chaves, incluindo:
    - A criação, modificação ou exclusão dessas chaves ou segredos.
    - A assinatura, verificação, criptografia, descriptografia, encapsulamento e desencapsulamento de chaves, obtenção de segredos e listagem de chaves e segredos (e suas versões).
- Solicitações não autenticadas que resultam em uma resposta 401. Por exemplo, solicitações que não têm um token de portador estão malformadas ou expiradas ou têm um token inválido.

As informações de log podem ser acessadas em 10 minutos após a operação do cofre de chaves. Cabe a você gerenciar seus logs em sua conta de armazenamento.

- Use os métodos de controle de acesso padrão do Azure para proteger seus logs ao restringir quem pode acessá-los.
- Exclua os logs que você não deseja manter em sua conta de armazenamento.

Para obter recomendações sobre o gerenciamento seguro de contas de armazenamento, examine o [Guia de segurança do armazenamento do Azure](../../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Próximas etapas

- [Pontos de extremidade de serviço de rede virtual para Azure Key Vault](overview-vnet-service-endpoints.md))
- [RBAC: funções internas](../../role-based-access-control/built-in-roles.md)
- [pontos de extremidade de serviço de rede virtual para Azure Key Vault](overview-vnet-service-endpoints.md))
