---
title: Use filtros de recursos para habilitar um recurso para um subconjunto de usuários
titleSuffix: Azure App Configuration
description: Saiba como usar filtros de recursos para habilitar um recurso para um subconjunto de usuários
ms.service: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 181c97615985283011834dcf9145810b1563fb4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057007"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>Use filtros de recursos para habilitar um recurso para um subconjunto de usuários

Os sinalizadores de recursos permitem ativar ou desativar a funcionalidade em seu aplicativo. Uma simples bandeira de recurso está ligado ou desligado. O aplicativo sempre se comporta da mesma maneira. Por exemplo, você pode lançar um novo recurso atrás de um sinalizador de recurso. Quando o sinalizador de recurso está ativado, todos os usuários verão o novo recurso. Desativar o sinalizador de recurso oculta o novo recurso.

Em contraste, um _sinalizador de recurso condicional_ permite que o sinalizador de recurso seja ativado ou desativado dinamicamente. O aplicativo pode se comportar de forma diferente, dependendo dos critérios de bandeira de recurso. Suponha que você queira mostrar seu novo recurso para um pequeno subconjunto de usuários no início. Um sinalizador de recurso condicional permite que você habilite o sinalizador de recurso para alguns usuários enquanto o desativa para outros. _Os filtros de recurso_ determinam o estado do sinalizador de recurso cada vez que é avaliado.

A `Microsoft.FeatureManagement` biblioteca inclui dois filtros de recursos:

- `PercentageFilter`habilita o sinalizador de recurso com base em uma porcentagem.
- `TimeWindowFilter`habilita o sinalizador de recurso durante uma janela de tempo especificada.

Você também pode criar seu próprio filtro de recursos que implementa a [interface Microsoft.FeatureManagement.IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Registrando um filtro de recursos

Você registra um filtro `AddFeatureFilter` de recurso chamando o método, especificando o nome do filtro de recurso. Por exemplo, os seguintes `PercentageFilter`registros de código:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Configuração de um filtro de recursos na configuração do aplicativo Azure

Alguns filtros de recursos têm configurações adicionais. Por exemplo, `PercentageFilter` ativa um recurso com base em uma porcentagem. Ele tem uma configuração definindo a porcentagem a ser usada.

Você pode configurar essas configurações para sinalizadores de recursos definidos na configuração do aplicativo Azure. Por exemplo, siga estas etapas a serem usados `PercentageFilter` para ativar o sinalizador de recursos para 50% das solicitações em um aplicativo web:

1. Siga as instruções em [Quickstart: Adicione sinalizadores de recursos a um aplicativo ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) para criar um aplicativo web com um sinalizador de recurso.

1. No portal Azure, vá até sua loja de configuração e clique **em Gerenciador de recursos**.

1. Clique no menu de contexto para o sinalizador de recurso *Beta* que você criou no quickstart. Clique em **Editar**.

    > [!div class="mx-imgBorder"]
    > ![Editar sinalizador de recurso Beta](./media/edit-beta-feature-flag.png)

1. Na tela **Editar,** selecione o botão **No** rádio se ele ainda não estiver selecionado. Em seguida, clique no botão **Adicionar filtro.** (O rótulo do botão de rádio **On** será alterado para ler **Conditional**.)

1. No campo **Chave,** digite *Microsoft.Percentagem*.

    > [!div class="mx-imgBorder"]
    > ![Adicionar filtro de recurso](./media/feature-flag-add-filter.png)

1. Clique no menu de contexto ao lado da tecla filtro de recursos. Clique **em Editar parâmetros**.

    > [!div class="mx-imgBorder"]
    > ![Editar parâmetros do filtro de recursos](./media/feature-flag-edit-filter-parameters.png)

1. Passar o tempo no **cabeçalho Nome** para que as caixas de texto apareçam na grade. Digite **um nome** de *valor* e um **valor** de 50. O **campo Valor** indica a porcentagem de solicitações para a qual ativar o filtro de recurso.

    > [!div class="mx-imgBorder"]
    > ![Definir parâmetros do filtro de recursos](./media/feature-flag-set-filter-parameters.png)

1. Clique **em Aplicar** para retornar à tela de sinalizador de recurso **Editar.** Em seguida, clique **em Aplicar** novamente para salvar as configurações do sinalizador de recurso.

1. O **estado** da bandeira de recurso agora aparece como *Conditional*. Este estado indica que o sinalizador de recurso será ativado ou desativado por solicitação, com base nos critérios impostos pelo filtro de recurso.

    > [!div class="mx-imgBorder"]
    > ![Bandeira de recurso condicional](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Filtros de recursos em ação

Para ver os efeitos desse sinalizador de recurso, inicie o aplicativo e aperte o botão **Atualizar** no seu navegador várias vezes. Você verá que o item *Beta* aparece na barra de ferramentas cerca de 50% do tempo. Está escondido o resto do tempo, `PercentageFilter` porque desativa o recurso *Beta* para um subconjunto de solicitações. O vídeo a seguir mostra esse comportamento em ação.

> [!div class="mx-imgBorder"]
> ![PorcentagemFiltro em ação](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Visão geral do gerenciamento de recursos](./concept-feature-management.md)
