---
title: Visão geral do Azure Functions
description: Compreenda como usar o Azure Functions para otimizar cargas de trabalho assíncronas em minutos.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621015"
---
# <a name="an-introduction-to-azure-functions"></a>Uma introdução ao Azure Functions

O Azure Functions permite que você execute pequenas partes de código (chamadas de "funções") sem se preocupar com a infraestrutura do aplicativo. Com o Azure Functions, a infraestrutura de nuvem fornece todos os servidores atualizados de que você precisa para manter seu aplicativo em execução em escala.

Uma função é "disparada" por um tipo de evento específico. Os [gatilhos com suporte](./functions-triggers-bindings.md) incluem responder a alterações nos dados, responder a mensagens, executar em um agendamento ou como resultado de uma solicitação HTTP.

Embora você sempre possa codificar diretamente em uma infinidade de serviços, a integração com outros serviços é simplificada usando associações. As associações oferecem [acesso declarativo a uma ampla variedade de serviços do Azure e de terceiros](./functions-triggers-bindings.md).

## <a name="features"></a>Recursos

Alguns dos recursos principais do Azure Functions incluem:

- **Aplicativos sem servidor**: o Functions permite desenvolver aplicativos [sem servidor](https://azure.microsoft.com/solutions/serverless/) no Microsoft Azure.

- **Opção de linguagem**: escreva funções usando sua escolha entre [C#, Java, JavaScript, Python e PowerShell](supported-languages.md).

- **Modelo de preço de pagamento conforme o uso**: pague somente pelo tempo gasto na execução de seu código. Veja a opção de plano de hospedagem de consumo na [seção de preços](#pricing).  

- **Traga as próprias dependências**: o Functions é compatível com NuGet e NPM e, permitindo que você acesse suas bibliotecas favoritas.

- **Segurança Integrada**: proteja funções disparadas por HTTP com provedores de OAuth como Azure Active Directory, Facebook, Google, Twitter e conta Microsoft.

- **Integração simplificada**: integre-se facilmente às ofertas de serviços e SaaS (software como serviço) do Azure.

- **Desenvolvimento flexível**: configure a integração contínua e implante seu código por meio do [GitHub](../app-service/scripts/cli-continuous-deployment-github.md), do [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md) e de outras [ferramentas de desenvolvimento com suporte](../app-service/deploy-local-git.md).

- **Arquitetura sem servidor com estado**: orquestre aplicativos sem servidor com o [Durable Functions](durable/durable-functions-overview.md).

- **Software livre**: o runtime do Functions é de software livre e [está disponível no GitHub](https://github.com/azure/azure-webjobs-sdk-script).

## <a name="what-can-i-do-with-functions"></a>O que posso fazer com o Functions?

O Functions é uma ótima solução para processamento de dados em massa, integração de sistemas, trabalho com a IoT (Internet das coisas) e criação de APIs e microsserviços simples.

Uma série de modelos está disponível para você começar a usar os principais cenários, incluindo:

- **HTTP**: execute código com base em [solicitações HTTP](functions-create-first-azure-function.md)

- **Timer**: agende código para [ser executado em horários predefinidos](./functions-create-scheduled-function.md)

- **Azure Cosmos DB**: processe [documentos do Azure Cosmos DB novos e modificados](./functions-create-cosmos-db-triggered-function.md)

- **Armazenamento de Blobs**: processe [blobs do Armazenamento do Azure novos e modificados](./functions-create-storage-blob-triggered-function.md)

- **Armazenamento de Filas**: responda a [mensagens da fila do Armazenamento do Azure](./functions-create-storage-queue-triggered-function.md)

- **Grade de Eventos**: responda a [eventos da Grade de Eventos do Azure por meio de assinaturas e filtros](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Hub de Eventos**: responda a [altos volumes de eventos do Hub de Eventos do Azure](./functions-bindings-event-hubs.md)

- **Fila do Barramento de Serviço**: conecte-se a outros serviços locais ou do Azure [respondendo mensagens da fila do Barramento de Serviço](./functions-bindings-service-bus.md)

- **Tópico do Barramento de Serviço**: conecte outros serviços locais ou do Azure [respondendo mensagens do tópico do Barramento de Serviço](./functions-bindings-service-bus.md)

## <a name="pricing"></a>Quanto custa o Functions?

O Azure Functions tem três tipos de planos de preço. Escolha a opção que melhor atende às suas necessidades:

- **Plano de consumo**: o Azure fornece todos os recursos computacionais necessários. Você não precisa se preocupar com o gerenciamento de recursos e paga apenas pelo tempo de execução do seu código.

- **Plano Premium**: você especifica um número de instâncias pré-configuradas que estão sempre online e prontas para responder imediatamente. Quando sua função é executada, o Azure fornece todos os recursos computacionais adicionais necessários. Você paga pelas instâncias pré-configuradas executadas continuamente e todas as instâncias adicionais que você usa conforme o Azure dimensiona seu aplicativo para dentro e para fora.

- **Plano do Serviço de Aplicativo**: execute suas funções da mesma forma que os aplicativos Web. Se você usar o Serviço de Aplicativo para outros aplicativos, suas funções poderão ser executadas no mesmo plano sem custo adicional.

Para obter mais informações sobre planos de hospedagem, consulte [Comparação de planos de hospedagem do Azure Functions](functions-scale.md). Os detalhes de preços completos estão disponíveis na [página Preço do Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Próximas etapas

- [Criar sua primeira Função do Azure](functions-create-first-function-vs-code.md)  
  Comece a usar o [Visual Studio Code](functions-create-first-function-vs-code.md) e a [linha de comando](functions-create-first-azure-function-azure-cli.md) ou use o [portal do Azure](functions-create-first-azure-function.md).

- [Referência do desenvolvedor do Azure Functions](functions-reference.md)  
  Fornece informações mais técnicas sobre o Azure Functions runtime e uma referência para funções de codificação e definição de associações e gatilhos.

- [Como escalar o Azure Functions](functions-scale.md)  
  Discute os planos de serviço disponíveis com o Azure Functions, incluindo o plano de hospedagem de consumo e como escolher o plano certo.

- [Saiba mais sobre o Serviço de Aplicativo do Azure](../app-service/overview.md)  
  O Azure Functions aproveita o Serviço de Aplicativo do Azure para a funcionalidade básica como implantações, variáveis de ambiente e diagnóstico.
