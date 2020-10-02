---
title: Gatilhos e associações no Azure Functions
description: Aprenda a usar gatilhos e associações para conectar sua função do Azure a eventos online e serviços baseados em nuvem.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: e00fd3d1dac0a18ac7f7377e08ae8d20ae132c56
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91652596"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Conceitos de gatilhos e de associações do Azure Functions

Neste artigo, você aprende os conceitos de alto nível em torno de gatilhos e associações de funções.

Os gatilhos são o que causa a execução de uma função. Um gatilho define como uma função é invocada e uma função deve ter exatamente um gatilho. Gatilhos têm dados associados, que geralmente são fornecidos como o conteúdo da função. 

A associação a uma função é uma maneira de conectar declarativamente outro recurso à função; as associações podem ser conectadas como *associações de entrada*, associações de *saída*ou ambas. Dados de associações são fornecidos para a função como parâmetros.

Você pode misturar e combinar diferentes associações para atender às suas necessidades. Associações são opcionais e uma função pode ter uma ou várias associações de entrada e/ou saída.

Gatilhos e associações permitem que você evite codificar o acesso a outros serviços. Sua função recebe dados (por exemplo, o conteúdo de uma mensagem da fila) em parâmetros de função. Você envia dados (por exemplo, para criar uma mensagem da fila) usando o valor retornado da função. 

Considere os exemplos a seguir de como você pode implementar funções diferentes.

| Cenário de exemplo | Gatilho | Associação de entrada | Associação de saída |
|-------------|---------|---------------|----------------|
| Uma nova mensagem de fila chega, que executa uma função para gravar em outra fila. | Espera<sup>*</sup> | *Nenhuma* | Espera<sup>*</sup> |
|Um trabalho agendado lê o conteúdo do armazenamento de BLOBs e cria um novo documento Cosmos DB. | Temporizador | Armazenamento de Blobs | Cosmos DB |
|A grade de eventos é usada para ler uma imagem do armazenamento de BLOBs e um documento do Cosmos DB para enviar um email. | Grade de Eventos | Armazenamento de BLOBs e Cosmos DB | SendGrid |
| Um webhook que usa Microsoft Graph para atualizar uma planilha do Excel. | HTTP | *Nenhuma* | Microsoft Graph |

<sup>\*</sup> Representa filas diferentes

Esses exemplos não devem ser completos, mas são fornecidos para ilustrar como você pode usar gatilhos e associações juntos.

###  <a name="trigger-and-binding-definitions"></a>Definições de associação e gatilho

Os gatilhos e as associações são definidos de forma diferente, dependendo da abordagem de desenvolvimento.

| Plataforma | Gatilhos e associações são configurados por... |
|-------------|--------------------------------------------|
| Biblioteca de classes C# | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decoração de métodos e parâmetros com atributos C# |
| Todos os outros (incluindo portal do Azure) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Atualizando [function.jsem](./functions-reference.md) ([esquema](http://json.schemastore.org/function)) |

O portal fornece uma interface do usuário para essa configuração, mas você pode editar o arquivo diretamente abrindo o **Editor avançado** disponível por meio da guia **integrar** da sua função.

No .NET, o tipo de parâmetro define o tipo de dados para dados de entrada. Por exemplo, use `string` para associar ao texto de um gatilho de fila, uma matriz de bytes para ler como binário e um tipo personalizado para desserializar para um objeto.

Para idiomas que são digitados dinamicamente como JavaScript, use a propriedade `dataType` no arquivo *function.json*. Por exemplo, para ler o conteúdo de uma solicitação HTTP em formato binário, defina `dataType` para `binary`:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Outras opções para `dataType` são `stream` e `string`.

## <a name="binding-direction"></a>Direção de associação

Todos os disparadores e associações têm uma propriedade `direction` no arquivo [function.json](./functions-reference.md):

- Para gatilhos, a direção sempre é `in`
- Associações de entrada e saída usam `in` e `out`
- Algumas associações dão suporte a uma direção especial `inout`. Se você usar `inout` , somente o **Editor avançado** estará disponível por meio da guia **integrar** no Portal.

Quando você usa [atributos em uma biblioteca de classes](functions-dotnet-class-library.md) para configurar associações e gatilhos, a direção é fornecida em um construtor de atributo ou inferida do tipo de parâmetro.

## <a name="add-bindings-to-a-function"></a>Adicionar associações a uma função

Você pode conectar sua função a outros serviços usando associações de entrada ou saída. Adicione uma associação adicionando suas definições específicas à sua função. Para saber como, consulte [Adicionar associações a uma função existente no Azure Functions](add-bindings-existing-function.md).  

## <a name="supported-bindings"></a>Associações com suporte

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Para obter informações sobre quais associações estão na visualização ou são aprovadas para o uso de produção, consulte [Idiomas com suporte](supported-languages.md).

## <a name="bindings-code-examples"></a>Exemplos de código de associações

Use a tabela a seguir para encontrar exemplos de tipos de associação específicos que mostram como trabalhar com associações em suas funções. Primeiro, escolha a guia idioma que corresponde ao seu projeto. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="resources"></a>Recursos
- [Padrões e expressões de associação](./functions-bindings-expressions-patterns.md)
- [Usando o valor de retorno da função do Azure](./functions-bindings-return-value.md)
- [Como registrar uma expressão de associação](./functions-bindings-register.md)
- Teste:
  - [Estratégias para testar seu código no Azure Functions](functions-test-a-function.md)
  - [Executar manualmente uma função não disparada por HTTP](functions-manually-run-non-http.md)
- [Tratamento de erros de associação](./functions-bindings-errors.md)

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Registrar Azure Functions extensões de associação](./functions-bindings-register.md)
