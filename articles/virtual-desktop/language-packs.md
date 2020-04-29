---
title: Instalar pacotes de idiomas em VMs do Windows 10 na área de trabalho virtual do Windows – Azure
description: Como instalar pacotes de idiomas para VMs de várias sessões do Windows 10 na área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c40df9d821e069e2cd5ff0c42d5841f6b9041c96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80634029"
---
# <a name="install-language-packs"></a>Instalar pacote de idiomas

Quando você configura implantações de área de trabalho virtual do Windows internacionalmente, é uma boa ideia garantir que a implantação dê suporte a vários idiomas. Você pode instalar pacotes de idiomas em uma imagem de VM (máquina virtual) de várias sessões do Windows 10 Enterprise para dar suporte a tantos idiomas quantos forem necessários à sua organização. Este artigo lhe dirá como instalar pacotes de idiomas e capturar imagens que permitem que os usuários escolham seus próprios idiomas de exibição.

Saiba mais sobre como implantar uma VM no Azure em [criar uma máquina virtual do Windows em uma zona de disponibilidade com o portal do Azure](../virtual-machines/windows/create-portal-availability-zone.md).

>[!NOTE]
>Este artigo se aplica a VMs de várias sessões do Windows 10 Enterprise.

## <a name="install-a-language-pack"></a>Instalar um pacote de idiomas

Para criar uma imagem de VM com pacotes de idiomas, primeiro você precisa instalar pacotes de idiomas em um computador e capturar uma imagem dele.

Para instalar os pacotes de idiomas:

1. Entre como administrador.
2. Verifique se você instalou todas as atualizações mais recentes do Windows e da Windows Store.
3. Acesse **configurações** > **hora &** > **região**de idioma.
4. Em **país ou região**, selecione seu país ou região preferencial no menu suspenso.
    Neste exemplo, vamos selecionar **França**, conforme mostrado na seguinte captura de tela:

    ![Uma captura de tela da página de região. A região selecionada atualmente é França.](media/region-page-france.png)

5. Depois disso, selecione **idioma**e, em seguida, selecione **Adicionar um idioma**. Escolha o idioma que você deseja instalar na lista e, em seguida, selecione **Avançar**.
6. Quando a janela **instalar recursos de idioma** for aberta, marque a caixa de seleção **instalar pacote de idiomas e definir como meu idioma de exibição do Windows**.
7. Selecione **Instalar**.
8. Para adicionar vários idiomas ao mesmo tempo, selecione **Adicionar um idioma**e repita o processo para adicionar um idioma nas etapas 5 e 6. Repita esse processo para cada idioma que você deseja instalar. No entanto, você só pode definir um idioma como seu idioma de exibição por vez.

    Vamos executar uma demonstração visual rápida. As imagens a seguir mostram como instalar os pacotes de idioma francês e holandês e, em seguida, definir o francês como o idioma de exibição.

    ![Uma captura de tela da página de idioma no início do processo. O idioma de exibição do Windows selecionado é o inglês.](media/language-page-default.png)

    ![Uma captura de tela da janela de seleção de idioma. O usuário inseriu "francês" na barra de pesquisa para localizar os pacotes de idioma em francês.](media/select-language-french.png)

    ![Uma captura de tela da página instalar recursos de idioma. O francês é selecionado como o idioma preferencial. As opções selecionadas são "definir meu idioma de exibição", "instalar pacote de idiomas", "reconhecimento de fala" e "manuscrito".](media/install-language-features.png)

    Depois que os pacotes de idiomas tiverem sido instalados, você deverá ver os nomes dos seus pacotes de idiomas aparecerem na lista de idiomas.

    ![Uma captura de tela da página de idioma com os novos pacotes de idiomas instalados. Os pacotes de idiomas francês e Holanda estão listados em "idiomas preferenciais".](media/language-page-complete.png)

9. Se uma janela for exibida solicitando que você saia da sessão. Saia e entre novamente. Seu idioma de exibição agora deve ser o idioma selecionado.

10.  Vá para a**região**de**relógio e região** > do **painel** > de controle.

11.  Quando a janela **região** for aberta, selecione a guia **Administração** e, em seguida, selecione **configurações de cópia**.

12.  Marque as caixas de seleção denominadas **tela de boas-vindas e contas do sistema** e **novas contas de usuário**.

13.  Selecione **OK**.

14.  Uma janela será aberta e informará que você deve reiniciar a sessão. Selecione **reiniciar agora**.

