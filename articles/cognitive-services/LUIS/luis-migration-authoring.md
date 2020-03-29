---
title: Migrar para o recurso Do Zure para a autoria
titleSuffix: Azure Cognitive Services
description: Migre para uma chave de recurso de autoria do Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: ec6f9592a4c149be382fab66cca27d929644d988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194502"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrar para uma chave de autoria de recursos do Azure

A autenticação de autenticação de compreensão de idiomas (LUIS) mudou de uma conta de e-mail para um recurso do Azure. Embora não seja necessário atualmente, a mudança para um recurso Do Zure será aplicada no futuro.

## <a name="why-migrate"></a>Por que migrar?

O uso de um recurso do Azure para a autoria permite que você, como proprietário do recurso, controle o acesso à autoria. Você pode criar e nomear recursos de autoria para gerenciar diferentes grupos de autores.

Por exemplo, você é o proprietário de 2 aplicativos LUIS, e você tem diferentes membros que são colaboradores em cada aplicativo. Você pode criar dois recursos de autoria diferentes e atribuir cada aplicativo a cada recurso separado. Em seguida, atribua cada membro como um contribuinte para o recurso de autoria adequado, dependendo de qual aplicativo eles colaboram. O recurso de autoria do Azure controla a autorização.

> [!Note]
> Antes da migração, os co-autores são conhecidos como _colaboradores_ no nível do aplicativo LUIS. Após a migração, a função Azure de _contribuinte_ é usada para a mesma funcionalidade, mas no nível de recursos do Azure.

## <a name="what-is-migrating"></a>O que está migrando?

A migração inclui:

* Todos os usuários do LUIS, proprietários e colaboradores.
* **Todos os** aplicativos.
* Uma migração **unidirecional.**

O proprietário não pode escolher um subconjunto de aplicativos para migrar e o processo não é reversível.

A migração não é:

* Um processo que coleta colaboradores e automaticamente move ou adiciona ao recurso de criação do Azure. Você, como proprietário do aplicativo, precisa completar essa etapa. Esta etapa requer permissões para o recurso apropriado.
* Um processo para criar e atribuir um recurso de tempo de execução de previsão. Se você precisar de um recurso de tempo de execução de previsão, esse é [um processo separado](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) e não é alterado.

## <a name="how-are-the-apps-migrating"></a>Como os aplicativos estão migrando?

O [portal LUIS](https://www.luis.ai) fornece o processo de migração.

Você será solicitado a migrar se:

* Você tem aplicativos no sistema de autenticação de e-mail para autoria.
* E você é o dono do aplicativo.

Você pode atrasar o processo de migração cancelando a janela. Você é periodicamente solicitado a migrar até que você migre ou o prazo de migração é passado. Você pode iniciar o processo de migração a partir do ícone de bloqueio da barra de navegação superior.

## <a name="migration-for-the-app-owner"></a>Migração para o proprietário do aplicativo

### <a name="before-you-migrate"></a>Antes de migrar

* **Obrigatório,** você precisa ter uma [assinatura do Azure.](https://azure.microsoft.com/free/) Uma parte do processo de assinatura requer informações de cobrança. No entanto, você`F0`pode usar o nível de preços gratuito () quando você usa LUIS.
* **Opcionalmente,** faça backup dos aplicativos da lista de aplicativos do portal LUIS exportando cada aplicativo ou use a [API de exportação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40).
* **Opcionalmente,** salve a lista de colaboradores de cada aplicativo. Esta lista de e-mails é fornecida como parte do processo de migração.


**A autoria do seu aplicativo LUIS é gratuita,** indicada pelo `F0` nível. Saiba [mais sobre os níveis de preços](luis-boundaries.md#key-limits).

Se você não tiver uma assinatura do Azure, [inscreva-se](https://azure.microsoft.com/free/).

### <a name="migration-steps"></a>Etapas da migração

Siga [estas etapas de migração](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Depois que você migrar

Após o processo de migração, todos os seus aplicativos LUIS são agora atribuídos a um único recurso de autoria de LUIS.

Você pode criar mais recursos de criação e atribuir a partir da página gerenciar -> recursos do **Azure** no _portal LUIS_.

Você pode adicionar contribuintes ao recurso de autoria do _portal Azure_, na página **Controle de Acesso (IAM)** para esse recurso. Para obter mais informações, consulte [adicionar acesso ao contribuinte](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

|Portal|Finalidade|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Criar recursos de previsão e de autoria.<br>* Atribuir colaboradores.|
|[Luis](https://www.luis.ai)|* Migrar para novos recursos de autoria.<br>* Atribuir ou não atribuir recursos de previsão e autoria a aplicativos da página **Gerenciar -> recursos do Azure.**|

## <a name="migration-for-the-app-contributor"></a>Migração para o contribuinte do aplicativo

Todo usuário do LUIS precisa migrar, incluindo colaboradores/colaboradores. Um colaborador deve migrar para ter acesso ao aplicativo.

> [!Note]
> Se o proprietário do aplicativo LUIS migrar e adicionar o colaborador como colaborador no recurso do Azure, o colaborador ainda não terá acesso ao aplicativo, a menos que também migre.

### <a name="before-the-app-is-migrated"></a>Antes que o aplicativo seja migrado

Você pode optar por exportar um aplicativo em que você é um colaborador e, em seguida, importar o aplicativo de volta para LUIS. O processo de importação cria um novo aplicativo com um novo ID de aplicativo, para o qual você é o proprietário.

### <a name="after-the-app-is-migrated"></a>Depois que o aplicativo é migrado

O proprietário do aplicativo precisa [adicionar seu e-mail ao recurso de autoria do Azure como colaborador.](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)

Após o processo de migração, todos os aplicativos que você possui estão disponíveis na página **Meus aplicativos** do portal LUIS.

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>Solução de problemas do processo de migração para a autoria do LUIS

* As chaves de autoria do LUIS só são visíveis no portal LUIS após a conclusão do processo de migração. Se você criar as chaves de criação, como com o LUIS CLI, o usuário ainda precisa concluir o processo de migração no portal LUIS.
* Se um usuário migrado adicionar um usuário não migrado como contribuinte em seu recurso azure, o usuário não migrado não terá acesso aos aplicativos a menos que migre.
* Se um usuário não migrado não é proprietário de nenhum aplicativo, mas é um colaborador de outros aplicativos que pertencem a outros e os proprietários passaram pelo processo de migração, esse usuário precisará migrar para ter acesso aos aplicativos.
* Se um usuário não migrado adicionou outro usuário migrado como colaborador ao seu aplicativo, um erro ocorrerá, pois você não poderá adicionar um usuário migrado como colaborador a um aplicativo. O usuário não migrado terá então que passar pelo processo de migração e criar um recurso azure e adicionar o usuário migrado como contribuinte para esse recurso.

Você recebe um erro durante o processo de migração se:
* Sua assinatura não autoriza você a criar recursos de Serviços Cognitivos
* Sua migração impacta negativamente qualquer tempo de execução dos aplicativos. Ao migrar, todos os colaboradores são removidos de seus aplicativos e você é removido como um colaborador de outros aplicativos. Este processo significa que as chaves que você atribuiu também são removidas. A migração é bloqueada se você tiver atribuído chaves em outros aplicativos. Remova a chave que você atribuiu com segurança antes de migrar. Se você sabe que a chave que você atribuiu não é usada no tempo de execução, então você precisa removê-la para poder progredir na migração.

Acesse a lista de recursos do azure do seu aplicativo usando o seguinte formato de URL:

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>Próximas etapas

* [Como migrar seu aplicativo para um recurso de autoria](luis-migration-authoring-steps.md)
