---
title: Segurança do Azure Blockchain Service
description: Azure Blockchain Service de acesso a dados e conceitos de segurança
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 06bf4e0fa4037b07505a4f816fc7af56c14576d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982237"
---
# <a name="azure-blockchain-service-security"></a>Segurança do Azure Blockchain Service

O Azure Blockchain Service usa vários recursos do Azure para manter seus dados seguros e disponíveis. Os dados são protegidos usando isolamento, criptografia e autenticação.

## <a name="isolation"></a>Isolamento

Os recursos do Azure Blockchain Service estão isolados em uma rede virtual privada. Cada nó de transação e validação é uma máquina virtual (VM). As VMs em uma rede virtual não podem se comunicar diretamente com VMs em uma rede virtual diferente. O isolamento garante que a comunicação permaneça privada dentro da rede virtual. Para obter mais informações sobre o isolamento da rede virtual do Azure, consulte [o isolamento na Nuvem Pública do Azure](../../security/fundamentals/isolation-choices.md#networking-isolation).

![Diagrama VNET](./media/data-security/vnet.png)

## <a name="encryption"></a>Criptografia

Os dados do usuário são armazenados no armazenamento do Azure. Os dados do usuário são criptografados em movimento e em repouso para segurança e confidencialidade. Para obter mais informações, [consulte: Guia de segurança do Azure Storage](../../storage/blobs/security-recommendations.md).

## <a name="authentication"></a>Autenticação

As transações podem ser enviadas para os nós de blockchain através de um ponto final do RPC. Os clientes se comunicam com um nó de transação usando um servidor proxy reverso que lida com a autenticação do usuário e criptografa dados através do SSL.

![Diagrama de autenticação](./media/data-security/authentication.png)

Existem três modos de autenticação para acesso rpc.

### <a name="basic-authentication"></a>Autenticação Básica

A autenticação básica usa um cabeçalho de autenticação HTTP contendo o nome de usuário e a senha. Nome de usuário é o nome do nó blockchain. A senha é definida durante o provisionamento de um membro ou nó. A senha pode ser alterada usando o portal Azure ou CLI.

### <a name="access-keys"></a>Chaves de acesso

As teclas de acesso usam uma seqüência gerada aleatoriamente incluída na URL do ponto final. Duas teclas de acesso ajudam a habilitar a rotação da chave. As chaves podem ser regeneradas a partir do portal Azure e da CLI.

### <a name="azure-active-directory"></a>Azure Active Directory

O Azure Active Directory (Azure AD) usa um mecanismo de autenticação baseado em reclamações onde o usuário é autenticado pelo Azure AD usando credenciais de usuário Azure AD. O Azure AD fornece gerenciamento de identidade baseado em nuvem e permite que os clientes usem uma única identidade em toda uma empresa e acessem aplicativos na nuvem. O Azure Blockchain Service integra-se ao Azure AD, permitindo a federação de ID, o login único e a autenticação multifatorial. Você pode atribuir usuários, grupos e funções de aplicativos em sua organização para acesso a membros e nós de blockchain.

O proxy do cliente Azure AD está disponível no [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). O proxy do cliente direciona o usuário para a página de login do Azure AD e obtém um token portador após autenticação bem-sucedida. Posteriormente, o usuário conecta um aplicativo cliente Ethereum, como Geth ou Truffle, ao ponto final do proxy do cliente. Finalmente, quando uma transação é submetida, o proxy do cliente injeta o token do portador no cabeçalho http e o proxy reverso valida o token usando o protocolo OAuth.

## <a name="keys-and-ethereum-accounts"></a>Contas de chaves e Ethereum

Ao provisionar um membro do Azure Blockchain Service, uma conta Ethereum e um par de chaves públicas e privadas são gerados. A chave privada é usada para enviar transações para o blockchain. A conta Ethereum é os últimos 20 bytes do hash da chave pública. A conta Ethereum também é chamada de carteira.

O par de tecla privada e pública é armazenado como um arquivo de chave no formato JSON. A chave privada é criptografada usando a senha inserida quando o serviço de livro razão blockchain é criado.

Chaves privadas são usadas para assinar transações digitalmente. Em blockchains privados, um contrato inteligente assinado por uma chave privada representa a identidade do signatário. Para verificar a validade da assinatura, o receptor pode comparar a chave pública do signatário com o endereço calculado a partir da assinatura.

As teclas de constelação são usadas para identificar exclusivamente um nó quórum. As chaves de constelação são geradas no momento do provisionamento do nó e são especificadas no parâmetro privateFor de uma transação privada no Quorum.

## <a name="next-steps"></a>Próximas etapas

[Veja como configurar o acesso ao Azure Active Directory para o Azure Blockchain Service](configure-aad.md).
