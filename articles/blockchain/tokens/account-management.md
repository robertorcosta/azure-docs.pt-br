---
title: Gerenciamento de contas do Azure Blockchain Tokens
description: Usando o gerenciamento de contas do Azure Blockchain Tokens, você pode criar grupos e vincular contas blockchain para controlar o acesso a ações blockchain.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74326111"
---
# <a name="azure-blockchain-tokens-account-management"></a>Gerenciamento de contas do Azure Blockchain Tokens

[!INCLUDE [Preview note](./includes/preview.md)]

Para uma solução blockchain, os usuários podem exigir diferentes níveis de acesso aos tokens criados com o serviço Azure Blockchain Tokens. Na maioria dos cenários de blockchain, você precisa planejar e implantar diferentes contas blockchain que existem no livro razão. Você também precisa gerenciar o acesso entre os participantes.Usando o gerenciamento de contas do Azure Blockchain Tokens, você pode criar grupos e vincular contas blockchain para controlar o acesso a ações blockchain.

## <a name="blockchain-networks"></a>Redes blockchain

O Azure Blockchain Tokens permite a implantação e o gerenciamento de tokens em um conjunto de redes blockchain. Você pode conectar um único livro de blockchain ou vários livros de blockchain ao serviço.

## <a name="accounts"></a>Contas

Para redes blockchain conectadas aos Tokens blockchain do Azure, o serviço cria e gerencia os pares de chaves de público privado da conta e realiza a assinatura e envio de transações. O Azure Blockchain Tokens também fornece mapeamento de identidade para combinar contas com a identidade chave pública no livro-razão.

## <a name="groups"></a>Grupos

Os grupos permitem gerenciar um grande número de contas blockchain em redes conectadas. Você pode rastrear e auditar quais aplicativos e usuários no diretório têm a capacidade de usar contas através de APIs do Azure Blockchain Tokens. Por exemplo, você pode agrupar um conjunto de contas que representam diferentes linhas de negócios ou diferentes funções e acesso a tokens blockchain.

Você também pode associar um grupo a um usuário ou diretor de serviço do Azure Active Directory e este principal tem permissões para o grupo e suas contas associadas.  

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [modelos disponíveis de Tokens de Blockchain do Azure](templates.md).
