---
title: Perguntas frequentes sobre aceleradores de solução do IoT - Azure | Microsoft Docs
description: Este artigo responde às perguntas frequentes sobre os aceleradores de solução de IoT. Ele inclui links para os repositórios do GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 7a2b167f90b4ec79f5fa515ded1c676ca7085e72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75647706"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Perguntas frequentes sobre os aceleradores de solução do IoT

Consulte também as [perguntas frequentes específicas de fábrica conectadas](iot-accelerators-faq-cf.md) e as [perguntas frequentes específicas do monitoramento remoto](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Onde posso encontrar o código-fonte dos aceleradores de solução?

O código-fonte é armazenado nos seguintes repositórios GitHub:

* [Acelerador de solução de Monitoramento Remoto (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Acelerador de solução de Monitoramento Remoto (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Acelerador de solução de Manutenção Preditiva](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Acelerador da solução de Fábrica Conectada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Quais SDKs posso usar para desenvolver clientes de dispositivos para os aceleradores de solução?

É possível localizar links para as diferentes SDKs do dispositivo IoT de linguagem (C, .NET, Java, Node.js, Python) nos repositórios do GitHub das [SDKs de IoT do Microsoft Azure](https://github.com/Azure/azure-iot-sdks).

Se você estiver usando o dispositivo DevKit, poderá encontrar recursos e exemplos no repositório GitHub do [SDK de DevKit do IoT](https://github.com/Microsoft/devkit-sdk).

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>A nova arquitetura de microsserviços está disponível para todos os três aceleradores de solução?

Atualmente, apenas a solução de monitoramento remoto usa a arquitetura de microsserviços, pois ela abrange o cenário mais amplo.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Quais são as vantagens que a nova arquitetura de software livre baseada em microsserviços fornecem na nova atualização?

Nos últimos dois anos, arquitetura de nuvem evoluiu bastante. Os microsserviços foram lançados como um ótimo padrão para obter flexibilidade e escala sem sacrificar a velocidade de desenvolvimento. Esse padrão de arquitetura é usado em vários serviços da Microsoft internamente, com resultados de escalabilidade e confiabilidade excelentes. A Microsoft está colocando essas aprendizagens em prática nos aceleradores de solução para que os clientes possam aproveitá-las.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Sou um administrador de serviços e gostaria de alterar o mapeamento de diretório entre minha assinatura e um locatário específico do Azure AD. Como posso concluir esta tarefa?

Consulte [Como adicionar uma assinatura existente ao seu diretório do Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Quero alterar um Administrador de Serviços ou um Coadministrador quando o logon for feito com uma conta organizacional

Confira o artigo de suporte [Alterando um Administrador de Serviços e um Coadministrador quando o logon for feito com uma conta organizacional](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Por que vejo este erro? "Sua conta não tem as permissões adequadas para criar uma solução. Verifique com o administrador da conta ou tente com uma conta diferente."

Observe o seguinte diagrama para obter orientação:

![Fluxograma de permissões](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Se você continuar recebendo o erro após validar que é um administrador global no locatário do Azure AD e um coadministrador na assinatura, solicite ao administrador da conta que remova o usuário e reatribua as permissões necessárias nesta ordem. Primeiro, adicione o usuário como um administrador global e adicione o usuário como um coadministrador na assinatura do Azure. Se o problema persistir, entre em contato com [Ajuda e Suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Por que estou vendo este erro se eu tenho uma assinatura do Azure? “Uma assinatura do Azure é necessária para criar soluções pré-configuradas. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos.”

Se você tiver certeza de que tem uma assinatura do Azure, valide o mapeamento de locatário da assinatura e verifique se o locatário correto está selecionado no menu suspenso. Se você validou que o locatário está correto, siga o diagrama anterior e valide o mapeamento da assinatura e esse locatário do Azure AD.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Onde posso encontrar informações sobre a versão anterior da solução de Monitoramento Remoto?

A versão anterior do acelerador de solução de Monitoramento Remoto era conhecida como a solução pré-configurada de Monitoramento Remoto do IoT Suite. Você pode encontrar a documentação arquivada em [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>O novo acelerador de solução está disponível na mesma região geográfica que a solução existente?

Sim, o novo monitoramento remoto está disponível nas mesmas regiões geográficas.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Qual é a diferença entre excluir um grupo de recursos no portal do Azure e clicar em excluir em um Solution Accelerator no azureiotsolutions.com?

* Se você excluir o Solution Accelerator no [azureiotsolutions.com](https://www.azureiotsolutions.com/), você excluirá todos os recursos que foram implantados quando criou o Solution Accelerator. Se você adicionou mais recursos ao grupo de recursos, esses recursos também serão excluídos.
* Se você excluir o grupo de recursos no [Portal do Azure](https://portal.azure.com), somente os recursos nesse grupo de recursos serão excluídos. Você também precisa excluir o aplicativo do Azure Active Directory associado ao acelerador de solução.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Posso continuar meus investimentos existentes nos aceleradores de solução do Azure IoT?

Sim. As soluções atualmente existentes continuarão funcionando na assinatura do Azure e o código-fonte ficará disponível no GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Hub IoT posso provisionar em uma assinatura?

Por padrão, você pode provisionar [10 Hubs IoT por assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). Você pode criar um [tíquete de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar esse limite. Como resultado, uma vez que cada acelerador de solução provisiona um novo Hub IoT, você só poderá provisionar até 10 aceleradores de solução em uma determinada assinatura.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Azure Cosmos DB posso provisionar em uma assinatura?

Cinquenta. Você pode criar um [tíquete de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar esse limite, mas, por padrão, você poderá apenas provisionar 50 instâncias do Cosmos DB por assinatura.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Quantas APIs do Bing Mapas Gratuitas posso provisionar em uma assinatura?

Duas. Você só pode criar dois Bing Mapas de Transações Internas de Nível 1 para planos Enterprise em uma assinatura do Azure. A solução de monitoramento remoto é provisionada por padrão com o plano Transações Internas de Nível 1. Como resultado, você só pode provisionar até duas soluções de monitoramento remoto em uma assinatura sem modificações.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Posso criar um acelerador de solução se possuo o Microsoft Azure para DreamSpark?

> [!NOTE]
> O Microsoft Azure para DreamSpark agora é conhecido como Microsoft Imagine para alunos.

Atualmente, você não pode criar um acelerador de solução com uma conta do [Microsoft Azure para DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/). No entanto, você pode criar uma [conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos que permite a você criar um acelerador de solução.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Como fazer para excluir um locatário do Azure AD?

Veja a postagem no blog de Eric Golpe, [Passo a passo da exclusão de um locatário do Azure AD](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Próximas etapas

Você também pode explorar alguns dos outros recursos dos aceleradores de solução de IoT:

* [Explorar os recursos do acelerador da solução de Monitoramento Remoto](quickstart-remote-monitoring-deploy.md)
* [Visão geral do acelerador de solução de Manutenção Preditiva](iot-accelerators-predictive-overview.md)
* [Implantar o Acelerador de solução de fábrica Conectada](quickstart-connected-factory-deploy.md)
* [Segurança IoT desde o início](/azure/iot-fundamentals/iot-security-ground-up)
