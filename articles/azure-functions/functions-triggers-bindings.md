---
title: Gatilhos e associações no Azure Functions
description: Aprenda a usar gatilhos e vinculações para conectar sua função Azure a eventos on-line e serviços baseados em nuvem.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276498"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Conceitos de gatilhos e de associações do Azure Functions

Neste artigo você aprende os conceitos de alto nível em torno de gatilhos e vinculações de funções.

Gatilhos são o que fazem com que uma função seja executada. Um gatilho define como uma função é invocada e uma função deve ter exatamente um gatilho. Gatilhos têm dados associados, que geralmente são fornecidos como o conteúdo da função. 

Vincular a uma função é uma forma de conectar declarativamente outro recurso à função; as vinculações podem ser conectadas como *ligações de entrada,* *ligações de saída*ou ambas. Dados de associações são fornecidos à função como parâmetros.

Você pode misturar e combinar diferentes associações para atender às suas necessidades. Associações são opcionais e uma função pode ter uma ou várias associações de entrada e/ou saída.

Gatilhos e vinculações permitem evitar o acesso de codificação difícil a outros serviços. Sua função recebe dados (por exemplo, o conteúdo de uma mensagem da fila) em parâmetros de função. Você envia dados (por exemplo, para criar uma mensagem da fila) usando o valor retornado da função. 

Considere os exemplos a seguir de como você pode implementar diferentes funções.

| Cenário de exemplo | Gatilho | Vinculação de entrada | Vinculação de saída |
|-------------|---------|---------------|----------------|
| Uma nova mensagem de fila chega que executa uma função para escrever em outra fila. | Fila<sup>*</sup> | *Nenhum* | Fila<sup>*</sup> |
|Um trabalho agendado lê o conteúdo do Blob Storage e cria um novo documento Cosmos DB. | Timer | Armazenamento de Blobs | Cosmos DB |
|A Grade de Eventos é usada para ler uma imagem do Blob Storage e um documento do Cosmos DB para enviar um e-mail. | Grade de Eventos | Blob Storage e Cosmos DB | SendGrid |
| Um webhook que usa o Microsoft Graph para atualizar uma folha do Excel. | HTTP | *Nenhum* | Microsoft Graph |

<sup>\*</sup>Representa diferentes filas

Esses exemplos não são feitos para serem exaustivos, mas são fornecidos para ilustrar como você pode usar gatilhos e amarras juntos.

###  <a name="trigger-and-binding-definitions"></a>Definições de gatilho e vinculação

Os gatilhos e ligações são definidos de forma diferente dependendo da abordagem de desenvolvimento.

| Plataforma | Gatilhos e amarrações são configurados por... |
|-------------|--------------------------------------------|
| Biblioteca de classe C# | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;métodos e parâmetros de decoração com atributos C# |
| Todos os outros (incluindo o portal Azure) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;atualização [function.json](./functions-reference.md) [(esquema)](http://json.schemastore.org/function) |

O portal fornece uma iu para esta configuração, mas você pode editar o arquivo diretamente abrindo o **editor Avançado** disponível através da guia **Integrar** sua função.

Em .NET, o tipo de parâmetro define o tipo de dados para dados de entrada. Por exemplo, `string` use para vincular ao texto de um gatilho de fila, uma matriz de bytes para ler como binário e um tipo personalizado para desserializar a um objeto.

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
- Algumas associações dão suporte a uma direção especial `inout`. Se você `inout`usar, apenas o **editor Avançado** está disponível através da guia **Integrar** no portal.

Quando você usa [atributos em uma biblioteca de classes](functions-dotnet-class-library.md) para configurar associações e gatilhos, a direção é fornecida em um construtor de atributo ou inferida do tipo de parâmetro.

## <a name="supported-bindings"></a>Associações com suporte

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Para obter informações sobre quais associações estão na visualização ou são aprovadas para o uso de produção, consulte [Idiomas com suporte](supported-languages.md).

## <a name="resources"></a>Recursos
- [Padrões e expressões de associação](./functions-bindings-expressions-patterns.md)
- [Usando o valor de retorno da função Azure](./functions-bindings-return-value.md)
- [Como registrar uma expressão vinculante](./functions-bindings-register.md)
- Teste:
  - [Estratégias para testar seu código no Azure Functions](functions-test-a-function.md)
  - [Executar manualmente uma função não disparada por HTTP](functions-manually-run-non-http.md)
- [Tratamento de erros de associação](./functions-bindings-errors.md)

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Registre extensões de vinculação de funções do Azure](./functions-bindings-register.md)
