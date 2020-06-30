---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72065ce602c6d29255a3500e26d8c6f36a19ebed
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095131"
---
### <a name="register-your-ios-app-for-push-notifications"></a>Registrar seu aplicativo iOS para notificações por push

Para enviar notificações por push para um aplicativo iOS, registre seu aplicativo na Apple e também registre-se para notificações por push.  

1. Se você ainda não tiver registrado seu aplicativo, navegue até o [Portal de provisionamento iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) no Apple Developer Center. Entre no portal com sua ID da Apple, navegue até **Certificados, Identificadores e Perfis** e selecione **Identificadores**. Clique em **+** para registrar um novo aplicativo.

    ![Página de IDs do aplicativo do Portal de Provisionamento do iOS](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. Na tela **Registrar um Novo Identificador**, selecione o botão de opção **IDs do Aplicativo**. Depois selecione **Continuar**.

    ![Página de registro de nova ID no Portal de Provisionamento do iOS](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

1. Atualize os três valores a seguir para o novo aplicativo e selecione **Continuar**:

   * **Descrição**: Digite um nome descritivo para o seu aplicativo.

   * **ID do Pacote**: Digite uma ID do Pacote no formulário **com.<identificador_da_organização>.<nome_do_produto>** conforme mencionado no [Guia de Distribuição de Aplicativos](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Na captura de tela abaixo, o valor `mobcat` é usado como um identificador de organização e o valor **PushDemo** é usado como o nome do produto.

      ![Página de registro da ID do aplicativo no Portal de Provisionamento do iOS](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Notificações por Push**: Marque a opção **Notificações por Push** na seção **Funcionalidades**.

      ![Formulário para registrar uma nova ID de aplicativo](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Essa ação gerará sua ID do Aplicativo e solicitará que você confirme as informações. Selecione **Continuar** e, em seguida, selecione **Registrar** para confirmar a nova ID do Aplicativo.

      ![Confirmar nova ID do Aplicativo](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Depois de selecionar **Registrar**, você verá a nova ID do Aplicativo como um item de linha na página **Certificados, Identificadores e Perfis**.

1. Na página **Certificados, Identificadores e Perfis**, em **Identificadores**, localize o item de linha da ID do aplicativo que você criou. Em seguida, selecione a linha para exibir a tela **Editar a configuração da ID do aplicativo**.

#### <a name="creating-a-certificate-for-notification-hubs"></a>Criar um certificado para os Hubs de Notificação

Um certificado é necessário para habilitar o hub de notificação para trabalhar com o **APNs (Apple Push Notification Service)** e pode ser fornecido de duas maneiras:

1. [Criando um certificado push p12 que possa ser carregado diretamente no Hub de Notificação](#option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub) (*a abordagem original*)

1. [Criando um certificado p8 que possa ser usado para autenticação baseada em token](#option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication) (*a abordagem mais recente e recomendada*)

A abordagem mais recente tem vários benefícios, conforme documentado em [Autenticação baseada em token (HTTP/2) para APNs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification). São necessárias menos etapas, mas também são obrigatórias para cenários específicos. No entanto, as etapas foram fornecidas para ambas as abordagens, já que funcionarão para os fins deste tutorial.

##### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>OPÇÃO UM: Criação de um certificado push p12 que possa ser carregado diretamente no Hub de Notificação

1. Em seu Mac, execute a ferramenta Acesso do Conjunto de Chaves. Ela pode ser aberta na pasta **Utilitários** ou na pasta **Outros** no Launchpad.

1. Selecione **Acesso do Conjunto de Chaves**, expanda **Assistente de Certificado** e selecione **Solicitar um Certificado de uma Autoridade de Certificação**.

    ![Usar o Acesso de conjunto de chaves para solicitar um novo certificado](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Por padrão, o Acesso às Chaves seleciona o primeiro item na lista. Isso pode ser um problema se você estiver na categoria **Certificados** e a **Autoridade de Certificação de Relacionamentos do Desenvolvedor Mundial da Apple** não for o primeiro item da lista. Verifique se você tem um item não chave ou se a chave **Autoridade de Certificação de Relacionamentos do Desenvolvedor Mundial da Apple** está selecionada antes de gerar o CSR (Solicitação de Assinatura de Certificado).

1. Selecione seu **Endereço de Email do Usuário**, insira o valor do seu **Nome Comum**, verifique se você especificou **Salvo em disco** e, em seguida, selecione **Continuar**. Deixe **Endereço de Email de AC** em branco, pois isso não é necessário.

    ![Informações do certificado esperadas](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Insira um nome para o arquivo **CSR (Solicitação de Assinatura de Certificado)** em **Salvar como**, selecione a localização em **Onde** e clique em **Salvar**.

    ![Escolher um nome de arquivo para o certificado](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Essa ação salva o **arquivo CSR** na localização selecionada. O local padrão é a **área de trabalho**. Lembre-se do local escolhido para o arquivo.

1. De volta à página **Certificados, Identificadores e Perfis** no [portal de provisionamento do iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), role para baixo até a opção marcada **Notificações por Push** e selecione **Configurar** para criar o certificado.

    ![Página Editar ID do aplicativo](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. A janela **Certificados TLS/SSL do Apple Push Notification Service** é exibida. Selecione o botão **Criar Certificado** na seção **Certificado TLS/SSL de Desenvolvimento**.

    ![Botão Criar um certificado para aplicativo](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    A tela **Criar um novo Certificado** é exibida.

    > [!NOTE]
    > Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Use o mesmo tipo de certificado ao enviar notificações.

1. Selecione **Escolher Arquivo**, navegue até a localização em que salvou o **arquivo CSR** e clique duas vezes no nome do certificado para carregá-lo. Depois selecione **Continuar**.

1. Depois que o portal cria o certificado, selecione o botão **Baixar**. Salve o certificado e lembre-se do local em que ele foi salvo.

    ![Página de download do certificado gerado](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    O certificado é baixado e salvo no seu computador, na pasta **Downloads**.

    ![Localize o arquivo do certificado na pasta de Downloads](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Por padrão, o certificado de desenvolvimento baixado é denominado **aps_development.cer**.

1. Clique duas vezes no certificado de push baixado, **aps_development.cer**. Essa ação instala o novo certificado no conjunto de chaves, conforme mostrado na seguinte imagem:

    ![Lista de certificados de acesso ao conjunto de chaves mostrando o novo certificado](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Embora o nome em seu certificado possa ser diferente, esse nome será prefixado com **Serviços de Notificação por Push do iOS para Desenvolvimento da Apple** e terá o identificador de pacote apropriado associado a ele.

1. No Acesso ao Conjunto de Chaves, **Control** + **Clique** no novo certificado push criado na categoria **Certificados**. Selecione **Exportar**, nomeie o arquivo, selecione o formato **p12** e clique em **Salvar**.

    ![Exportar o certificado como formato p12](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Você pode optar por proteger o certificado com uma senha, mas a senha é opcional. Clique em **OK** caso deseje ignorar a criação de senha. Anote o nome do arquivo e a localização do certificado p12 exportado. Eles são usados para habilitar a autenticação com APNs.

    > [!NOTE]
    > O nome e a localização do arquivo p12 podem ser diferentes do que foi mostrado neste tutorial.

##### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>OPÇÃO DOIS: Criação de um certificado p8 que possa ser usado para a autenticação baseada em token

1. Anote os seguintes detalhes:

    * **Prefixo da ID do Aplicativo** (**ID da Equipe**)
    * **ID do Pacote**

1. Novamente em **Certificados, Identificadores e Perfis**, clique em **Chaves**.

   > [!NOTE]
   > Caso já tenha uma chave configurada para **APNs**, reutilize o certificado p8 que você baixou logo depois de criá-lo. Nesse caso, você pode ignorar desde a etapa **3** até a **5**.

1. Clique no botão **+** (ou no botão **Criar uma chave**) para criar uma chave.
1. Forneça um valor de **Nome de Chave** adequado, marque a opção **APNs (Apple Push Notification Service)** , clique em **Continuar** e, na próxima tela, clique em **Registrar**.
1. Clique em **Baixar**, mova o arquivo **p8** (prefixado com *AuthKey_* ) para um diretório local seguro e clique em **Concluído**.

   > [!NOTE]
   > Verifique se o arquivo p8 está em um local seguro (e salve um backup). Após o download da chave, não é possível baixá-la novamente, pois a cópia do servidor é removida.
  
1. Em **Chaves**, clique na chave que você criou (ou em uma já existente, caso tenha optado por usá-la).
1. Anote o valor da **ID da Chave**.
1. Abra o certificado p8 em um aplicativo de sua escolha, como o [**Visual Studio Code**](https://code.visualstudio.com). Anote o valor da chave (entre **-----BEGIN PRIVATE KEY-----** e **-----END PRIVATE KEY-----** ).

    -----BEGIN PRIVATE KEY-----  
    < key_value >  
    -----END PRIVATE KEY-----

    > [!NOTE]
    > Esse é o **valor do token** que será usado mais tarde para configurar o **Hub de Notificação**.

No final dessas etapas, você terá as seguintes informações para uso posterior em [Configurar o hub de notificação com informações da APNs](#configure-your-notification-hub-with-apns-information):

* A **ID da Equipe** (veja a etapa um)
* A **ID do Pacote** (veja a etapa um)
* A **ID da Chave** (veja a etapa sete)
* **Valor do token** (o valor da chave p8 obtido na etapa 8)

### <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de provisionamento para o aplicativo

1. Retorne ao [Portal de Provisionamento do iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecione **Certificados, Identificadores e Perfis**, selecione **Perfis** no menu à esquerda e selecione **+** para criar um novo perfil. A tela **Registrar um Novo Perfil de Provisionamento** é exibida.

1. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e selecione **Continuar**.

    ![Lista de perfil de provisionamento](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Em seguida, selecione a ID do aplicativo que você criou na lista suspensa **ID do Aplicativo** e selecione **Continuar**.

    ![Selecionar a ID do aplicativo](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Na janela **Selecionar certificados**, selecione o certificado de desenvolvimento que você usa para assinatura de código e selecione **Continuar**.

    > [!NOTE]
    > Esse certificado não é o certificado push que você criou na [etapa anterior](#creating-a-certificate-for-notification-hubs). Este é o certificado de desenvolvimento. Se não houver um, você deverá criá-lo porque este é um [pré-requisito](#prerequisites) para este tutorial. Os certificados de desenvolvedor podem ser criados no [Apple Developer Portal](https://developer.apple.com), por meio do [Xcode](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/YourFirstAppStoreSubmission/ProvisionYourDevicesforDevelopment/ProvisionYourDevicesforDevelopment.html) ou no [Visual Studio](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/).

1. Retorne à página **Certificados, Identificadores e Perfis**, selecione **Perfis** no menu à esquerda e selecione **+** para criar um novo perfil. A tela **Registrar um Novo Perfil de Provisionamento** é exibida.

1. Na janela **Selecionar certificados**, selecione o certificado de desenvolvimento que você criou. Depois selecione **Continuar**.

1. Em seguida, selecione os dispositivos a serem usados para teste e selecione **Continuar**.

1. Por fim, escolha um nome para o perfil em **Nome do Perfil de Provisionamento** e selecione **Gerar**.

    ![Escolher um nome do perfil de provisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Quando o novo perfil de provisionamento for criado, selecione **Baixar**. Lembre-se do local em que ele foi salvo.

1. Navegue até a localização do perfil de provisão e clique duas vezes nele para instalá-lo em seu computador de desenvolvimento.
