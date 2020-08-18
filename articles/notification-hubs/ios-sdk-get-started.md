---
title: Enviar notificações por push para o iOS usando os Hubs de Notificação do Azure e o SDK do iOS
description: Neste tutorial, você aprenderá a usar os Hubs de Notificação do Azure e o Apple Push Notification Service para enviar notificações por push para dispositivos iOS específicos.
author: sethmanheim
ms.author: sethm
ms.date: 08/10/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: 7cdf095898bfe85e6f3b14fa1dcdb7b0c94ccde6
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042438"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Tutorial: Enviar notificações por push para aplicativos iOS usando Hubs de Notificação do Microsoft Azure

Este tutorial mostra como configurar os Hubs de Notificação do Azure e configurar credenciais para enviar notificações por push a um dispositivo iOS por meio do [APNS (Apple Push Notification Service)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1). 

A execução deste tutorial é um pré-requisito para os tutoriais subsequentes do Objective C e do Swift iOS e aborda as seguintes etapas:

- Gerar o arquivo de solicitação de assinatura de certificado.
- Solicitar notificações por push ao aplicativo.
- Criar um perfil de provisão para o aplicativo.
- Criar um hub de notificação.
- Configurar o hub de notificação com as informações de APNS.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/free/).

Você também precisará do seguinte:

