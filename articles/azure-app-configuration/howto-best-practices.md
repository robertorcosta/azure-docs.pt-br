---
title: Práticas recomendadas de configuração de aplicativos do Azure | Microsoft Docs
description: Saiba como usar melhor a configuração do aplicativo Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: df56f53b64a35737700529b80c004efeb31eaabc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348669"
---
# <a name="azure-app-configuration-best-practices"></a>Práticas recomendadas de configuração do aplicativo Azure

Este artigo discute padrões comuns e práticas recomendadas quando você está usando a configuração do aplicativo Azure.

## <a name="key-groupings"></a>Agrupamentos-chave

A configuração do aplicativo oferece duas opções para organizar chaves:

* Principais prefixos
* Rótulos

Você pode usar uma ou ambas as opções para agrupar suas chaves.

*Os prefixos-chave* são as partes iniciais das chaves. Você pode logicamente agrupar um conjunto de chaves usando o mesmo prefixo em seus nomes. Os prefixos podem conter vários componentes conectados `/`por um delimitador, como, semelhante a um caminho de URL, para formar um namespace. Tais hierarquias são úteis quando você armazena chaves para muitos aplicativos, serviços de componentes e ambientes em uma loja de configuração de aplicativos.

Uma coisa importante a ter em mente é que as chaves são o que o código do aplicativo faz referência para recuperar os valores das configurações correspondentes. As chaves não devem mudar, ou então você terá que modificar seu código cada vez que isso acontecer.

*Os rótulos* são um atributo nas teclas. Eles são usados para criar variantes de uma chave. Por exemplo, você pode atribuir rótulos a várias versões de uma chave. Uma versão pode ser uma iteração, um ambiente ou alguma outra informação contextual. Seu aplicativo pode solicitar um conjunto totalmente diferente de valores-chave especificando outro rótulo. Como resultado, todas as referências-chave permanecem inalteradas em seu código.

## <a name="key-value-compositions"></a>Composições de valor-chave

A Configuração do aplicativo trata todas as chaves armazenadas com ele como entidades independentes. A configuração do aplicativo não tenta inferir qualquer relação entre chaves ou herdar valores-chave com base em sua hierarquia. No entanto, você pode agregar vários conjuntos de chaves usando etiquetas acopladas ao empilhamento de configuração adequado no código do aplicativo.

Vamos examinar um exemplo. Suponha que você tenha uma configuração chamada **Asset1**, cujo valor pode variar de acordo com o ambiente de desenvolvimento. Você cria uma chave chamada "Asset1" com um rótulo vazio e um rótulo chamado "Desenvolvimento". No primeiro rótulo, você coloca o valor padrão para **O Asset1**, e você coloca um valor específico para "Desenvolvimento" no último.

Em seu código, primeiro você recupera os valores-chave sem rótulos e, em seguida, recupera o mesmo conjunto de valores-chave uma segunda vez com o rótulo "Desenvolvimento". Quando você recupera os valores na segunda vez, os valores anteriores das chaves são substituídos. O sistema de configuração .NET Core permite que você "empilhe" vários conjuntos de dados de configuração em cima um do outro. Se existir uma chave em mais de um conjunto, o último conjunto que a contém será usado. Com uma estrutura de programação moderna, como o .NET Core, você recebe este recurso de empilhamento gratuitamente se você usar um provedor de configuração nativo para acessar a configuração do aplicativo. O seguinte trecho de código mostra como você pode implementar o empilhamento em um aplicativo .NET Core:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[O uso de etiquetas para habilitar diferentes configurações para diferentes ambientes](./howto-labels-aspnet-core.md) fornece um exemplo completo.

## <a name="app-configuration-bootstrap"></a>Bootstrap de configuração de aplicativo

Para acessar uma loja de configuração de aplicativos, você pode usar sua string de conexão, que está disponível no portal Azure. Como as seqüências de conexão contêm informações de credenciais, são consideradas segredos. Esses segredos precisam ser armazenados no Azure Key Vault, e seu código deve autenticar-se no Key Vault para recuperá-los.

Uma opção melhor é usar o recurso de identidades gerenciadas no Azure Active Directory. Com identidades gerenciadas, você precisa apenas da URL de ponto final de configuração do aplicativo para acessar bootstrap à sua loja de configuração de aplicativos. Você pode incorporar a URL no código do aplicativo (por exemplo, no arquivo *appsettings.json).* Consulte [Integrar com as identidades gerenciadas pelo Azure](howto-integrate-azure-managed-service-identity.md) para obter detalhes.

## <a name="app-or-function-access-to-app-configuration"></a>Acesso de aplicativo ou função à configuração do aplicativo

Você pode fornecer acesso à configuração do aplicativo para aplicativos ou funções da Web usando qualquer um dos seguintes métodos:

* Através do portal Azure, digite a seqüência de conexões na sua loja de configuração de aplicativos nas configurações de aplicativo do App Service.
* Armazene a seqüência de conexões na sua loja de configuração de aplicativos no Key Vault e faça [referência a ela no App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Use identidades gerenciadas pelo Azure para acessar a loja de configuração de aplicativos. Para obter mais informações, consulte [Integrar com as identidades gerenciadas pelo Azure](howto-integrate-azure-managed-service-identity.md).
* Empurrar a configuração da configuração do aplicativo para o serviço do aplicativo. A Configuração do Aplicativo fornece uma função de exportação (no portal Azure e no Azure CLI) que envia dados diretamente para o App Service. Com este método, você não precisa alterar o código do aplicativo.

## <a name="reduce-requests-made-to-app-configuration"></a>Reduzir as solicitações feitas à configuração do aplicativo

Solicitações excessivas para configuração de aplicativos podem resultar em estrangulamento ou sobrecarga de taxas. Para reduzir o número de solicitações feitas:

* Aumente o tempo limite de atualização, especialmente se os valores de configuração não mudarem com freqüência. Especifique um novo [ `SetCacheExpiration` ](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration)tempo de atualização usando o método .

* Observe uma única *tecla sentinela,* em vez de observar chaves individuais. Atualize todas as configurações somente se a tecla sentinela mudar. Veja [Usar configuração dinâmica em um aplicativo ASP.NET Core](enable-dynamic-configuration-aspnet-core.md) para um exemplo.

* Use o Azure Event Grid para receber notificações quando a configuração mudar, em vez de votar constantemente em qualquer alteração. Consulte [os eventos de configuração do aplicativo Route Azure em um ponto final da Web](./howto-app-configuration-event.md) para obter mais informações

## <a name="importing-configuration-data-into-app-configuration"></a>Importando dados de configuração para a configuração do aplicativo

A configuração do aplicativo oferece a opção de [importar](https://aka.ms/azconfig-importexport1) em massa suas configurações de seus arquivos de configuração atuais usando o portal Azure ou cli. Você também pode usar as mesmas opções para exportar valores da Configuração do aplicativo, por exemplo, entre lojas relacionadas. Se você quiser configurar uma sincronização contínua com o seu repo do GitHub, você pode usar o nosso [GitHub Action](https://aka.ms/azconfig-gha2) para que você possa continuar usando suas práticas de controle de origem existentes enquanto recebe os benefícios da Configuração do Aplicativo.

## <a name="next-steps"></a>Próximas etapas

* [Chaves e valores](./concept-key-value.md)
