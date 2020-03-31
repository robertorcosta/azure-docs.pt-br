---
title: Teste de desempenho e carga com o Azure Application Insights | Microsoft Docs
description: Configure testes de desempenho e carga com o Azure Application Insights
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669855"
---
# <a name="performance-testing"></a>Testes de desempenho

> [!NOTE]
> O serviço de teste de carga baseado em nuvem foi preterido. Mais informações sobre a depreciação, a disponibilidade do serviço e serviços alternativos podem ser encontradas [aqui.](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops)

O Application Insights permite que você gere testes de carga para seus sites. Como [testes de disponibilidade,](monitor-web-app-availability.md)você pode enviar solicitações básicas ou [solicitações em várias etapas](availability-multistep.md) de agentes de teste do Azure em todo o mundo. Testes de desempenho permitem simular até 20.000 usuários simultâneos por até 60 minutos.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Para criar um teste de desempenho, primeiro você precisa criar um recurso application Insights. Se você já criou um recurso, prossiga para a próxima seção.

No portal Do Zure, selecione **Criar um recurso** > **Developer Tools** > **Application Insights** e criar um recurso Application Insights.

## <a name="configure-performance-testing"></a>Configurar testes de desempenho

Se esta é a primeira vez que cria o teste de desempenho, **selecione Azure** DevOps para ser a fonte dos seus testes de desempenho.

Em **Configure,** vá para **Testes de Desempenho** e clique em **Novo** para criar um teste.

![Preencha pelo menos o URL do seu site](./media/performance-testing/new-performance-test.png)

Para criar um teste básico de desempenho, selecione um tipo de teste de **teste** manual e preencha as configurações desejadas para o teste.

|Configuração| Valor máximo
|----------|------------|
| Carga do usuário | 20,000 |
| Duração (Minutos)  | 60 |  

Depois que seu teste for criado, clique em **Executar teste**.

Uma vez que o teste esteja concluído, você verá resultados semelhantes aos resultados abaixo:

![Resultados do teste](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Configurar o teste web do Visual Studio

Os recursos avançados de teste de desempenho do Application Insights são construídos em cima dos projetos de teste de desempenho e carga do Visual Studio.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Próximas etapas

* [Testes na Web com diversas etapas](availability-multistep.md)
* [Testes de ping de URL](monitor-web-app-availability.md)
