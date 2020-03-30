---
title: Composability azure Blockchain Tokens
description: A composability do Azure Blockchain Tokens oferece flexibilidade para criar tokens para cenários avançados.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: a3fe1b290917de20b7c3af31fe386ed93580d850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325112"
---
# <a name="azure-blockchain-tokens-composability"></a>Composability azure Blockchain Tokens

[!INCLUDE [Preview note](./includes/preview.md)]

A composability do Token fornece flexibilidade para criar tokens para cenários avançados. Você pode ter um cenário complexo que não pode ser implementado usando os [quatro modelos de token pré-construídos](templates.md#base-token-types). A composability de token permite que você projete seus próprios modelos de token adicionando ou removendo comportamentos definidos para construir seu próprio modelo de token. Ao criar um novo modelo de token, o Azure Blockchain Tokens verifica todas as regras de gramática do token. Modelos compostos são salvos no serviço Azure Blockchain Tokens para emissão em redes blockchain conectadas.

Você pode usar os comportamentos do [token](templates.md#token-behaviors) nas seções a seguir para projetar seu modelo de token.

## <a name="burnable-b"></a>Burnable (b)

Capacidade de remover os tokens do fornecimento.

Por exemplo, quando você resgata pontos de cartão de crédito on-line para um cartão de presente, os pontos do cartão de crédito são queimados.

## <a name="delegable-g"></a>Delegável (g)

Capacidade de delegar as ações tomadas no token que você possui.

O delegado pode realizar ações como o dono do token. Por exemplo, você poderia usar um token delegável para implementar uma votação. Um token delegável permite que o proprietário do token de voto tenha alguém votando em seu nome.

## <a name="logable-l"></a>Locável (l)

Capacidade de logar.

Por exemplo, você pode emitir um token logable para uma distribuição de filme para cada cinema mostrando um filme específico. Para que o filme seja reproduzido, a exibição deve registrar uma transação para cada exibição, porque os pagamentos de royalties são por exibição durante a exibição do filme. Os atores construídos podem usar os tokens de filme para validar pagamentos por filme exibido por filme na distribuição.

## <a name="mint-able-m"></a>Mint-able (m)

Capacidade de cunhar tokens adicionais para a classe de tokens. O papel minter inclui o comportamento minável.

Por exemplo, uma empresa de varejo, que quer implementar um programa de fidelidade, pode usar tokens mintable para seu programa de fidelidade. Eles podem ganhar pontos de fidelidade adicionais para seus clientes à medida que sua base de clientes cresce.  

## <a name="non-subdividable-or-whole-d"></a>Não subdividable ou inteiro (~d)

Restrição para evitar que um token seja dividido em partes menores.

Por exemplo, uma única pintura de arte não pode ser subdividida em várias partes menores. 

## <a name="non-transferable-t"></a>Intransferível (~t)

Restrição para evitar uma mudança de propriedade do proprietário inicial do token.

Por exemplo, um diploma universitário é um token intransferível. Uma vez que um diploma é dado a um graduado, ele não pode ser transferido do graduado para outra pessoa.

## <a name="roles-r"></a>Papéis (r)

Capacidade de definir funções dentro da classe de modelo de token para comportamentos específicos.

Você pode fornecer uma lista de nomes de função que um token suporta no momento da criação do token. Quando as funções são especificadas, o usuário pode atribuir funções a esses comportamentos. Atualmente, apenas o papel minter é suportado.

## <a name="singleton-s"></a>Singleton (s)

Restrição para permitir o fornecimento de um token.

Por exemplo, um artefato de museu é um símbolo de singleton. Artefatos de museu são únicos. Um token representando um artefato só tem um único item no fornecimento.

## <a name="subdividable-d"></a>Subdividable (d)

Capacidade de dividir um token em partes menores.

Por exemplo, um dólar pode ser subdividido em centavos.

## <a name="transferable-t"></a>Transferível (t)

Capacidade de transferir a propriedade do token.

Por exemplo, um título de propriedade é um token transferível, que pode ser transferido de uma pessoa para outra quando a propriedade é vendida.

## <a name="next-steps"></a>Próximas etapas

Conheça [o gerenciamento de contas do Azure Blockchain Tokens](account-management.md).