15.  Depois de entrar novamente, volte para a**região**de **Control Panel** > **relógio e região** > do painel de controle.

16.  Selecione a guia **Administração** .

17.  Selecione **alterar localidade do sistema**.

18. No menu suspenso, em **localidade atual do sistema**, selecione o idioma de localidade que você deseja usar. Depois disso, selecione **OK**.

19. Selecione **reiniciar agora** para reiniciar a sessão mais uma vez.

Parabéns, você instalou seus pacotes de idiomas!

Antes de continuar, verifique se o sistema tem as versões mais recentes do Windows e da Windows Store instaladas.

## <a name="sysprep"></a>Sysprep

Em seguida, você precisará fazer o Sysprep do seu computador para prepará-lo para o processo de captura de imagem.

Para Sysprep seu computador:

1. Abra o PowerShell como Administrador.
2. Execute o seguinte cmdlet para ir para o diretório correto:
   
    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Em seguida, execute o seguinte cmdlet:
    
    ```powershell
    .\sysprep.exe
    ```

4. Quando a janela da ferramenta de preparação do sistema for aberta, marque a caixa de seleção como **generalizar**, vá para **Opções de desligamento** e selecione **desligar** no menu suspenso.

>[!NOTE]
>O processo de syprep levará alguns minutos para ser concluído. À medida que a VM for desligada, sua sessão remota será desconectada.

### <a name="resolve-sysprep-errors"></a>Resolver erros do Sysprep

Se você vir uma mensagem de erro durante o processo do Sysprep, veja o que você deve fazer:

1. Abra a **unidade C** e vá para **Windows** > **System32 Sysprep** > **Panther**e, em seguida, abra o arquivo **Setuperr** .

   O texto no arquivo de erro informará que você precisa desinstalar um pacote de idioma específico, conforme mostrado na imagem a seguir. Copie o nome do pacote de idioma para a próxima etapa.

   ![Uma captura de tela do arquivo Setuperr. O texto com o nome do pacote é realçado em azul escuro.](media/setuperr-package-name.png)

2. Abra uma nova janela do PowerShell e execute o seguinte cmdlet com o nome do pacote que você copiou na etapa 2 para remover o pacote de idiomas:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Verifique se você removeu o pacote executando o `Remove-AppxPackage` cmdlet novamente. Se você tiver removido o pacote com êxito, verá uma mensagem que diz que o pacote que você está tentando remover não está lá.

4. Execute o `sysprep.exe` cmdlet novamente.

## <a name="capture-the-image"></a>Capturar a imagem

Agora que o sistema está pronto, você pode capturar uma imagem para que outros usuários possam começar a usar VMs com base em seu sistema sem precisar repetir o processo de configuração.

Para capturar uma imagem:

1. Vá para a portal do Azure e selecione o nome do computador que você configurou em [instalar um pacote de idiomas](#install-a-language-pack) e [Sysprep](#sysprep).

2. Selecione **capturar**.

3. Insira um nome para a imagem no campo **nome** e atribua-o ao grupo de recursos usando o menu suspenso **grupo de recursos** , conforme mostrado na imagem a seguir.

   ![Uma captura de tela da janela criar imagem. O nome que o usuário forneceu para esta imagem de teste é "vmwvd-Image-fr" e o atribuiu ao grupo de recursos "testwvdimagerg".](media/create-image.png)

4. Selecione **Criar**.

5. Aguarde alguns minutos para que o processo de captura seja concluído. Quando a imagem estiver pronta, você verá uma mensagem no centro de notificações informando que a imagem foi capturada.

Agora você pode implantar uma VM usando sua nova imagem. Ao implantar a VM, certifique-se de seguir as instruções em [criar uma máquina virtual do Windows em uma zona de disponibilidade com o portal do Azure](../virtual-machines/windows/create-portal-availability-zone.md).

### <a name="how-to-change-display-language-for-standard-users"></a>Como alterar o idioma de exibição para usuários padrão

Os usuários padrão podem alterar o idioma de exibição em suas VMs.

Para alterar o idioma de exibição:

1. Vá para **configurações de idioma**. Se você não souber onde está, poderá inserir o **idioma** na barra de pesquisa no menu iniciar.

2. No menu suspenso Idioma de exibição do Windows, selecione o idioma que você deseja usar como seu idioma de exibição.

3. Saia da sessão e entre novamente. O idioma de exibição agora deve ser aquele que você selecionou na etapa 2.
