---
title: Gerenciar QnA Maker QnA Maker de aplicativos
description: O QnA Maker permite que várias pessoas colaborem em uma base de dados de conhecimento. O QnA Maker oferece uma capacidade para melhorar a qualidade de sua base de dados de conhecimento com o aprendizado ativo. É possível revisar, aceitar ou rejeitar e adicionar sem remover ou alterar as perguntas existentes.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 93d9cc871d1cb114f7f08b68eb8ae9d597e228b9
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376478"
---
# <a name="manage-qna-maker-app"></a>Gerenciar QnA Maker aplicativo

QnA Maker permite que você colabore com autores e editores de conteúdo diferentes, oferecendo uma capacidade de restringir o acesso do colaborador com base na função do colaborador.
Saiba mais sobre os [conceitos de autenticação do colaborador do QnA Maker](../Concepts/role-based-access-control.md).

Você também pode melhorar a qualidade de sua base de dados de conhecimento sugerindo perguntas alternativas por meio do [aprendizado ativo](../Concepts/active-learning-suggestions.md). Os envios por usuários são levados em consideração e aparecem como sugestões na lista de perguntas alternativas. Você tem a flexibilidade de adicionar essas sugestões como perguntas alternativas ou rejeitá-las.

Sua base de dados de conhecimento não é alterada automaticamente. Para que qualquer alteração entre em vigor, você deve aceitar as sugestões. Essas sugestões adicionam perguntas, mas não alteram nem removem perguntas existentes.

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Adicionar controle de acesso baseado em função do Azure (RBAC do Azure)

QnA Maker permite que várias pessoas colaborem em todas as bases de dados de conhecimento no mesmo recurso de QnA Maker. Esse recurso é fornecido com o [controle de acesso baseado em função do Azure (RBAC do Azure)](../../../active-directory/role-based-access-control-configure.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Acesso no nível de recurso QnA Maker

Não é possível compartilhar uma base de dados de conhecimento específica em um serviço QnA Maker. Se você quiser um controle de acesso mais granular, considere distribuir suas bases de dados de conhecimento em diferentes QnA Maker recursos e, em seguida, adicionar funções a cada recurso.

## <a name="add-a-role-to-a-resource"></a>Adicionar uma função a um recurso

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Adicionar uma conta de usuário ao recurso de QnA Maker

As etapas a seguir usam a função de colaborador, mas qualquer uma das [funções](../reference-role-based-access-control.md) pode ser adicionada usando estas etapas

1. Entre no portal [do Azure](https://portal.azure.com/) e vá para o recurso QnA Maker.

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Abra a guia **Controle de Acesso (IAM)**.

    ![IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Selecione **Adicionar**.

    ![Adicionar IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Selecione uma função na lista a seguir:

    |Função|
    |--|
    |Proprietário|
    |Colaborador|
    |QnA Maker leitor de serviços cognitivas|
    |Editor de QnA Maker de serviços cognitivas|
    |Usuário dos Serviços Cognitivos|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker Adicionar função IAM.":::

1. Insira o endereço de email do usuário e pressione **salvar**.

    ![Adicionar no IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Exibir QnA Maker base de dados de conhecimento

Quando a pessoa que você compartilhou seu serviço de QnA Maker com logs no [portal de QnA Maker](https://qnamaker.ai), ele pode ver todas as bases de dados de conhecimento nesse serviço com base em sua função.

Quando eles selecionam uma base de dados de conhecimento, sua função atual nesse QnA Maker recurso fica visível ao lado do nome da base de dados de conhecimento.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Captura de tela da base de dados de conhecimento no modo de edição com o nome da função entre parênteses ao lado do nome da base de dados de conhecimento no canto superior esquerdo da página da Web.":::

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Atualizar a versão de tempo de execução para usar o aprendizado ativo

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

O aprendizado ativo é compatível com a versão de runtime 4.4.0 e superior. Se sua base de dados de conhecimento foi criada em uma versão anterior, [atualize seu runtime](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) para usar esse recurso.

# <a name="qna-maker-managed-preview-release"></a>[Gerenciado QnA Maker (versão de visualização)](#tab/v2)

No QnA Maker gerenciado (versão prévia), como o tempo de execução é hospedado pelo próprio serviço de QnA Maker, não há necessidade de atualizar o tempo de execução manualmente.

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>Ativar o aprendizado ativo para perguntas alternativas

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

O aprendizado ativo está desativado por padrão. Ative-o para ver sugestões de perguntas. Depois de ativar o aprendizado ativo, você precisa enviar informações do aplicativo cliente para QnA Maker. Para obter mais informações, consulte [o fluxo arquitetônico para usar GenerateAnswer e treinar APIs de um bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Selecione **publicar** para publicar a base de dados de conhecimento. Consultas de aprendizado ativo são coletadas somente do ponto de extremidade de previsão da API GenerateAnswer. As consultas ao painel de teste no portal de QnA Maker não afetam o aprendizado ativo.

1. Para ativar o aprendizado ativo no portal de QnA Maker, vá para o canto superior direito, selecione seu **nome** , vá para [**configurações de serviço**](https://www.qnamaker.ai/UserSettings).

    ![Ative as alternativas de perguntas sugeridas do aprendizado ativo na página Configurações de serviço. Selecione seu nome de usuário no menu superior direito e, em seguida, selecione configurações de serviço.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Localize o serviço QnA Maker e, em seguida, ative o **aprendizado ativo**.

    > [!div class="mx-imgBorder"]
    > [![Na página Configurações de serviço, alterne o recurso de aprendizado ativo. Se não for possível alternar o recurso, talvez seja necessário atualizar seu serviço.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)
    > [!Note]
    > A versão exata na imagem anterior é mostrada apenas como um exemplo. Sua versão pode ser diferente.
    Quando o **aprendizado ativo** é habilitado, a base de dados de conhecimento sugere novas perguntas em intervalos regulares com base em perguntas enviadas pelo usuário. Você pode desabilitar o **aprendizado ativo** desativando a configuração novamente.

# <a name="qna-maker-managed-preview-release"></a>[Gerenciado QnA Maker (versão de visualização)](#tab/v2)

Por padrão, o aprendizado ativo está **ativado** no QnA Maker gerenciado (versão prévia). Para ver as perguntas alternativas sugeridas, [use as opções de exibição](../How-To/improve-knowledge-base.md#view-suggested-questions) na página Editar.

---

## <a name="review-suggested-alternate-questions"></a>Revisar as perguntas alternativas sugeridas

[Examine as perguntas sugeridas alternativas](improve-knowledge-base.md) na página de **edição** de cada base de dados de conhecimento.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento](./manage-knowledge-bases.md)