- Uma conta de [Desenvolvedor da Apple](https://developer.apple.com/) ativa.
- Um Mac executando [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), juntamente com um certificado de desenvolvedor válido instalado em seu conjunto de chaves.
- Um iPhone ou iPad executando o iOS versão 10 ou posterior.
- Seu dispositivo físico registrado no [portal da Apple](https://developer.apple.com/) e associado ao seu certificado.

Leia a [Visão geral dos Hubs de Notificação do Azure](notification-hubs-push-notification-overview.md) se você não estiver familiarizado com o serviço.

> [!NOTE]
> O hub de notificação será configurado para usar somente o modo de autenticação de Área restrita. Não use esse modo de autenticação para cargas de trabalho de produção.

## <a name="generate-the-certificate-signing-request-file"></a>Gerar o arquivo de solicitação de assinatura de certificado

O APNS (Serviço de Notificação por Push da Apple) usa certificados para autenticar suas notificações por push. Siga estas instruções para criar o certificado de push necessário para enviar e receber notificações. Para obter mais informações sobre esses conceitos, confira a documentação oficial do [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) .

Gere o arquivo CSR (Solicitação de Assinatura de Certificado), usado pela Apple para gerar um certificado de push assinado:

1. Em seu Mac, execute a ferramenta Acesso do Conjunto de Chaves. Ele pode ser aberto na pasta **Utilitários** ou na pasta **Outros** no Launchpad.

2. Selecione **Acesso a Conjunto de Chaves**, expanda **Assistente de Certificado**e selecione **Solicitar um Certificado de uma Autoridade de Certificação**.

   :::image type="content" source="media/ios-sdk-get-started/image1.png" alt-text="Acesso às Chaves":::

   > [!NOTE]
   > Por padrão, o Acesso às Chaves seleciona o primeiro item na lista. Isso pode ser um problema se você estiver na categoria **Certificados** e a **Autoridade de Certificação de Relacionamentos do Desenvolvedor Mundial da Apple** não for o primeiro item da lista. Verifique se você tem um item não chave ou se a chave **Autoridade de Certificação de Relacionamentos do Desenvolvedor Mundial da Apple** está selecionada antes de gerar o CSR (Solicitação de Assinatura de Certificado).

3. Selecione o **Endereço de Email do Usuário**, insira o valor do **Nome Comum** , verifique se você especificou **Salvo em disco**e escolha **Continuar**. Deixe **Endereço de Email de AC** em branco, pois essa informação não é necessária.

   :::image type="content" source="media/ios-sdk-get-started/image2.png" alt-text="Informações de certificado necessárias":::

4. Insira um nome para o arquivo CSR em **Salvar Como**, selecione a localização em **Onde**e escolha **Salvar**.

   :::image type="content" source="media/ios-sdk-get-started/image3.png" alt-text="Escolher nome de arquivo":::

   Essa ação salva o arquivo CSR no local selecionado. A localização padrão é a **Área de trabalho**. Lembre-se do local escolhido para o arquivo.

Em seguida, registre seu aplicativo na Apple, habilite as notificações por push e carregue o CSR exportado para criar um certificado de push.

## <a name="register-your-app-for-push-notifications"></a>Registrar seu aplicativo para notificações por push

Para enviar notificações por push para um aplicativo iOS, registre seu aplicativo na Apple e também registre-se para notificações por push.

1. Se você ainda não tiver registrado seu aplicativo, navegue até o [Portal de Provisionamento do iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) no Centro de Desenvolvedor da Apple. Entre no portal com sua ID da Apple e selecione **Identificadores**. Em seguida, selecione **+**  para registrar um novo aplicativo.

   :::image type="content" source="media/ios-sdk-get-started/image4.png" alt-text="Página de IDs do aplicativo":::

2. Na tela **Registrar um Novo Identificador** , selecione o botão de opção **IDs do Aplicativo** . Em seguida, selecione **Continuar**.

   :::image type="content" source="media/ios-sdk-get-started/image5.png" alt-text="Página Registrar nova ID":::

3. Atualize os seguintes três valores para o novo aplicativo e selecione **Continuar**:

   - **Descrição**: Digite um nome descritivo para o seu aplicativo.
   - **ID do Pacote**: digite uma ID do Pacote no formulário **Identificador da Organização.Nome do Produto** conforme mencionado no [Guia de Distribuição de Aplicativos](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Os valores **Identificador de Organização** e **Nome do Produto** deverão corresponder ao identificador da organização e ao nome do produto que você usar quando criar seu projeto Xcode. Na captura de tela abaixo, o valor **NotificationHubs** é usado como um identificador de organização e o valor **GetStarted** é usado como o nome do produto. Verifique se o valor de **Identificador de Pacote** corresponde ao valor em seu projeto do Xcode, para que o Xcode use o perfil de publicação correto.

      :::image type="content" source="media/ios-sdk-get-started/image6.png" alt-text="Registrar ID do aplicativo":::

   - **Notificações por Push**: Marque a opção **Notificações por Push** na seção **Funcionalidades** .

      :::image type="content" source="media/ios-sdk-get-started/image7.png" alt-text="Registrar nova ID do aplicativo":::

      Essa ação gerará sua ID do Aplicativo e solicitará que você confirme as informações. Selecione **Continuar** e escolha **Registrar** para confirmar a nova ID do Aplicativo.

      :::image type="content" source="media/ios-sdk-get-started/image8.png" alt-text="Confirmar nova ID do Aplicativo":::

      Depois de selecionar **Registrar**, você verá a nova ID do Aplicativo como um item de linha na página **Certificados, Identificadores e Perfis** .

4. Na página **Certificados, Identificadores e Perfis** , em **Identificadores**, localize o item de linha da ID do Aplicativo que você acabou de criar e selecione sua linha para exibir a tela **Editar sua Configuração de ID do Aplicativo** .

## <a name="create-a-certificate-for-notification-hubs"></a>Criar um certificado para os Hubs de Notificação

> [!NOTE]
> Com o lançamento do iOS 13, só é possível receber notificações silenciosas usando a autenticação baseada em token. Se você estiver usando a autenticação baseada em certificado para suas credenciais do APNs, precisará alternar para o uso da autenticação baseada em token.

Um certificado é necessário para habilitar que o hub de notificação funcione com o **APNS**. Isso pode ser feito de duas maneiras:

- Pela criação de um arquivo **.p12** que possa ser carregado diretamente nos Hubs de Notificação.

- Pela criação de um arquivo **.p8** que possa ser usado para a [autenticação baseada em token](notification-hubs-push-notification-http2-token-authentication.md) (abordagem mais recente).

A segunda opção tem vários benefícios em comparação ao uso de certificados, conforme documentado em [Autenticação baseada em token (HTTP/2) para o APNS](notification-hubs-push-notification-http2-token-authentication.md). No entanto, etapas foram fornecidas para ambas as abordagens.

### <a name="option-1-create-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hubs"></a>Opção 1: Criação de um certificado push .p12 que possa ser carregado diretamente nos Hubs de Notificação

1. Role para baixo até a opção marcada **Notificações por Push** e selecione **Configurar** para criar o certificado.

   :::image type="content" source="media/ios-sdk-get-started/image9.png" alt-text="ID do Aplicativo":::

2. A janela **Certificados SSL do Apple Push Notification Service** é exibida. Selecione o botão **Criar Certificado** na seção **Certificado SSL de Desenvolvimento** .

   :::image type="content" source="media/ios-sdk-get-started/image10.png" alt-text="Criar certificado":::

   A tela **Criar um Certificado** é exibida.

   > [!NOTE]
   > Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Use o mesmo tipo de certificado ao enviar notificações.

3. Selecione **Escolher Arquivo**, navegue até a localização em que você salvou o arquivo CSR da primeira tarefa e clique duas vezes no nome do certificado para carregá-lo. Em seguida, selecione **Continuar**.

4. Depois que o portal criar o certificado, selecione o botão **Baixar** . Salve o certificado e lembre-se do local em que ele foi salvo.

   :::image type="content" source="media/ios-sdk-get-started/image11.png" alt-text="Baixar certificado":::

   O certificado é baixado e salvo em sua pasta **Downloads** .

   :::image type="content" source="media/ios-sdk-get-started/image12.png" alt-text="Localizar arquivo de certificado":::

   Por padrão, o certificado de desenvolvimento baixado é denominado **aps_development.cer**.

5. Clique duas vezes no certificado de push baixado, nomeado **aps\_development.cer**. Essa ação instala o novo certificado no conjunto de chaves, conforme mostrado na seguinte imagem:

   :::image type="content" source="media/ios-sdk-get-started/image13.png" alt-text="Acesso às Chaves":::

   Embora o nome em seu certificado possa ser diferente, esse nome será prefixado com **Serviços de Notificação por Push do iOS para Desenvolvimento da Apple**.

6. No Acesso às Chaves, clique com o botão direito do mouse no novo certificado push criado na categoria **Certificados** . Selecione **Exportar**, nomeie o arquivo, selecione o formato **.p12** e selecione **Salvar**.

   :::image type="content" source="media/ios-sdk-get-started/image14.png" alt-text="Exportar o certificado":::

   Você pode optar por proteger o certificado com uma senha, mas isso é opcional. Clique em **OK** caso deseje ignorar a criação de senha. Anote o nome do arquivo e o local do certificado .p12 exportado. Eles são usados para habilitar a autenticação com o APNS.

   > [!NOTE]
   > O nome e o local do arquivo. p12 podem ser diferentes do que foi mostrado neste tutorial.

### <a name="option-2-create-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>Opção 2: Criação de um certificado .p8 que possa ser usado para a autenticação baseada em token

1. Anote os seguintes detalhes:

   - **Prefixo da ID do Aplicativo** (é uma **ID da Equipe**)
   - **ID do Pacote**

2. Novamente em **Certificados, Identificadores e Perfis**, clique em **Chaves**. Caso já tenha uma chave configurada para **APNs**, você pode usar novamente o certificado .p8 que você baixou logo depois de criá-lo. Nesse caso, você pode ignorar as etapas 3 a 5.

3. Clique no botão **+**  (ou no botão **Criar uma chave** ) para criar uma chave.

4. Forneça um valor de **Nome de Chave** adequado, marque a opção **APNS (Apple Push Notifications Service)**  , clique primeiro em **Continuar**e depois, na próxima tela, em **Registrar** .

5. Clique em **Baixar** , mova o arquivo **.p8** (prefixado com `AuthKey_`) para um diretório local seguro e clique em **Concluído**.

   > [!IMPORTANT]
   > Verifique se o arquivo .p8 está em um local seguro (e salve um backup). Após o download da chave, não é possível baixá-la novamente, pois a cópia do servidor é removida.

6. Em **Chaves**, clique na chave que você acabou de criar (ou em uma já existente caso você tenha optado por usá-la em vez disso).

7. Anote o valor da **ID da chave** .

8. Abra o certificado .p8 em um aplicativo de sua escolha, como o [Visual Studio Code](https://code.visualstudio.com/), depois anote o valor da chave. Esse é o valor entre **-----BEGIN PRIVATE KEY-----**  e **-----END PRIVATE KEY-----**  .

   ```p8
   -----BEGIN PRIVATE KEY-----
   <key_value>
   -----END PRIVATE KEY-----
   ```

   Esse é o valor do token que será usado mais tarde para configurar os Hubs de Notificação.

No final dessas etapas, você deveria ter as seguintes informações para uso posterior em [Configurar o hub de notificação com informações do APNS](#configure-the-notification-hub-with-apns-information):

- A**ID da Equipe** (confira a etapa 1)
- A**ID do Pacote** (confira a etapa 1)
- A**ID da Chave** (confira a etapa 7)
- O**Valor do token** (o valor da chave .p8, confira a etapa 8)

## <a name="create-a-provisioning-profile"></a>Criar um perfil de provisão

1. Retorne ao [Portal de Provisionamento do iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecione **Certificados, Identificadores e Perfis**, selecione **Perfis** no menu à esquerda e selecione **+**  para criar um perfil. A tela **Registrar um Novo Perfil de Provisão** é exibida.

2. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisão e selecione **Continuar**.

   :::image type="content" source="media/ios-sdk-get-started/image15.png" alt-text="Lista de perfis de provisão":::

3. Em seguida, selecione a ID do aplicativo que você criou na lista suspensa **ID do Aplicativo** e selecione **Continuar**.

   :::image type="content" source="media/ios-sdk-get-started/image16.png" alt-text="Selecionar a ID do aplicativo":::

4. Na janela **Selecionar certificados** , selecione o certificado de desenvolvimento que você usa para assinatura de código e selecione **Continuar**. Esse certificado não é o certificado push que você criou. Se não houver um, você deverá criá-lo. Se houver um certificado, pule para a próxima etapa. Para criar um certificado de desenvolvimento, caso não exista um:

   1. Se você vir **Nenhum certificado disponível**, selecione **Criar Certificado**.
   2. Na seção **Software** , selecione **Desenvolvimento da Apple**. Em seguida, selecione **Continuar**.
   3. Na tela **Criar um Certificado** , selecione **Escolher Arquivo**.
   4. Navegue até o certificado **Solicitação de Assinatura de Certificado** criado anteriormente, selecione-o e escolha **Abrir**.
   5. Selecione **Continuar**.
   6. Baixe o certificado de desenvolvimento e lembre-se da localização em que ele foi salvo.

5. Retorne à página **Certificados, Identificadores e Perfis** , selecione **Perfis** no menu à esquerda e selecione **+**  para criar um perfil. A tela **Registrar um Novo Perfil de Provisão** é exibida.

6. Na janela **Selecionar certificados** , selecione o certificado de desenvolvimento que você acabou de criar. Em seguida, selecione **Continuar**.

7. Em seguida, selecione os dispositivos a serem usados para teste e selecione **Continuar**.

8. Por fim, escolha um nome para o perfil em **Nome do Perfil de Provisionamento**e selecione **Gerar**.

   :::image type="content" source="media/ios-sdk-get-started/image17.png" alt-text="Escolher um nome do perfil de provisão":::

9. Quando o perfil de provisão for criado, selecione **Baixar**. Lembre-se da localização em que ele foi salvo.

10. Navegue até o local do perfil de provisionamento e clique duas vezes nele para instalá-lo em seu computador de desenvolvimento do Xcode.

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

Nesta seção, você cria um hub de notificação e configura a autenticação com o APNS usando ou o certificado push .p12 ou a autenticação baseada em token. Se você quiser usar um hub de notificação já criado, passe diretamente à etapa 5.

1. Faça login no  [ portal do Azure ](https://portal.azure.com/).

2. Selecione **Todos os serviços** no menu à esquerda e **Hubs de Notificação** na seção **Celular** . Selecione o ícone de estrela ao lado do nome do serviço para adicionar o serviço à seção **FAVORITOS** no menu à esquerda. Depois de adicionar os **Hubs de Notificação** a **FAVORITOS**, selecione essa opção.

   :::image type="content" source="media/ios-sdk-get-started/image18.png" alt-text="Azure portal":::

3. Na página **Hubs de Notificação** , selecione **Adicionar** na barra de ferramentas.

   :::image type="content" source="media/ios-sdk-get-started/image19.png" alt-text="Botão de barra de ferramentas Adicionar":::

4. Na página **Hubs de Notificação** , execute as seguintes etapas:

   1. Insira um nome em **Hub de Notificação**.
   2. Insira um nome em **Criar um namespace**. Um namespace contém um ou mais Hubs de Notificação.
   3. Selecione um valor na lista suspensa **Localização** . Esse valor especifica a localização na qual você deseja criar o hub de notificação.
   4. Selecione um grupo de recursos em **Grupo de Recursos**ou crie um.
   5. Selecione **Criar**.

   :::image type="content" source="media/ios-sdk-get-started/image20.png" alt-text="Definir propriedades":::

5. Selecione **Notificações** (ícone de sino) e **Ir para o recurso**. Atualize também a lista na página **Hubs de Notificação** e selecione o seu hub.

   :::image type="content" source="media/ios-sdk-get-started/image21.png" alt-text="Notificações do portal":::

6. Selecione **Políticas de Acesso** na lista. Observe que as cadeias de caracteres de duas conexão estão disponíveis para você. Você precisará delas para manipular notificações por push mais tarde.

   > [!IMPORTANT]
   > Não use a política **DefaultFullSharedAccessSignature** no aplicativo. Ela é destinada a ser usada apenas em seu back-end.

   :::image type="content" source="media/ios-sdk-get-started/image22.png" alt-text="Cadeias de conexão":::

## <a name="configure-the-notification-hub-with-apns-information"></a>Configurar o hub de notificação com informações do APNS

Em **Notification Services**, selecione **Apple (APNS)** e siga as etapas apropriadas com base na abordagem que você selecionou antes na seção [Criar um certificado para os Hubs de Notificação](#create-a-certificate-for-notification-hubs) .

> [!NOTE]
> Use **Produção** no **Modo de Aplicativo** somente caso queira enviar notificações por push aos usuários que tenham comprado seu aplicativo da loja.

### <a name="option-1-use-a-p12-push-certificate"></a>Opção 1: Usar um certificado push .p12

1. Selecione **Certificado**.

2. Selecione o ícone arquivo.

3. Selecione o arquivo .p12 que você exportou anteriormente e escolha **Abrir**.

4. Caso seja necessário, especifique a senha correta.

5. Selecione o modo de **Área restrita** .

   :::image type="content" source="media/ios-sdk-get-started/image23.png" alt-text="Configurar":::

6. Selecione **Salvar**.

### <a name="option-2-use-token-based-authentication"></a>Opção 2: Usar autenticação baseada em token

1. Selecione **Token**.

2. Insira os seguintes valores que você adquiriu antes:

   - **ID da Chave**
   - **ID do Pacote**
   - **ID da Equipe**
   - **Token**

3. Escolha **Área restrita**

4. Selecione **Salvar**.

Agora você configurou seu hub de notificações com o APNS. Você também tem as cadeias de conexão necessárias para registrar seu aplicativo e enviar notificações por push.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um hub de notificação no Azure e o configurou de modo a permitir que as notificações sejam enviadas ao seu aplicativo por meio do APNS (Apple Push Notification Service). Em seguida, criaremos um aplicativo iOS de exemplo e integraremos o SDK dos Hubs de Notificação do Azure para que ele possa receber notificações por push enviadas por meio do portal do Azure. Avance para o seguinte tutorial com base no idioma de sua escolha:

- [Tutorial: Integrar com um aplicativo iOS usando o Swift]()
