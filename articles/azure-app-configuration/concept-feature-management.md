---
title: Gerenciamento de recursos de configuração do Azure App
description: Uma visão geral de como a Configuração de Aplicativos do Azure pode ser usada para ativar e desligar recursos de aplicativo sob demanda.
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 48aebfe1ba6af348e98e5600075f3a9e9dce1a8e
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495761"
---
# <a name="feature-management-overview"></a>Visão geral do gerenciamento de recursos

Tradicionalmente, o envio de um novo recurso de aplicativo exige uma reimplantação completa do aplicativo em si. Para testar um recurso, provavelmente, você precisará implantar seu aplicativo muitas vezes para controlar quando o recurso se tornará visível ou quando alguém puder vê-lo.

O gerenciamento de recursos é uma prática moderna de desenvolvimento de software que separa a liberação do recurso da implantação do código e permite alterações rápidas na disponibilidade de recursos sob demanda. Ele usa uma técnica chamada *sinalizador de recursos* (também conhecida como *alternância de funcionalidades*, *opções de recurso* e assim por diante) para administrar o ciclo de vida de um recurso dinamicamente.

O gerenciamento de recursos ajuda os desenvolvedores a lidar com os seguintes problemas:

* **Gerenciamento de ramificação de código**: os sinalizadores de recursos são usados frequentemente para encapsular a nova funcionalidade do aplicativo que está em desenvolvimento. Essa funcionalidade é "oculta" por padrão. Você pode enviar o recurso com segurança, embora não esteja concluído, e ele permanecerá inativo na produção. Com essa abordagem chamada *implantação escura*, você pode liberar todo o código no final de cada ciclo de desenvolvimento. Você não precisa manter nenhum branch de código em vários ciclos porque um recurso leva mais de um ciclo para ser concluído.
* **Teste em produção**: você pode usar sinalizadores de recurso para conceder acesso antecipado a novas funcionalidades na produção. Por exemplo, limite esse acesso apenas aos membros da equipe ou aos testadores beta internos. Esses usuários obterão a experiência de produção com fidelidade total, em vez de uma experiência simulada ou parcial em um ambiente de teste.
* **Comprovando**: você também pode usar sinalizadores de recurso para distribuir incrementalmente novas funcionalidades para os usuários finais. Direcione um pequeno percentual da população de usuários primeiro e aumente esse percentual gradualmente com o tempo, depois de ganhar mais confiança na implementação.
* **Interruptor de eliminação instantânea**: os sinalizadores de recursos fornecem uma rede de segurança inerente para liberar novas funcionalidades. Com eles, você pode prontamente ativar e desativar recursos do aplicativo. Você obtém a capacidade de desabilitar rapidamente um recurso, se necessário, sem a necessidade de recompilar e reimplantar seu aplicativo.
* **Ativação seletiva**: embora a maioria dos sinalizadores de recurso existam somente até que suas funcionalidades associadas tenham sido liberadas com êxito, alguns podem durar muito tempo. Você pode usar os sinalizadores de recurso como uma maneira de segmentar os usuários e fornecer um conjunto específico de recursos para cada grupo. Por exemplo, você pode ter um recurso que funciona apenas em determinado navegador da Web. Defina um sinalizador de recurso para que somente os usuários desse navegador possam ver e usar o recurso. Com essa abordagem, você pode expandir com facilidade a lista de navegadores compatíveis posteriormente sem precisar alterar códigos.

## <a name="basic-concepts"></a>Conceitos básicos

Veja os vários novos termos relacionados ao gerenciamento de recursos:

* **Sinalizador de recurso**: um sinalizador de recurso é uma variável com um estado binário de *ativado* ou *desativado*. O sinalizador de recurso também tem um bloco de código associado. O estado do sinalizador de recursos decide se o bloco de código será executado ou não.
* **Gerenciador de recursos**: um Gerenciador de recursos é um pacote de aplicativos que manipula o ciclo de vida de todos os sinalizadores de recurso em um aplicativo. Normalmente, o gerenciador de recursos fornece funcionalidades adicionais, como cache de sinalizadores de recursos e atualização de seus estados.
* **Filtro**: um filtro é uma regra para avaliar o estado de um sinalizador de recurso. Um grupo de usuários, um tipo de dispositivo ou navegador, uma geolocalização e uma janela de tempo são todos exemplos do que pode representar um filtro.

Uma implementação efetiva do gerenciamento de recursos consiste em pelo menos dois componentes trabalhando em conjunto:

* Um aplicativo que usa sinalizadores de recursos.
* Um repositório separado que armazena os sinalizadores de recursos e seus estados atuais.

A interação desses componentes está ilustrada nos exemplos a seguir.

## <a name="feature-flag-usage-in-code"></a>Uso de sinalizador de recurso no código

O padrão básico para implementar sinalizadores de recurso em um aplicativo é simples. Você pode pensar em um sinalizador de recursos como uma variável de estado booliana usada com uma instrução condicional `if` no código:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Nesse caso, se `featureFlag` é definido como `True`, o bloco de código embutido é executado; caso contrário, ele é ignorado. Você pode definir o valor de `featureFlag` estaticamente, como no exemplo de código a seguir:

```csharp
bool featureFlag = true;
```

Você também pode avaliar o estado do sinalizador com base em determinadas regras:

```csharp
bool featureFlag = isBetaUser();
```

Um padrão de sinalizador de recurso um pouco mais complicado também inclui uma instrução `else`:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

No entanto, esse comportamento pode ser reescrito no padrão básico. O artigo [Usar sinalizadores de recurso em um aplicativo ASP.NET Core](./use-feature-flags-dotnet-core.md) mostra a vantagem de padronização em um padrão de código simples. Por exemplo:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Repositório de sinalizadores de recurso

Para usar sinalizadores de recursos com eficiência, você precisará externalizar todos os sinalizadores de recursos usados em um aplicativo. Essa abordagem permite que você altere os estados do sinalizador de recurso sem modificar e reimplantar o aplicativo em si.

A Configuração de Aplicativos do Azure foi projetada para ser um repositório centralizado de sinalizadores de recurso. Você pode utilizá-la para definir diferentes tipos de sinalizadores de recurso e manipular seus estados de maneira rápida e segura. Você pode usar as bibliotecas da Configuração de Aplicativos para várias estruturas de linguagem de programação para acessar esses sinalizadores de recurso com facilidade em seu aplicativo.

O artigo [Usar sinalizadores de recursos em um aplicativo ASP.NET Core](./use-feature-flags-dotnet-core.md) mostra como o provedor da Configuração de Aplicativos do .NET Core e as bibliotecas do Gerenciamento de Recursos são usados juntos para implementação de sinalizadores de recursos para seu aplicativo Web ASP.NET.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Adicionar sinalizadores de recurso a um aplicativo Web ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
