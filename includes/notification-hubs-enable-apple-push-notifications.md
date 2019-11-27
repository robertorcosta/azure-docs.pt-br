---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/21/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: ef2b98821b28d8a49e5f16bf1c6ac176eb8b5793
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407262"
---
## <a name="generate-the-certificate-signing-request-file"></a>Gerar o arquivo de solicitação de assinatura de certificado

O APNs (Serviço de Notificação por Push da Apple) usa certificados para autenticar suas notificações por push. Siga estas instruções para criar o certificado de push necessário para enviar e receber notificações. Para obter mais informações sobre esses conceitos, consulte a documentação oficial do [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Gere o arquivo CSR (Solicitação de Assinatura de Certificado), usado pela Apple para gerar um certificado de push assinado.

1. Em seu Mac, execute a ferramenta Acesso do Conjunto de Chaves. Ela pode ser aberta na pasta **Utilitários** ou na pasta **Outros** no Launchpad.

1. Selecione **Acesso do Conjunto de Chaves**, expanda **Assistente de Certificado** e selecione **Solicitar um Certificado de uma Autoridade de Certificação**.

    ![Usar o Acesso de conjunto de chaves para solicitar um novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. Selecione seu **Endereço de Email do Usuário**, insira o valor do seu **Nome Comum**, verifique se você especificou **Salvo em disco** e, em seguida, selecione **Continuar**. Deixe **Endereço de Email de AC** em branco, pois isso não é necessário.

    ![Informações de certificado necessárias](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Insira um nome para o arquivo CSR em **Salvar Como**, selecione o local em **Onde** e, em seguida, selecione **Salvar**.

    ![Escolher um nome de arquivo para o certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Essa ação salva o arquivo CSR no local selecionado. O local padrão é a **área de trabalho**. Lembre-se do local escolhido para o arquivo.

Em seguida, registre seu aplicativo na Apple, habilite as notificações por push e carregue o CSR exportado para criar um certificado de push.

## <a name="register-your-app-for-push-notifications"></a>Registrar seu aplicativo para notificações por push

Para enviar notificações por push para um aplicativo iOS, registre seu aplicativo na Apple e também registre-se para notificações por push.  

1. Se você ainda não tiver registrado seu aplicativo, navegue até o [Portal de provisionamento iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) no Apple Developer Center. Conecte-se ao portal com sua ID da Apple e selecione **Identificadores**. Em seguida, selecione **+** para registrar um novo aplicativo.

    ![Página de IDs do aplicativo do Portal de Provisionamento do iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Na tela **Registrar um Novo Identificador**, selecione o botão de opção **IDs do Aplicativo**. Depois selecione **Continuar**.

    ![Página de registro de nova ID no Portal de Provisionamento do iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Atualize os três valores a seguir para o novo aplicativo e selecione **Continuar**:

   * **Descrição**: Digite um nome descritivo para o seu aplicativo.

   * **ID do Pacote**: Digite uma ID do Pacote no formulário **Identificador da Organização.Nome do Produto** conforme mencionado no [Guia de Distribuição de Aplicativos](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Os valores *Identificador de Organização* e *Nome do Produto* deverão corresponder ao identificador da organização e o nome do produto usados quando você criar seu projeto Xcode. Na captura de tela abaixo, o valor **NotificationHubs** é usado como um identificador de organização e o valor **GetStarted** é usado como o nome do produto. Verifique se o valor de **Identificador de Pacote** corresponde ao valor em seu projeto do Xcode, para que o Xcode use o perfil de publicação correto.

      ![Página de registro da ID do aplicativo no Portal de Provisionamento do iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Notificações por Push**: Marque a opção **Notificações por Push** na seção **Funcionalidades**.

      ![Formulário para registrar uma nova ID de aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Essa ação gerará sua ID do Aplicativo e solicitará que você confirme as informações. Selecione **Continuar** e, em seguida, selecione **Registrar** para confirmar a nova ID do Aplicativo.

      ![Confirmar nova ID do Aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Depois de selecionar **Registrar**, você verá a nova ID do Aplicativo como um item de linha na página **Certificados, Identificadores e Perfis**.

4. Na página **Certificados, Identificadores e Perfis**, em **Identificadores**, localize o item de linha da ID do Aplicativo que você acabou de criar e selecione sua linha para exibir a tela **Editar sua Configuração de ID do Aplicativo**.

5. Role para baixo até a opção marcada **Notificações por Push** e, em seguida, selecione **Configurar** para criar o certificado.

    ![Página Editar ID do aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

6. A janela **Certificados SSL do Apple Push Notification service** é exibida. Selecione o botão **Criar Certificado** na seção **Certificado SSL de Desenvolvimento**.

    ![Botão Criar um certificado para aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    A tela **Criar um novo Certificado** é exibida.

    > [!NOTE]
    > Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Use o mesmo tipo de certificado ao enviar notificações.

1. Selecione **Escolher Arquivo**, navegue até o local em que salvou o arquivo CSR da primeira tarefa e clique duas vezes no nome do certificado para carregá-lo. Depois selecione **Continuar**.

1. Depois que o portal cria o certificado, selecione o botão **Baixar**. Salve o certificado e lembre-se do local em que ele foi salvo.

    ![Página de download do certificado gerado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    O certificado é baixado e salvo no seu computador, na pasta **Downloads**.

    ![Localize o arquivo do certificado na pasta de Downloads](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Por padrão, o certificado de desenvolvimento baixado é denominado **aps_development.cer**.

1. Clique duas vezes no certificado de push baixado, **aps_development.cer**. Essa ação instala o novo certificado no conjunto de chaves, conforme mostrado na seguinte imagem:

    ![Lista de certificados de acesso ao conjunto de chaves mostrando o novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Embora o nome em seu certificado possa ser diferente, esse nome será prefixado com **Serviços de Notificação por Push do iOS para Desenvolvimento da Apple**.

1. No Acesso ao Conjunto de Chaves, clique com o botão direito do mouse no novo certificado push criado na categoria **Certificados** . Selecione **Exportar**, nomeie o arquivo, selecione o formato **.p12** e selecione **Salvar**.

    ![Exportar o certificado como formato p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Você pode optar por proteger o certificado com uma senha, mas isso é opcional. Clique em **OK** caso deseje ignorar a criação de senha. Anote o nome do arquivo e o local do certificado .p12 exportado. Eles são usados para habilitar a autenticação com APNs.

    > [!NOTE]
    > O nome e o local do arquivo. p12 podem ser diferentes do que foi mostrado neste tutorial.

## <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de provisionamento para o aplicativo

1. Retorne ao [Portal de Provisionamento do iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecione **Certificados, Identificadores e Perfis**, selecione **Perfis** no menu à esquerda e selecione **+** para criar um novo perfil. A tela **Registrar um Novo Perfil de Provisionamento** é exibida.

1. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e selecione **Continuar**.

    ![Lista de perfil de provisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Em seguida, selecione a ID do aplicativo que você criou na lista suspensa **ID do Aplicativo** e selecione **Continuar**.

    ![Selecionar a ID do aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Na janela **Selecionar certificados**, selecione o certificado de desenvolvimento que você usa para assinatura de código e selecione **Continuar**. Esse certificado não é o certificado push que você criou. Se não houver um, você deverá criá-lo. Se houver um certificado, pule para a próxima etapa. Para criar um certificado de desenvolvimento, caso não exista um:

    1. Se você vir **Nenhum certificado disponível**, selecione **Criar Certificado**.
    2. Na seção **Software**, selecione **Desenvolvimento da Apple**. Depois selecione **Continuar**.
    3. Na tela **Criar um Novo Certificado**, selecione **Escolher Arquivo**.
    4. Navegue até o certificado **Solicitação de Assinatura de Certificado** criado anteriormente, selecione-o e, em seguida, selecione **Abrir**.
    5. Selecione **Continuar**.
    6. Baixe o certificado de desenvolvimento e lembre-se do local em que ele foi salvo.

1. Retorne à página **Certificados, Identificadores e Perfis**, selecione **Perfis** no menu à esquerda e selecione **+** para criar um novo perfil. A tela **Registrar um Novo Perfil de Provisionamento** é exibida.

1. Na janela **Selecionar certificados**, selecione o certificado de desenvolvimento que você acabou de criar. Depois selecione **Continuar**.

1. Em seguida, selecione os dispositivos a serem usados para teste e selecione **Continuar**.

1. Por fim, escolha um nome para o perfil em **Nome do Perfil de Provisionamento** e selecione **Gerar**.

    ![Escolher um nome do perfil de provisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Quando o novo perfil de provisionamento for criado, selecione **Baixar**. Lembre-se do local em que ele foi salvo.

1. Navegue até o local do perfil de provisionamento e clique duas vezes nele para instalá-lo em seu computador de desenvolvimento do Xcode.

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

Nesta seção, você cria um hub de notificação e configura a autenticação com APNs usando o certificado push .p12 que você criou anteriormente. Se você quiser usar um hub de notificação já criado, passe diretamente à etapa 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Configure seu hub de notificação com as informações de APNs

1. Em **Serviços de Notificação**, selecione **Apple (APNS)** .

1. Selecione **Certificado**.

1. Selecione o ícone arquivo.

1. Selecione o arquivo .p12 que você exportou anteriormente e, em seguida, clique em **Abrir**.

1. Caso seja necessário, especifique a senha correta.

1. Selecione o modo **Sandbox**. Use o modo **Produção** apenas se quiser enviar notificações por push aos usuários que adquiriram seu aplicativo na loja.

    ![Configurar certificação de APNs no portal do Azure](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Clique em **Salvar**.

Agora você configurou seu hub de notificações com APNs. Você também tem as cadeias de conexão para registrar seu aplicativo e enviar notificações por push.
