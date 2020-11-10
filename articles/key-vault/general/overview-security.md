---
title: Segurança do Azure Key Vault
description: Gerencie permissões de acesso para Azure Key Vault, chaves e segredos. Abrange o modelo de autenticação e autorização do Key Vault e descreve como proteger o cofre de chaves.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: mbaldwin
ms.openlocfilehash: dc08df7390285f9b6e4701bb1ca5c4227b19f1da
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445023"
---
# <a name="azure-key-vault-security"></a>Segurança do Azure Key Vault

Você usa Azure Key Vault para proteger chaves de criptografia e segredos como certificados, cadeias de conexão e senhas na nuvem. Ao armazenar dados confidenciais e críticos para os negócios, você precisa tomar medidas para maximizar a segurança de seus cofres e os dados armazenados neles.

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

Quando você cria um cofre de chaves em uma assinatura do Azure, ele é automaticamente associado ao locatário do Azure Active Directory da assinatura. Qualquer pessoa que tentar gerenciar ou recuperar conteúdo de um cofre deve ser autenticada pelo Azure AD.

- A autenticação estabelece a identidade do chamador.
- A autorização determina quais operações o chamador pode realizar. A autorização no Key Vault usa uma combinação de [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md) e políticas de acesso de Azure Key Vault.

### <a name="access-model-overview"></a>Visão geral do modelo de acesso

O acesso aos cofres é feito através de duas interfaces ou planos. Os planos são o plano de gerenciamento e o plano de dados.

- O *plano de gerenciamento* é onde você gerencia o Key Vault em si é a interface usada para criar e excluir cofres. Também é possível ler as propriedades do cofre de chaves e gerenciar políticas de acesso.
- O *plano de dados* permite trabalhar com os dados armazenados em um cofre de chaves. Você pode adicionar, excluir e alterar chaves, segredos e certificados.

Para acessar um cofre de chaves em qualquer um dos planos, todos os chamadores (usuários ou aplicativos) devem estar autenticados ou autorizados. Os dois planos usam o Azure AD (Azure Active Directory) para autenticação. Para autorização, o plano de gerenciamento usa o controle de acesso baseado em função do Azure (RBAC do Azure) e o plano de dados usa uma política de acesso Key Vault.

O modelo de mecanismo único para autenticação em ambos os planos tem vários benefícios:

- As organizações podem controlar centralmente o acesso a todos os cofres de chaves em sua organização.
- Se um usuário parte, ele perde instantaneamente o acesso a todos os cofres de chaves na organização.
- As organizações podem personalizar a autenticação usando as opções no Azure Active Directory, como habilitar a autenticação multifator para obter maior segurança.

### <a name="managing-administrative-access-to-key-vault"></a>Gerenciamento de acesso administrativo ao Key Vault

Quando criar um cofre de chaves em um grupo de recursos, gerencie o acesso usando o Azure AD. Conceda a usuários ou grupos a capacidade de gerenciar os cofres de chaves em um grupo de recursos. Você pode conceder acesso em um nível de escopo específico atribuindo as funções apropriadas do Azure. Para conceder acesso a um usuário para gerenciar os cofres de chaves, atribua uma função `key vault Contributor` predefinida ao usuário em um escopo específico. Os seguintes níveis de escopos podem ser atribuídos a uma função do Azure:

- **Assinatura** : uma função do Azure atribuída no nível de assinatura se aplica a todos os grupos de recursos e recursos dentro dessa assinatura.
- **Grupo de recursos** : uma função do Azure atribuída no nível do grupo de recursos se aplica a todos os recursos nesse grupo de recursos.
- **Recurso específico** : uma função do Azure atribuída a um recurso específico se aplica a esse recurso. Nesse caso, o recurso é um cofre de chaves específico.

