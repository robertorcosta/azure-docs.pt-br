---
title: Relatório de uso e insights | Microsoft Docs
description: Introdução ao relatório de uso e insights no portal do Diretório Ativo do Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b3db86137207ae726c7befc393f62590fd1456d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008256"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Relatório de uso e insights no portal do Diretório Ativo do Azure

Com o relatório de uso e insights, você pode obter uma visão centrada no aplicativo de seus dados de login. Você pode encontrar respostas para as seguintes perguntas:

*   Quais são as principais aplicações usadas na minha organização?
*   Quais aplicativos têm mais logins com falha? 
*   Quais são os principais erros de login de cada aplicativo?

## <a name="prerequisites"></a>Pré-requisitos 

Para acessar os dados do relatório de uso e insights, você precisa:

* Um locatário do Azure AD
* Uma licença Azure AD premium (P1/P2) para visualizar os dados de login
* Um usuário nas funções de administrador global, administrador de segurança, leitor de segurança ou leitor de relatórios. Além disso, qualquer usuário (não-admins) pode acessar seus próprios logins. 

## <a name="access-the-usage-and-insights-report"></a>Acesse o relatório de uso e insights

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Selecione o diretório certo e selecione **O Diretório Ativo do Azure** e escolha aplicativos **Corporativos**.
3. Na seção **Atividade,** **selecione Use & insights** para abrir o relatório. 

![Relatório de uso e insights](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Use o relatório

O relatório de uso e insights mostra a lista de aplicativos com um ou mais sinais na tentativa e permite classificar pelo número de logins bem-sucedidos, logins com falha e a taxa de sucesso.

Clicar em carregar mais na parte inferior da lista permite que você visualize aplicativos adicionais na página. Você pode selecionar o intervalo de datas para visualizar todos os aplicativos que foram usados dentro do intervalo.

Você também pode definir o foco em uma aplicação específica. Selecione **exibir atividade de login** para ver o login em atividade ao longo do tempo para o aplicativo, bem como os principais erros.  

Quando você seleciona um dia no gráfico de uso do aplicativo, você recebe uma lista detalhada das atividades de login para o aplicativo.  

![Relatório de uso e insights](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Próximas etapas

* [Relatório de entradas](concept-sign-ins.md)