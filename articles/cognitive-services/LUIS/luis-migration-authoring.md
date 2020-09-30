---
title: Migrar para uma chave de criação de recursos do Azure
titleSuffix: Azure Cognitive Services
description: Este artigo descreve como migrar a autenticação de criação de Reconhecimento vocal (LUIS) de uma conta de email para um recurso do Azure.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 926b79e672c14249ec7c2b053dba7eb3a31443a7
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536029"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrar para uma chave de criação de recursos do Azure

A autenticação de criação de Reconhecimento vocal (LUIS) mudou de uma conta de email para um recurso do Azure. Embora não seja necessário no momento, a alternância para um recurso do Azure será imposta no futuro.


## <a name="what-is-migration"></a>O que é migração?

A migração é o processo de alterar a criação de autenticação de uma conta de email para um recurso do Azure. Sua conta será vinculada a uma assinatura do Azure e a um recurso de criação do Azure depois que você migrar. *Todos os usuários do LUIS (proprietários ou colaboradores) eventualmente precisarão migrar.*

A migração deve ser feita no portal do LUIS. Se você criar as chaves de criação usando a CLI do LUIS, por exemplo, você precisará concluir o processo de migração no portal do LUIS. Você ainda pode ter coautores em seus aplicativos após a migração, mas eles serão adicionados no nível de recurso do Azure em vez de no nível do aplicativo.

> [!Note]
> Antes da migração, os coautores são conhecidos como _colaboradores_ no nível do aplicativo Luis. Após a migração, a função de _colaborador_ do Azure é usada para a mesma funcionalidade no nível de recurso do Azure.

## <a name="note-before-you-migrate"></a>Observação antes de migrar

* Você deve migrar sua experiência de criação em **Nov, 2, 2020**. 
* A migração é um processo unidirecional. Você não pode voltar depois de migrar.
* Os aplicativos serão migrados automaticamente com você se você for o proprietário do aplicativo.
* O proprietário não pode escolher um subconjunto de aplicativos para migrar e o processo não é reversível.
* Os aplicativos desaparecerão do lado do colaborador depois que o proprietário for migrado.
* Os proprietários são solicitados a enviar emails aos colaboradores para informá-los sobre a migração.
* Os aplicativos não serão migrados com você se você for um colaborador no aplicativo.
* Não há nenhuma maneira de um proprietário saber que os colaboradores migraram.
* A migração não coleta automaticamente colaboradores e move-os ou adiciona-os ao recurso de criação do Azure. O proprietário do aplicativo é aquele que precisa concluir esta etapa após a migração. Esta etapa requer [permissões para o recurso de criação do Azure](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate).
* Após os colaboradores serem atribuídos ao recurso do Azure, eles precisam migrar para acessar aplicativos. Caso contrário, eles não terão acesso para criar os aplicativos.
* Um usuário migrado não pode ser adicionado como um colaborador do aplicativo.
* Se você tiver chaves de previsão que são atribuídas a aplicativos pertencentes a outro usuário, isso bloqueará a migração para o proprietário e os colaboradores. Consulte as recomendações mais adiante neste artigo.

> [!Note]
> Se você precisar criar um recurso de tempo de execução de previsão, há [um processo separado](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) para criá-lo.

## <a name="migration-prerequisites"></a>Pré-requisitos de migração