Há várias funções predefinidas. Se uma função predefinida não atender às suas necessidades, você poderá definir sua própria função. Para obter mais informações, consulte [RBAC do Azure: funções internas](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se um usuário tem permissões `Contributor` para um plano de gerenciamento de cofre de chaves, pode conceder a si mesmo o acesso ao plano de dados definindo a política de acesso do Key Vault. Você deve controlar rigorosamente quem tem função de acesso `Contributor` aos cofres de chaves. Certifique-se de que apenas pessoas autorizadas possam acessar e gerenciar seus cofres de chaves, chaves, segredos e certificados.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Controle do acesso aos dados do Key Vault

As políticas de acesso ao Key Vault concedem separadamente as permissões a chaves, segredos ou certificados. Você pode conceder a um usuário o acesso apenas às chaves e não aos segredos. As permissões de acesso para chaves, segredos e certificados são gerenciadas no nível do cofre.

> [!IMPORTANT]
> As políticas de acesso do Key Vault não aceitam permissões granulares no nível do objeto, como uma chave, um segredo ou um certificado específicos. Quando um usuário tem permissão para criar e excluir chaves, ele pode executar essas operações em todas as chaves no cofre de chaves.

Você pode definir políticas de acesso para um cofre de chaves usar as [portal do Azure](assign-access-policy-portal.md), [CLI do Azure](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md)ou as [APIs REST de gerenciamento de Key Vault](/rest/api/keyvault/).

Você pode restringir o acesso ao plano de dados usando [pontos de extremidade de serviço de rede virtual no Azure Key Vault](overview-vnet-service-endpoints.md)). É possível configurar [regras de firewalls e da rede virtual](network-security.md) para ter uma camada adicional de segurança.

## <a name="network-access"></a>Acesso de rede

Reduza a exposição de seus cofres especificando quais endereços IP têm acesso a eles. Os pontos de extremidade de serviço de rede virtual para o Azure Key Vault permitem restringir o acesso a uma rede virtual especificada. Os pontos de extremidade também permitem restringir o acesso a uma lista de intervalos de endereços IPv4 (protocolo de Internet versão 4). Qualquer usuário que conecte o cofre de chaves de fora dessas fontes terá acesso negado.

Depois que as regras de firewall estiverem ativas, os usuários podem apenas ler os dados do Key Vault quando as solicitações deles originarem-se de redes virtuais ou intervalos de endereços IPv4 permitidos. Isso também aplica-se ao acessar o Key Vault a partir do portal do Azure. Embora os usuários possam navegar em um cofre de chaves a partir do portal do Azure, eles talvez não consigam listar chaves, segredos ou certificados se o computador cliente deles não estiver na lista permitida. Isso também afeta o Seletor do Cofre de Chaves de outros serviços do Azure. Os usuários poderão ver a lista de cofres de chaves, mas não listar chaves, se as regras de firewall impedirem o computador cliente.

Para obter mais informações sobre o endereço de rede do Azure Key Vault, veja [Pontos de extremidade de serviço de rede virtual para Azure Key Vault](overview-vnet-service-endpoints.md))

## <a name="tls-and-https"></a>TLS e HTTPS

*   O front-end de Key Vault (plano de dados) é um servidor de vários locatários. Isso significa que os cofres de chaves de diferentes clientes podem compartilhar o mesmo endereço IP público. Para alcançar o isolamento, cada solicitação HTTP é autenticada e autorizada independentemente de outras solicitações.
*   Você pode identificar versões mais antigas do TLS para relatar vulnerabilidades, mas como o endereço IP público é compartilhado, não é possível que a equipe de serviço do cofre de chaves desabilite versões antigas do TLS para cofres de chaves individuais no nível de transporte.
*   O protocolo HTTPS permite que o cliente participe da negociação TLS. Os **clientes podem impor a versão mais recente do TLS** e sempre que um cliente faz isso, a conexão inteira usará a proteção de nível correspondente. O fato de que Key Vault ainda oferece suporte a versões mais antigas do TLS não prejudicará a segurança das conexões usando versões mais recentes do TLS.
*   Apesar de vulnerabilidades conhecidas no protocolo TLS, não há nenhum ataque conhecido que permita que um agente mal-intencionado extraia todas as informações do cofre de chaves quando o invasor inicia uma conexão com uma versão TLS que tem vulnerabilidades. O invasor ainda precisaria se autenticar e se autorizar e, desde que os clientes legítimos sempre se conectem com versões recentes do TLS, não há nenhuma maneira de que as credenciais pudessem ter sido vazadas de vulnerabilidades em versões antigas do TLS.

## <a name="logging-and-monitoring"></a>Log e monitoramento

O registrar em log do Key Vault salva informações sobre as atividades executadas em seu cofre. Para obter detalhes completos, consulte [log de Key Vault](logging.md).

Para obter recomendações sobre o gerenciamento seguro de contas de armazenamento, examine o [Guia de segurança do armazenamento do Azure](../../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Próximas etapas

- [Pontos de extremidade de serviço de rede virtual para o Azure Key Vault](overview-vnet-service-endpoints.md)
- [RBAC do Azure: funções internas](../../role-based-access-control/built-in-roles.md)