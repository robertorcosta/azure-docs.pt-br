---
title: incluir arquivo
description: incluir arquivo
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: ba46d4dbe90b696398ed4c78383e127861c1a066
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050462"
---
1. Para criar um recurso de Serviço do Azure SignalR, primeiro, entre no [portal do Azure](https://portal.azure.com). No canto superior esquerdo da página, escolha **+ Criar um recurso**. Na caixa de texto **Pesquisar no Marketplace**, insira **Serviço do SignalR**.

2. Escolha **Serviço do SignalR** nos resultados e escolha **Criar**.

3. Na nova página de configurações do **SignalR**, adicione as seguintes configurações ao novo recurso do SignalR:

    | Nome | Valor recomendado | DESCRIÇÃO |
    | ---- | ----------------- | ----------- |
    | Nome do recurso | *testsignalr* | Insira um nome exclusivo do recurso a ser usado para o recurso do SignalR. O nome deve ser uma cadeia de caracteres com 1 a 63 caracteres, e deve conter somente números, letras e o hífen (`-`). O nome não pode começar nem terminar com o caractere hífen, e os caracteres hífen consecutivos não serão válidos.|
    | Subscription | Escolha sua assinatura |  Selecione a assinatura do Azure que você deseja usar para testar o SignalR. Se a conta tiver apenas uma assinatura, ela será selecionada automaticamente e a lista suspensa **Assinatura** não será exibida.|
    | Resource group | Criar um grupo de recursos chamado *SignalRTestResources*| Selecione ou crie um grupo de recursos para seu recurso do SignalR. Esse grupo é útil para organizar vários recursos que você pode querer excluir ao mesmo tempo, excluindo o grupo de recursos. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure](../articles/azure-resource-manager/management/overview.md). |
    | Location | *Leste dos EUA* | Use **Local** para especificar a localização geográfica em que o recurso do SignalR está hospedado. Para obter o melhor desempenho, recomendamos que você crie o recurso na mesma região que outros componentes do seu aplicativo. |
    | Tipo de preço | *Gratuito* | Atualmente, as opções **Gratuito** e **Standard** estão disponíveis. |
    | Fixar no painel | ✔ | Marque esta caixa para fixar o recurso no painel e facilitar a localização. |

4. Selecione **Criar**. A implantação pode levar alguns minutos para ser concluída.

5. Após a conclusão da implantação, escolha **Chaves** em **CONFIGURAÇÕES**. Copie a cadeia de conexão para a chave primária. Você usará essa cadeia de caracteres posteriormente para configurar seu aplicativo para usar o recurso do Serviço do Azure SignalR.

    A cadeia de conexão terá o seguinte formato:
    
    `Endpoint=<service_endpoint>;AccessKey=<access_key>;`
