---
title: Migrar para o recurso do Azure para criação 2
titleSuffix: Azure Cognitive Services
description: Migre para uma chave de recurso de criação do Azure 2.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/17/2020
ms.author: diberry
ms.openlocfilehash: 5f0778436db7bd8c3a09e3ba346d8a9a6c4af454
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84983768"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrar para uma chave de criação de recursos do Azure

A autenticação de criação de Reconhecimento vocal (LUIS) mudou de uma conta de email para um recurso do Azure. Embora não seja necessário no momento, a alternância para um recurso do Azure será imposta no futuro.


## <a name="what-is-migration"></a>O que é migração?

A migração é o processo de alterar a criação de autenticação de uma conta de email para um recurso do Azure. Sua conta será vinculada a uma assinatura do Azure e a um recurso de criação do Azure depois que você migrar. **Todos os usuários do LUIS (proprietários ou colaboradores) eventualmente precisarão migrar.** A migração deve ser feita no portal do LUIS. Se você criar as chaves de criação, como com a CLI do LUIS, ainda será necessário concluir o processo de migração no portal do LUIS. Você ainda pode ter coautores em seus aplicativos após a migração, mas eles serão adicionados no nível de recurso do Azure em vez de no nível do aplicativo.

> [!Note]
> Antes da migração, os coautores são conhecidos como _colaboradores_ no nível do aplicativo Luis. Após a migração, a função de _colaborador_ do Azure é usada para a mesma funcionalidade, mas no nível de recurso do Azure.

## <a name="note-before-you-migrate"></a>Observação antes de migrar

* A migração é um processo **unidirecional** . Você não pode voltar depois de migrar.
* Os aplicativos serão **migrados automaticamente** com você se você for o **proprietário** do aplicativo.
* O proprietário não pode escolher um subconjunto de aplicativos para migrar e o processo não é reversível.
* Os aplicativos **desaparecerão dos lados do colaborador** depois que o **proprietário for migrado**.
* Os proprietários são solicitados a enviar emails aos colaboradores para informá-los sobre a migração.
* Os aplicativos **não serão migrados** com você se você for um **colaborador** no aplicativo.
* Não há nenhuma maneira de um proprietário saber que seus colaboradores migraram.
* A **migração não** coleta automaticamente colaboradores e move-os ou adiciona-os ao recurso de criação do Azure. O proprietário do aplicativo é aquele que precisa concluir esta etapa após a migração. Esta etapa requer [permissões para o recurso de criação do Azure](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate).
* Depois de ser atribuído ao recurso do Azure, **os colaboradores precisarão migrar para acessar aplicativos**. Caso contrário, eles não terão acesso para criar os aplicativos.
* Um usuário migrado não pode ser adicionado como um colaborador do aplicativo.
* Se **você tiver chaves de previsão que são atribuídas a aplicativos pertencentes a outro usuário**, isso **bloqueará a migração** para o proprietário e os colaboradores. Veja abaixo as recomendações.

> [!Note]
> Se você precisar criar um recurso de tempo de execução de previsão, há [um processo separado](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) para criá-lo.

## <a name="migration-prerequisites"></a>Pré-requisitos de migração

