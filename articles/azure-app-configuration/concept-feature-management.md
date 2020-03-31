---
title: Entenda o gerenciamento de recursos usando a configuração do aplicativo Azure
description: Ativar e desativar os recursos usando a configuração do aplicativo Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 8227810c154078fc8424b2cadd373394d07e9730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523723"
---
# <a name="feature-management-overview"></a>Visão geral do gerenciamento de recursos

Tradicionalmente, o envio de um novo recurso de aplicativo exige uma reimplantação completa do aplicativo em si. Testar um recurso muitas vezes requer várias implantações do aplicativo.  Cada implantação pode alterar o recurso ou expor o recurso a diferentes clientes para testes.  

O gerenciamento de recursos é uma prática moderna de desenvolvimento de software que separa a liberação do recurso da implantação do código e permite alterações rápidas na disponibilidade de recursos sob demanda. Ele usa uma técnica chamada *sinalizador de recursos* (também conhecida como *alternância de funcionalidades*, *opções de recurso* e assim por diante) para administrar o ciclo de vida de um recurso dinamicamente.

O gerenciamento de recursos ajuda os desenvolvedores a resolver os seguintes problemas:

* **Gerenciamento de ramificações de código**: Use sinalizadores de recursos para envolver novas funcionalidades de aplicativos atualmente em desenvolvimento. Essa funcionalidade é "oculta" por padrão. Você pode enviar o recurso com segurança, embora não esteja concluído, e ele permanecerá inativo na produção. Usando essa abordagem, chamada *implantação escura*, você pode liberar todo o código no final de cada ciclo de desenvolvimento. Você não precisa mais manter ramificações de código em vários ciclos de desenvolvimento porque um determinado recurso requer mais de um ciclo para ser concluído.
* **Teste na produção**: Use sinalizadores de recursos para conceder acesso antecipado a novas funcionalidades na produção. Por exemplo, você pode limitar o acesso a membros da equipe ou a testadores beta internos. Esses usuários experimentarão a experiência de produção de fidelidade total em vez de uma experiência simulada ou parcial em um ambiente de teste.
* **Flighting**: Use sinalizadores de recursos para implementar gradualmente novas funcionalidades para usuários finais. Você pode atingir uma pequena porcentagem da sua população de usuários primeiro e aumentar essa porcentagem gradualmente ao longo do tempo.
* **Interruptor de morte instantâneo**: Os sinalizadores de recursos fornecem uma rede de segurança inerente para liberar novas funcionalidades. Você pode ativar e desativar os recursos do aplicativo sem reimplantar nenhum código. Você obtém a capacidade de desabilitar rapidamente um recurso, se necessário, sem a necessidade de recompilar e reimplantar seu aplicativo.
* **Ativação seletiva**: Use sinalizadores de recursos para segmentar seus usuários e fornecer um conjunto específico de recursos para cada grupo. Você pode ter um recurso que funciona apenas em um determinado navegador da Web. Defina um sinalizador de recurso para que somente os usuários desse navegador possam ver e usar o recurso. Com essa abordagem, você pode expandir com facilidade a lista de navegadores compatíveis posteriormente sem precisar alterar códigos.

## <a name="basic-concepts"></a>Conceitos básicos

Veja os vários novos termos relacionados ao gerenciamento de recursos:

* **Sinalizador de recurso**: Um sinalizador de recurso é uma variável com um estado binário de *ligado* ou *desligado*. O sinalizador de recurso também tem um bloco de código associado. O estado do sinalizador de recurso aciona se o bloco de código é executado.
* **Gerenciador de**recursos : Um gerenciador de recursos é um pacote de aplicativos que lida com o ciclo de vida de todos os sinalizadores de recurso em um aplicativo. O gerenciador de recursos também oferece funcionalidades adicionais, incluindo cacheching de sinalizadores de recursos e atualização de seus estados.
* **Filtro**: Um filtro é uma regra para avaliar o estado de uma bandeira de recurso. Os filtros potenciais incluem grupos de usuários, tipos de dispositivos ou navegadores, localizações geográficas e janelas de tempo.

Uma implementação efetiva do gerenciamento de recursos consiste em pelo menos dois componentes trabalhando em conjunto:

* Um aplicativo que usa sinalizadores de recursos.
* Um repositório separado que armazena os sinalizadores de recursos e seus estados atuais.

## <a name="using-feature-flags-in-your-code"></a>Usando sinalizadores de recursos em seu código

O padrão básico para implementar sinalizadores de recurso em um aplicativo é simples. Um sinalizador de recurso é uma variável de estado booleano que controla uma declaração condicional em seu código:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Você pode definir `featureFlag` o valor de estáticamente.

```csharp
bool featureFlag = true;
```

Você pode avaliar o estado da bandeira com base em certas regras:

```csharp
bool featureFlag = isBetaUser();
```

Você pode estender o condicional para definir o comportamento do aplicativo para qualquer estado:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Repositório de sinalizadores de recurso

Para usar sinalizadores de recursos com eficiência, você precisará externalizar todos os sinalizadores de recursos usados em um aplicativo. Isso permite alterar os estados de bandeira do recurso sem modificar e reimplantar o aplicativo em si.

A configuração do aplicativo Azure fornece um repositório centralizado para sinalizadores de recursos. Você pode utilizá-la para definir diferentes tipos de sinalizadores de recurso e manipular seus estados de maneira rápida e segura. Você pode usar as bibliotecas da Configuração de Aplicativos para várias estruturas de linguagem de programação para acessar esses sinalizadores de recurso com facilidade em seu aplicativo.

O artigo [Usar sinalizadores de recursos em um aplicativo ASP.NET Core](./use-feature-flags-dotnet-core.md) mostra como o provedor da Configuração de Aplicativos do .NET Core e as bibliotecas do Gerenciamento de Recursos são usados juntos para implementação de sinalizadores de recursos para seu aplicativo Web ASP.NET.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar sinalizadores de recurso a um aplicativo Web ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