* Você precisa estar associado a uma assinatura válida do Azure. Peça ao administrador de locatários para adicioná-lo à assinatura ou [Inscreva-se em um gratuito](https://azure.microsoft.com/free/cognitive-services).
* Você precisa criar um recurso de criação do LUIS Azure no portal do LUIS ou no portal do Azure. A criação de um recurso de criação do portal do LUIS faz parte do fluxo de migração que é abordado na próxima seção.
* Se você for um colaborador em aplicativos, os aplicativos não serão migrados automaticamente. Recomendamos que você faça backup desses aplicativos exportando-os ou usando a [API de exportação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). Você pode importar o aplicativo de volta para o LUIS após a migração. O processo de importação cria um novo aplicativo com uma nova ID do aplicativo, para o qual você é o proprietário.
* Se você for o proprietário do aplicativo, não precisará exportar seus aplicativos porque eles serão migrados automaticamente. Recomendamos que você salve a lista de colaboradores de cada aplicativo. Um modelo de email com essa lista é fornecido opcionalmente como parte do processo de migração.


|Portal|Finalidade|
|--|--|
|[Azure](https://azure.microsoft.com/free/cognitive-services)| Crie recursos de previsão e criação.<br> Atribuir colaboradores em recursos.|
|[LUIS](https://www.luis.ai)| Migre para novos recursos de criação.<br> Crie novos recursos de criação no fluxo de migração.<br> Atribua ou desatribua recursos de previsão e criação a aplicativos na página **gerenciar**  >  **recursos do Azure** . <br> Mova aplicativos de um recurso de criação para outro.  |

> [!Note]
> A criação de seu aplicativo LUIS é gratuita, conforme indicado pela camada F0. Saiba [mais sobre os tipos de preço](luis-limits.md#key-limits).


## <a name="migration-steps"></a>Etapas da migração

1. No portal do LUIS no qual você está trabalhando, você pode iniciar o processo de migração no ícone **do Azure** na barra de ferramentas superior.

   > [!div class="mx-imgBorder"]
   > ![Ícone de migração](./media/migrate-authoring-key/migration-button.png)

2. A janela pop-up de migração permite que você continue a migração ou migre mais tarde. Selecione **migrar agora**.

   > [!div class="mx-imgBorder"]
   > ![Primeira janela pop-up no processo de migração, onde você seleciona migrar agora](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Se qualquer um de seus aplicativos tiver colaboradores, você será solicitado a enviar um email que lhes permita saber mais sobre a migração. Esta é uma etapa opcional.

   Para cada colaborador e aplicativo, o aplicativo de email padrão é aberto com um email levemente formatado. Você pode editar o email antes de enviá-lo. O modelo de email inclui a ID do aplicativo e o nome do aplicativo exatos.

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```

   > [!Note]
   > Depois de migrar sua conta para o Azure, seus aplicativos não estarão mais disponíveis para colaboradores.

4. Se você for um colaborador em qualquer aplicativo, será solicitado a exportar uma cópia dos aplicativos selecionando essa opção durante o fluxo de migração. Esta é uma etapa opcional.

   Se você selecionar a opção, a página a seguir será exibida. Selecione os botões de download à esquerda para exportar os aplicativos desejados. Você pode importar esses aplicativos de volta após a migração, pois eles não serão migrados automaticamente com você.

   > [!div class="mx-imgBorder"]
   > ![Avisar para exportar seus aplicativos.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. Você pode optar por criar um novo recurso de criação de LUIS ou migrar para um recurso de criação existente se já tiver criado um do Azure. Escolha a opção desejada selecionando um dos botões a seguir.

   > [!div class="mx-imgBorder"]
   > ![Botões para criar um novo recurso de criação e usar um recurso de criação existente](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Criar novo recurso de criação do LUIS para migrar

Se você quiser criar um novo recurso de criação, selecione **criar novo recurso de criação** e forneça as informações a seguir na próxima janela. Em seguida, selecione **Concluído**.

> [!div class="mx-imgBorder"]
> ![Janela para criar um recurso de criação](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Nome do locatário**: o locatário ao qual sua assinatura do Azure está associada. Isso é definido por padrão para o locatário que você está usando no momento. Você pode alternar locatários selecionando o avatar mais à direita, que contém suas iniciais.
* **Nome do recurso**: um nome personalizado que você escolher. Ele é usado como parte da URL para suas consultas de ponto de extremidade de criação e previsão.
* **Nome da assinatura**: a assinatura que será associada ao recurso. Se você tiver mais de uma assinatura que pertence ao seu locatário, selecione aquela que você deseja na lista suspensa.
* **Nome do grupo de recursos do Azure**: um nome de grupo de recursos personalizado que você escolhe na lista suspensa. Os grupos de recursos permitem agrupar os recursos do Azure para acesso e gerenciamento.

Observe que você pode ter 10 recursos de criação gratuitos por região, por assinatura. Se sua assinatura tiver mais de 10 recursos de criação na mesma região, você não poderá criar um novo.

Quando o recurso de criação é criado, a mensagem de êxito é mostrada. Selecione **fechar** para fechar a janela pop-up.

  > [!div class="mx-imgBorder"]
  > ![Mensagem que indica que o recurso de criação foi criado com êxito](./media/migrate-authoring-key/migration-success-2.png)


### <a name="use-existing-authoring-resource-to-migrate"></a>Usar recurso de criação existente para migrar

Se sua assinatura já estiver associada a um recurso do Azure de criação de LUIS ou se você tiver criado um recurso do portal do Azure e quiser migrar para ele em vez de criar um novo, selecione **usar recurso de criação existente**. Forneça as seguintes informações na próxima janela e, em seguida, selecione **concluído**.

> [!div class="mx-imgBorder"]
>![Janela para alterar um recurso de criação existente](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Nome do locatário**: o locatário ao qual sua assinatura do Azure está associada. Isso é definido por padrão para o locatário que você está usando no momento.
* **Nome da assinatura**: a assinatura que será associada ao recurso. Se você tiver mais de uma assinatura que pertence ao seu locatário, selecione aquela que você deseja na lista suspensa.
* **Nome do recurso**: o recurso de criação para o qual você deseja migrar.

> [!Note]
> Se você não conseguir ver o recurso de criação na lista suspensa, certifique-se de criá-lo no local apropriado de acordo com o portal do LUIS em que você está conectado. Além disso, certifique-se de que o que você criou é um recurso de criação e não um recurso de previsão.


Valide o nome do recurso de criação e selecione o botão **migrar** .

> [!div class="mx-imgBorder"]
> ![Janela para escolher um recurso de criação, com o botão migrar agora disponível](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

Uma mensagem de êxito é exibida. Selecione **fechar** para fechar a janela pop-up.

> [!div class="mx-imgBorder"]
> ![Mensagem que diz que o recurso de criação foi migrado com êxito](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Usando aplicativos após a migração

Após o processo de migração, todos os seus aplicativos LUIS para os quais você é o proprietário agora serão atribuídos a um único recurso de criação de LUIS.

A lista **meus aplicativos** mostra os aplicativos migrados para o novo recurso de criação. Antes de acessar seus aplicativos, selecione a assinatura e o recurso de criação de LUIS para ver os aplicativos que você pode criar.

 > [!div class="mx-imgBorder"]
 > ![Caixas de assinatura e recurso de criação](./media/create-app-in-portal-select-subscription-luis-resource.png)

Você não precisa saber a chave do recurso de criação para continuar editando seus aplicativos no portal do LUIS.

Se você planeja editar seus aplicativos programaticamente, precisará dos valores de chave de criação. Esses valores são exibidos na página **gerenciar**  >  **recursos do Azure** no portal do Luis. Eles também estão disponíveis na portal do Azure na página de **chaves** do recurso. Você também pode criar mais recursos de criação e atribuí-los da mesma página.

 > [!div class="mx-imgBorder"]
 > ![Página para gerenciar recursos de criação](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="adding-contributors-to-authoring-resources"></a>Adicionando colaboradores aos recursos de criação

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Saiba [como adicionar colaboradores](luis-how-to-collaborate.md) em seu recurso de criação. Os colaboradores terão acesso a todos os aplicativos sob esse recurso.

Você pode adicionar colaboradores ao recurso de criação do portal do Azure, na página de controle de **acesso (iam)** para esse recurso. Para obter mais informações, consulte [Adicionar acesso de colaborador](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

> [!Note]
> Se o proprietário do aplicativo LUIS migrou e adicionou o colaborador como um colaborador no recurso do Azure, o colaborador ainda não terá acesso ao aplicativo, a menos que eles também migrem.

## <a name="luis-portal-migration-reminders"></a>Lembretes de migração do portal LUIS

O [portal do Luis](https://www.luis.ai) fornece o processo de migração.

Você será solicitado a migrar se as duas condições forem verdadeiras:
* Você tem aplicativos no sistema de autenticação de email para criação.
* Você é o proprietário do aplicativo.

A cada semana, você será solicitado a migrar seus aplicativos. Você pode fechar esta janela sem migrar. Se você quiser migrar antes do próximo período agendado, poderá iniciar o processo de migração no ícone **do Azure** na barra de ferramentas superior do portal do Luis.

## <a name="prediction-resources-blocking-migration"></a>Recursos de previsão que bloqueiam a migração
Sua migração afeta negativamente qualquer tempo de execução do aplicativo. Quando você migra, todos os colaboradores são removidos dos seus aplicativos e você é removido como um colaborador de outros aplicativos. Esse processo significa que as chaves que um colaborador atribui também são removidas, o que pode interromper seu aplicativo se ele estiver em produção. Esse é o motivo pelo qual bloqueamos a migração até que você remova manualmente colaboradores ou chaves atribuídos a eles.

A migração será bloqueada se qualquer uma dessas condições for verdadeira:

* Você atribuiu recursos de previsão/tempo de execução em aplicativos que não são de sua propriedade.
* Você tem outros usuários que atribuem recursos de previsão/tempo de execução aos aplicativos que você possui.

### <a name="recommended-steps-if-youre-the-owner-of-the-app"></a>Etapas recomendadas se você for o proprietário do aplicativo
Se você for um proprietário de alguns aplicativos e tiver colaboradores atribuídos uma chave de previsão/tempo de execução para esses aplicativos, um erro aparecerá quando você migrar. O erro lista as IDs de aplicativo que têm chaves de previsão atribuídas a elas pertencentes a outros usuários.

É recomendável que você:
* Notifique os colaboradores sobre a migração.
* Remova todos os colaboradores dos aplicativos mostrados no erro.
* Passará pelo processo de migração, o que deverá ter sucesso se você remover manualmente os colaboradores.
* Atribua colaboradores como colaboradores ao seu novo recurso de criação. Os colaboradores migrarão e reatribuirão os recursos de previsão de volta para os aplicativos. Observe que isso causará uma interrupção no aplicativo temporariamente até que os recursos de previsão sejam reatribuídos.

Há outra solução em potencial aqui. Antes da migração do proprietário, os colaboradores podem adicionar proprietários de aplicativo como colaboradores em suas assinaturas do Azure da portal do Azure. Esta etapa concederá ao proprietário acesso ao recurso de previsão de tempo de execução. Se o proprietário migrar usando a nova assinatura à qual ele foi adicionado (que será encontrado em um novo locatário), essa etapa não desbloqueará apenas o processo de migração para o colaborador e o proprietário do aplicativo. Ele também permitirá uma migração tranqüila de aplicativos, com a chave de previsão ainda atribuída a eles, não dividindo os aplicativos.


### <a name="recommended-steps-if-youre-a-collaborator-on-an-app"></a>Etapas recomendadas se você for um colaborador em um aplicativo
Se você estiver colaborando em aplicativos e tiver atribuído uma chave de previsão/tempo de execução para esses aplicativos, um erro será exibido quando você migrar. O erro lista as IDs de aplicativo e os caminhos de chave que estão bloqueando a migração.

É recomendável que você:
* Exportar aplicativos como um backup. Essa é uma etapa opcional no processo de migração.
* Cancele a atribuição dos recursos de previsão da página **gerenciar**  >  **recursos do Azure** .
* Passam pelo processo de migração.
* Importe aplicativos de volta após a migração.
* Reatribua as chaves de previsão para seus aplicativos da página **gerenciar**  >  **recursos do Azure** .

> [!Note]
> Quando você importar os aplicativos de volta após a migração, eles terão IDs de aplicativo diferentes. Eles também serão diferentes dos que estão sendo atingidos na produção. Agora você será o proprietário desses aplicativos.

## <a name="troubleshooting-the-migration-process"></a>Solucionando problemas do processo de migração

Quando você tenta migrar, mas não consegue encontrar sua assinatura do Azure na lista suspensa:
* Verifique se você tem uma assinatura válida do Azure que está autorizada a criar recursos de serviços cognitivas. Vá para a [portal do Azure](https://ms.portal.azure.com) e verifique o status da assinatura. Se você não tiver uma, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/cognitive-services/).
* Verifique se você está no locatário adequado associado à sua assinatura válida. Você pode alternar locatários do avatar para a esquerda de suas iniciais nesta barra de ferramentas: ![ barra de ferramentas onde você pode alternar locatários](./media/migrate-authoring-key/switch-user-tenant-2.png)

Se você tiver um recurso de criação existente, mas não conseguir encontrá-lo quando selecionar a opção **usar recurso de criação existente** :
* O recurso foi provavelmente criado em um local diferente do portal em que você está conectado. Verifique as [regiões de criação do Luis e portais](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions).
* Em vez disso, crie um novo recurso no portal do LUIS.

Se você selecionar a opção **criar novo recurso de criação** e a migração falhar com a mensagem de erro "falha ao recuperar as informações do usuário do Azure, tente novamente mais tarde":
* Sua assinatura pode ter 10 ou mais recursos de criação por região, por assinatura. Se esse for o caso, você não poderá criar um novo recurso de criação.
* Migre selecionando a opção **usar recurso de criação existente** e selecionando um dos recursos existentes em sua assinatura.

Se você vir o erro a seguir, verifique as [etapas recomendadas se você for o proprietário do aplicativo](#recommended-steps-if-youre-the-owner-of-the-app).
![Erro que mostra a migração falhou para os proprietários](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Se você vir o erro a seguir, verifique as [etapas recomendadas se você for um colaborador em um aplicativo](#recommended-steps-if-youre-a-collaborator-on-an-app).
![Erro que mostra a migração falhou para colaboradores](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Próximas etapas

* Examine os [conceitos sobre criação e chaves de tempo de execução](luis-how-to-azure-subscription.md).
* Examine como [atribuir chaves](luis-how-to-azure-subscription.md) e [Adicionar colaboradores](luis-how-to-collaborate.md).