* Você precisa estar associado a uma assinatura válida do Azure. Peça ao administrador de locatários para adicioná-lo à assinatura ou você pode se inscrever para obter um [aqui](https://azure.microsoft.com/free/)gratuito.
* Você precisa criar um recurso de criação do LUIS Azure no portal do LUIS ou no portal do Azure. A criação de um recurso de criação do portal do LUIS faz parte do fluxo de migração que é abordado na próxima seção.
* Se você for um **colaborador em aplicativos**, os aplicativos não serão migrados automaticamente. É **recomendável fazer backup desses aplicativos** exportando-os ou usar a [API de exportação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). Você pode importar o aplicativo de volta para o LUIS após a migração. O processo de importação cria um novo aplicativo com uma nova ID do aplicativo, para o qual você é o proprietário.
* Se você for o **proprietário do aplicativo**, não será necessário exportar seus aplicativos, pois eles serão migrados automaticamente. É **recomendável salvar a lista de collaborator's de cada aplicativo.** Um modelo de email com essa lista é fornecido opcionalmente como parte do processo de migração.


|Portal|Finalidade|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Crie recursos de previsão e criação.<br>* Atribuir colaboradores em recursos.|
|[LUIS](https://www.luis.ai)|* Migre para novos recursos de criação.<br>* Criar novo recurso de criação no fluxo de migração.<br>* Atribuir ou cancelar a atribuição de recursos de previsão e de criação a aplicativos da página **gerenciar > recursos do Azure** . <br>* Mover aplicativos de um recurso de criação para outro.  |

> [!Note]
> A **criação de seu aplicativo Luis é gratuita**, indicada pela `F0` camada. Saiba [mais sobre os tipos de preço](luis-limits.md#key-limits).


## <a name="migration-steps"></a>Etapas da migração

1. No portal do LUIS no qual você está trabalhando, você pode iniciar o processo de migração no ícone **do Azure** na barra de ferramentas superior.

   > [!div class="mx-imgBorder"]
   > ![Ícone de migração](./media/migrate-authoring-key/migration-button.png)

2. A janela pop-up de migração permite que você continue a migração ou migre mais tarde. Selecione **migrar agora**.

   > [!div class="mx-imgBorder"]
   > ![Primeira janela pop-up no processo de migração, selecione migrar agora.](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Opcionalmente, se algum dos seus aplicativos tiver colaboradores, você será solicitado a **enviar um email** informando-os sobre a migração. Esta é uma etapa opcional.

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

4. Opcionalmente, se você for um colaborador em qualquer aplicativo, será solicitado a **exportar uma cópia dos aplicativos** selecionando essa opção durante o fluxo de migração. Depois de selecionar a opção, você encontrará a página abaixo, na qual você clica no botão baixar à esquerda para exportar os aplicativos desejados. Você pode importar esses aplicativos de volta depois de migrar, pois eles não serão migrados automaticamente com você. Esta é uma etapa opcional.

   > [!div class="mx-imgBorder"]
   > ![Avisar para exportar seu aplicativo.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. Você pode optar por criar um novo recurso de criação de LUIS ou migrar para um recurso de criação existente se já tiver criado um do Azure. Escolha a opção desejada selecionando o botão apropriado abaixo.

   > [!div class="mx-imgBorder"]
   > ![Criar recurso de criação](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Criar novo recurso de criação do LUIS para migrar

Se você quiser criar um novo recurso de criação, selecione **criar um novo recurso de criação** e forneça as informações a seguir na próxima janela.

> [!div class="mx-imgBorder"]
> ![Criar recurso de criação](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Nome do recurso** – um nome personalizado que você escolher, usado como parte da URL para as consultas de ponto de extremidade de criação e previsão.
* **Nome da assinatura** -a assinatura que será associada ao recurso. Se você tiver mais de uma assinatura que pertence ao seu locatário, selecione aquela que você deseja na lista suspensa.
* **Grupo de recursos** -um nome de grupo de recursos personalizado que você escolhe na lista suspensa. Os grupos de recursos permitem agrupar os recursos do Azure para acesso e gerenciamento.
* **Locatário** – o locatário ao qual sua assinatura do Azure está associada. Isso é definido por padrão para o locatário que você está escolhendo no momento. Você pode alternar locatários selecionando o avatar mais à direita que contém suas iniciais.

Depois de inserir as informações acima, selecione **concluído**.

Observe que você pode ter 10 recursos de criação gratuitos por região, por assinatura. Se sua assinatura tiver mais de 10 recursos de criação na mesma região, você não poderá criar um novo.

* Quando o recurso de criação é criado, a mensagem de êxito é mostrada. Selecione **fechar** para fechar a janela pop-up.

  > [!div class="mx-imgBorder"]
  > ![O recurso de criação foi criado com êxito.](./media/migrate-authoring-key/migration-success-2.png)

### <a name="use-existing-authoring-resource-to-migrate"></a>Usar recurso de criação existente para migrar

Se sua assinatura já estiver associada a um recurso do Azure de criação de LUIS ou se você tiver criado no portal do Azure e quiser migrar para ele em vez de criar um novo recurso, selecione **usar recurso de criação existente** e forneça as informações a seguir na próxima janela.

> [!div class="mx-imgBorder"]
>![Criar recurso de criação](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Locatário** – o locatário ao qual sua assinatura do Azure está associada. Isso é definido por padrão para o locatário que você está escolhendo no momento.
* **Nome da assinatura** -a assinatura que será associada ao recurso. Se você tiver mais de uma assinatura que pertence ao seu locatário, selecione aquela que você deseja na lista suspensa.
* **Nome do recurso** – selecione o recurso de criação para o qual você deseja migrar.

> [!Note]
> Se você não puder ver o recurso de criação na lista suspensa, certifique-se de criá-lo no **local apropriado** de acordo com o portal do Luis no qual você está conectado. Além disso, certifique-se de que o que você criou é, de fato, um **recurso de criação** e não um **recurso de previsão**.


* Valide o nome do recurso de criação e clique no botão **migrar agora** .

 > [!div class="mx-imgBorder"]
 > ![Criar recurso de criação](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

* A mensagem de êxito é mostrada. Selecione **fechar** para fechar a janela pop-up.

 > [!div class="mx-imgBorder"]
 > ![O recurso de criação foi criado com êxito.](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Usando aplicativos após a migração

* Após o processo de migração, todos os seus aplicativos LUIS de que você é proprietário serão atribuídos a um único recurso de criação de LUIS.
* A lista **meus aplicativos** mostra os aplicativos migrados para o novo recurso de criação.
* Antes de acessar seus aplicativos, selecione a assinatura e o recurso de criação de LUIS para ver os aplicativos que você pode criar.

 > [!div class="mx-imgBorder"]
 > ![Selecione assinatura e recurso de criação de LUIS para ver os aplicativos que você pode criar.](./media/create-app-in-portal-select-subscription-luis-resource.png)

* Você não precisa saber a chave do recurso de criação para continuar editando seus aplicativos no portal do LUIS.
* Se você planeja editar seus aplicativos programaticamente, precisará dos valores de chave de criação. Esses valores são exibidos na página **gerenciar > recursos do Azure** no portal do Luis e também estão disponíveis no portal do Azure na página **chaves** do recurso. Você também pode criar mais recursos de criação e atribuí-los da mesma página.

 > [!div class="mx-imgBorder"]
 > ![Gerenciar recurso de criação.](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="add-contributors-to-authoring-resources"></a>Adicionar colaboradores aos recursos de criação

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Saiba [como adicionar colaboradores](luis-how-to-collaborate.md) em seu recurso de criação. Os colaboradores terão acesso a todos os aplicativos sob esse recurso.

Você pode adicionar colaboradores ao recurso de criação do _portal do Azure_, na página de controle de **acesso (iam)** para esse recurso. Para obter mais informações, consulte [Adicionar acesso de colaborador](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

> [!Note]
> Se o proprietário do aplicativo LUIS migrou e adicionou o colaborador como um colaborador no recurso do Azure, o colaborador ainda não terá acesso ao aplicativo, a menos que eles também migrem.

## <a name="luis-portal-migration-reminders"></a>Lembretes de migração do portal LUIS

O [portal do Luis](https://www.luis.ai) fornece o processo de migração.

Você será solicitado a migrar se:
* Você tem aplicativos no sistema de autenticação de email para criação.
* E você é o proprietário do aplicativo.

Em uma base semanal, você será solicitado a migrar seus aplicativos. Você pode cancelar esta janela sem migrar. Se você quiser migrar antes do próximo período agendado, poderá iniciar o processo de migração no ícone **do Azure** na barra de ferramentas superior do portal do Luis.

Você pode atrasar o processo de migração cancelando a janela. Periodicamente, você será solicitado a migrar até migrar ou o prazo de migração for passado. Você pode iniciar o processo de migração no ícone de bloqueio da barra de navegação superior.

## <a name="prediction-resources-blocking-migration"></a>Recursos de previsão que bloqueiam a migração
Sua migração afeta negativamente qualquer tempo de execução de aplicativos. Ao migrar, todos os colaboradores são removidos dos seus aplicativos e você é removido como um colaborador de outros aplicativos. Esse processo significa que as chaves que um colaborador atribui também são removidas, o que pode interromper seu aplicativo se ele estiver em produção. Esse é o motivo pelo qual bloqueamos a migração até que você remova os colaboradores ou chaves atribuídos manualmente.

### <a name="when-does-prediction-resources-block-migration"></a>Quando os recursos de previsão bloqueiam a migração?
* A migração será bloqueada se você tiver atribuído recursos de previsão/tempo de execução em aplicativos que não são de sua propriedade.
* A migração será bloqueada se você tiver outros usuários que atribuem recursos de previsão/tempo de execução aos aplicativos que você possui.

### <a name="recommended-steps-to-do-if-you-are-the-owner-of-the-app"></a>Etapas recomendadas para fazer se você for o proprietário do aplicativo
Se você for um proprietário de alguns aplicativos e tiver colaboradores atribuídos à previsão/chave de tempo de execução para esses aplicativos, um erro será mostrado quando você migrar que lista as IDs de aplicativo que têm chaves de previsão atribuídas a elas pertencentes a outros usuários.

É recomendável:
* Notifique os colaboradores sobre a migração.
* Remova todos os colaboradores dos aplicativos mostrados no erro.
* Passará pelo processo de migração que deve ter sucesso se você remover manualmente colaboradores.
* Atribua colaboradores como colaboradores ao seu novo recurso de criação.
* Os colaboradores são migrar e reatribuir os recursos de previsão de volta aos aplicativos.
Observe que isso causará uma interrupção no aplicativo temporariamente até que os recursos de previsão sejam atribuídos novamente.

Outra solução aqui é, antes da migração do proprietário, os colaboradores podem adicionar proprietários de aplicativo como colaboradores em suas assinaturas do Azure da portal do Azure. Isso concederá ao proprietário acesso ao recurso de previsão de tempo de execução. Se o proprietário migrar usando a nova assinatura à qual foi adicionado (que será encontrado em um novo locatário), isso não apenas desbloqueará o processo de migração para o colaborador e o proprietário do aplicativo, mas permitirá uma migração tranqüila de aplicativos com a chave de previsão ainda atribuída a eles que não estão dividindo os aplicativos.


### <a name="recommended-steps-to-do-if-you-are-a-collaborator-on-an-app"></a>Etapas recomendadas para fazer se você for um colaborador em um aplicativo
Se você estiver colaborando em aplicativos e tiver atribuído a chave de previsão/tempo de execução para esses aplicativos, um erro será mostrado quando você migrar que lista as IDs de aplicativo e os caminhos de chave que estão bloqueando a migração.

É recomendável:
* Exportar aplicativos como backup. Isso é fornecido como uma etapa opcional no processo de migração.
* Cancele a atribuição dos recursos de previsão de **gerenciar-> página de recursos do Azure** .
* Passam pelo processo de migração.
* Importe aplicativos de volta após a migração.
* Reatribua as chaves de previsão à página **gerenciar recursos do > do Azure** para seus aplicativos.

> [!Note]
> Quando você importar os aplicativos de volta após a migração, eles terão IDs de aplicativo diferentes e serão diferentes daqueles que estão sendo atingidos na produção. Agora você será o proprietário desses aplicativos.

## <a name="troubleshooting-migration-process"></a>Solucionando problemas do processo de migração

Quando você tenta migrar, mas não consegue encontrar sua assinatura do Azure na lista suspensa:
* Verifique se você tem uma assinatura válida do Azure que está autorizada a criar recursos de serviços cognitivas. Vá para [portal do Azure](https://ms.portal.azure.com) e verifique o status da assinatura. Se você não tiver uma, [crie uma avaliação gratuita](https://azure.microsoft.com/free/).
* Verifique se você está no locatário adequado associado à sua assinatura válida. Você pode alternar locatários do avatar esquerdo adicional na barra de ferramentas abaixo: ![ alternar locatários.](./media/migrate-authoring-key/switch-user-tenant-2.png)

 Se você já tiver um recurso de criação existente, mas não puder encontrá-lo ao selecionar a opção "usar recurso de criação existente":
* O recurso provavelmente foi criado em um local diferente do portal no qual você fez logon. Verifique [as regiões de criação do Luis e os portais](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions)
* Crie um novo recurso no portal do LUIS em vez disso

Se você selecionar a opção "criar novo recurso de criação" e a migração falhar com a mensagem de erro "falha ao recuperar as informações do usuário do Azure, tente novamente mais tarde"
* Sua assinatura pode ter 10 ou mais recursos de criação por região por assinatura. Se esse for o caso, você não poderá criar um novo recurso de criação.
* Migre selecionando a opção "usar recurso de criação existente" e selecione um dos recursos existentes que você tem em sua assinatura.

Se você vir o erro a seguir, marque a seção [etapas recomendadas para fazer se você for o proprietário do aplicativo] ![ falha na migração para proprietários](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Se você vir o erro abaixo, marque a seção [etapas recomendadas para fazer se você for um colaborador em um aplicativo] ![ falha na migração para colaboradores](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Próximas etapas

* Examinar [conceitos](luis-concept-keys.md) sobre criação e chaves de tempo de execução
* Examine [como atribuir chaves](luis-how-to-azure-subscription.md) e adicionar [colaboradores](luis-how-to-collaborate.md)
