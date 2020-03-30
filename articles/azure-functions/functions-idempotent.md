---
title: Projetando funções azure para entrada idêntica
description: Construindo funções do Azure para ser impotente
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.openlocfilehash: 15af60ac5a862e6fb20e65ba6fbb92482420b7c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226867"
---
# <a name="designing-azure-functions-for-identical-input"></a>Projetando funções azure para entrada idêntica

A realidade da arquitetura baseada em eventos e baseada em mensagens dita a necessidade de aceitar solicitações idênticas, preservando a integridade dos dados e a estabilidade do sistema.

Para ilustrar, considere um botão de chamada de elevador. Quando você aperta o botão, ele acende e um elevador é enviado para o seu chão. Alguns momentos depois, outra pessoa se junta a você no saguão. Essa pessoa sorri para você e aperta o botão iluminado uma segunda vez. Você sorri para si mesmo enquanto é lembrado que o comando para chamar um elevador é idempotente.

Pressionar um botão de chamada de elevador uma segunda, terceira ou quarta vez não tem relação com o resultado final. Quando você aperta o botão, independentemente do número de vezes, o elevador é enviado para o seu chão. Sistemas idempotentes, como o elevador, resultam no mesmo resultado, não importa quantas vezes comandos idênticos sejam emitidos.

Quando se trata de criar aplicações, considere os seguintes cenários:

- O que acontece se seu aplicativo de controle de estoque tentar excluir o mesmo produto mais de uma vez?
- Como sua aplicação de recursos humanos se comporta se há mais de um pedido para criar um registro de funcionário para a mesma pessoa?
- Para onde vai o dinheiro se o seu aplicativo bancário recebe 100 pedidos para fazer o mesmo saque?

Existem muitos contextos onde solicitações para uma função podem receber comandos idênticos. Algumas situações incluem:

- Retente políticas enviando o mesmo pedido muitas vezes
- Comandos armazenados em cache reproduzidos para o aplicativo
- Erros de aplicativo enviando várias solicitações idênticas

Para proteger a integridade dos dados e a saúde do sistema, um aplicativo idempotente contém lógica que pode conter os seguintes comportamentos:

- Verificando a existência de dados antes de tentar executar uma exclusão
- Verificando se os dados já existem antes de tentar executar uma ação de criação
- Conciliar lógica que cria eventual consistência nos dados
- Controles de concorrência
- Detecção de duplicação
- Validação de frescor de dados
- Guarde a lógica para verificar dados de entrada

Em última análise, a idempotência é alcançada garantindo que uma determinada ação seja possível e só seja executada uma vez.
