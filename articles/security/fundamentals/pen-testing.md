---
title: Teste de Caneta | Microsoft Docs
description: O artigo fornece uma visão geral do processo (teste) dos testes de penetração e de como executar o teste de caneta em seus aplicativos em execução na infraestrutura do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: terrylan
ms.openlocfilehash: db6e25b6304ee9ac41ca95d5a3a6eac0e91eb41b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287774"
---
# <a name="penetration-testing"></a>Teste de penetração
Um dos benefícios do uso do Azure para implantação e testes de aplicativos é que você pode criar rapidamente ambientes. Você não precisa se preocupar sobre requisição, aquisição e "posicionamento no rack e empilhamento" do seu próprio hardware local.

Isso é ótimo, mas você ainda precisará certificar-se de executar a inspeção de segurança normal. Uma das coisas que você provavelmente quer fazer é o teste de penetração dos aplicativos implantados no Azure.

Talvez você já saiba que a Microsoft realiza [teste de penetração do nosso ambiente do Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Isso ajuda a gerar melhorias no Azure.

Não fazemos o teste de penetração de seu aplicativo para você, mas entendemos que você desejará e precisará executar testes em seus próprios aplicativos. Isso é bom, porque ao aprimorar a segurança de seus aplicativos, você ajuda a tornar todo o ecossistema do Azure mais seguro.

A partir de 15 de junho de 2017, a Microsoft não exige mais aprovação prévia para conduzir um teste de penetração nos recursos do Azure. Os clientes que desejam documentar formalmente futuros testes de penetração no Microsoft Azure são incentivados a preencher o [formulário Notificação de teste de penetração do Azure Service](https://portal.msrc.microsoft.com/en-us/engage/pentest). Esse processo só está relacionado ao Microsoft Azure e não se aplica a outro serviço do Microsoft Cloud.

>[!IMPORTANT]
>Embora a notificação da Microsoft da caneta as atividades de teste não é mais necessário clientes ainda devem estar de acordo com o [Microsoft nuvem unificado penetração teste regras](https://technet.microsoft.com/mt784683).

Os testes padrão que você pode executar incluem:

* Testes em seus pontos de extremidade para revelar as [10 maiores vulnerabilidades do OWASP (Projeto de Segurança de Aplicativo Web Aberto)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Teste de fuzzing](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) de seus pontos de extremidade
* [Exame de portas](https://en.wikipedia.org/wiki/Port_scanner) de seus pontos de extremidade

## <a name="next-steps"></a>Próximas etapas

- Se você quiser documentar formalmente um teste de penetração futuro em seus aplicativos hospedados na Microsoft Azure, vá até as [regras de teste de penetração do Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) e preencha o formulário de notificação de teste.
