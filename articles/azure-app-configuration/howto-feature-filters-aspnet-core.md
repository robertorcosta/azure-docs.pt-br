---
title: Usar filtros de recurso para habilitar um recurso para um subconjunto de usuários
titleSuffix: Azure App Configuration
description: Saiba como usar filtros de recurso para habilitar um recurso para um subconjunto de usuários
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 5b2eb942581f6e4163012b0f767d04c02689bb7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88206762"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>Usar filtros de recurso para habilitar um recurso para um subconjunto de usuários

Os sinalizadores de recurso permitem ativar ou desativar a funcionalidade em seu aplicativo. Um sinalizador de recurso simples está ativado ou desativado. O aplicativo sempre se comporta da mesma maneira. Por exemplo, você pode distribuir um novo recurso por trás de um sinalizador de recurso. Quando o sinalizador de recurso está habilitado, todos os usuários veem o novo recurso. Desabilitar o sinalizador de recurso oculta o novo recurso.

Por outro lado, um _sinalizador de recurso condicional_ permite que o sinalizador de recurso seja habilitado ou desabilitado dinamicamente. O aplicativo pode se comportar de forma diferente, dependendo dos critérios do sinalizador de recurso. Suponha que você deseja mostrar o novo recurso a um pequeno subconjunto de usuários a princípio. Um sinalizador de recurso condicional permite habilitar o sinalizador de recurso para alguns usuários ao desabilitá-lo para outros. Os _filtros de recurso_ determinam o estado do sinalizador de recurso cada vez que ele é avaliado.

A `Microsoft.FeatureManagement` biblioteca inclui dois filtros de recursos:

- `PercentageFilter` habilita o sinalizador de recurso com base em uma porcentagem.
- `TimeWindowFilter` habilita o sinalizador de recurso durante uma janela de tempo especificada.

Você também pode criar seu próprio filtro de recursos que implementa a [interface Microsoft. FeatureManagement. IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Registrando um filtro de recurso

Você registra um filtro de recurso chamando o `AddFeatureFilter` método, especificando o nome do filtro de recursos. Por exemplo, o código a seguir registra `PercentageFilter` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Configurando um filtro de recursos no Azure App configuração

Alguns filtros de recursos têm configurações adicionais. Por exemplo, `PercentageFilter` ativa um recurso com base em uma porcentagem. Ele tem uma configuração que define a porcentagem a ser usada.

Você pode definir essas configurações para sinalizadores de recursos definidos na configuração do Azure App. Por exemplo, siga estas etapas a serem usadas para `PercentageFilter` habilitar o sinalizador de recurso para 50% das solicitações para um aplicativo Web:

1. Siga as instruções em [início rápido: Adicionar sinalizadores de recurso a um aplicativo ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) para criar um aplicativo Web com um sinalizador de recurso.

1. Na portal do Azure, vá para o repositório de configurações e clique em **Gerenciador de recursos**.

1. Clique no menu de contexto do sinalizador de recurso *beta* que você criou no guia de início rápido. Clique em **Editar**.

    > [!div class="mx-imgBorder"]
    > ![Editar sinalizador de recurso beta](./media/edit-beta-feature-flag.png)

1. Na tela de **edição** , selecione o botão **de opção on** se ele ainda não estiver selecionado. Em seguida, clique no botão **Adicionar filtro** . (O rótulo do botão **de opção em** será alterado para leitura **condicional**.)

1. No campo **chave** , insira *Microsoft. Percentage*.

    > [!div class="mx-imgBorder"]
    > ![Adicionar filtro de recurso](./media/feature-flag-add-filter.png)

1. Clique no menu de contexto ao lado da chave de filtro de recurso. Clique em **Editar parâmetros**.

    > [!div class="mx-imgBorder"]
    > ![Editar parâmetros de filtro de recursos](./media/feature-flag-edit-filter-parameters.png)

1. Focalize o cabeçalho **nome** para que as caixas de texto apareçam na grade. Insira um **nome** de *valor* e um **valor** de 50. O campo **valor** indica a porcentagem de solicitações para as quais habilitar o filtro de recursos.

    > [!div class="mx-imgBorder"]
    > ![Definir parâmetros de filtro de recurso](./media/feature-flag-set-filter-parameters.png)

1. Clique em **aplicar** para retornar à tela **Editar sinalizador de recurso** . Em seguida, clique em **aplicar** novamente para salvar as configurações do sinalizador de recurso.

1. O **estado** do sinalizador de recurso agora aparece como *condicional*. Esse estado indica que o sinalizador de recurso será habilitado ou desabilitado em uma base por solicitação, com base nos critérios impostos pelo filtro de recursos.

    > [!div class="mx-imgBorder"]
    > ![Sinalizador de recurso condicional](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Filtros de recurso em ação

Para ver os efeitos desse sinalizador de recurso, inicie o aplicativo e pressione o botão **Atualizar** no navegador várias vezes. Você verá que o item *beta* aparece na barra de ferramentas cerca de 50% do tempo. Ele fica oculto no restante do tempo, porque o `PercentageFilter` desativa o recurso *beta* para um subconjunto de solicitações. O vídeo a seguir mostra esse comportamento em ação.

> [!div class="mx-imgBorder"]
> ![PercentageFilter em ação](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Visão geral do gerenciamento de recursos](./concept-feature-management.md)
