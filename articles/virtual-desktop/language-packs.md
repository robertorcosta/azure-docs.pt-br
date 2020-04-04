---
title: Instale pacotes de idiomas em VMs do Windows 10 no Windows Virtual Desktop - Azure
description: Como instalar pacotes de idiomas para VMs multi-sessão do Windows 10 no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c40df9d821e069e2cd5ff0c42d5841f6b9041c96
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80634029"
---
# <a name="install-language-packs"></a>Instalar pacotes de idiomas

Quando você configura as implantações do Windows Virtual Desktop internacionalmente, é uma boa ideia garantir que sua implantação suporte vários idiomas. Você pode instalar pacotes de idiomas em uma imagem de VM (Multi-Session M) do Windows 10 Enterprise para suportar quantos idiomas sua organização precisar. Este artigo lhe dirá como instalar pacotes de idiomas e capturar imagens que permitem que seus usuários escolham seus próprios idiomas de exibição.

Saiba mais sobre como implantar uma VM no Azure no [Criar uma máquina virtual do Windows em uma zona de disponibilidade com o portal Azure](../virtual-machines/windows/create-portal-availability-zone.md).

>[!NOTE]
>Este artigo se aplica às VMs multi-sessão do Windows 10 Enterprise.

## <a name="install-a-language-pack"></a>Instale um pacote de idiomas

Para criar uma imagem VM com pacotes de idiomas, primeiro você precisa instalar pacotes de idiomas em uma máquina e capturar uma imagem dela.

Para instalar pacotes de idiomas:

1. Faça login como um admin.
2. Certifique-se de que você instalou todas as atualizações mais recentes do Windows e da Windows Store.
3. Vá para **Configurações** > Tempo &**Região****de Idioma** > .
4. Em **País ou região,** selecione seu país ou região preferido no menu suspenso.
    Neste exemplo, vamos selecionar a **França,** como mostrado na captura de tela a seguir:

    ![Uma captura de tela da página Região. A região atualmente selecionada é a França.](media/region-page-france.png)

5. Depois disso, selecione **Idioma**e **selecione Adicionar um idioma**. Escolha o idioma que deseja instalar na lista e selecione **Next**.
6. Quando a **janela Instalar o idioma é** aberta, selecione a caixa de seleção rotulada Instalar o pacote de **idiomas e definir como meu idioma de exibição do Windows**.
7. Selecione **Instalar**.
8. Para adicionar vários idiomas ao mesmo tempo, **selecione Adicionar um idioma**e repita o processo para adicionar um idioma nas etapas 5 e 6. Repita este processo para cada idioma que deseja instalar. No entanto, você só pode definir um idioma como seu idioma de exibição de cada vez.

    Vamos fazer uma rápida demonstração visual. As imagens a seguir mostram como instalar os pacotes de idiomas francês e holandês e, em seguida, definir o francês como o idioma de exibição.

    ![Uma captura de tela da página Language no início do processo. O idioma de exibição do Windows selecionado é inglês.](media/language-page-default.png)

    ![Uma captura de tela da janela de seleção do idioma. O usuário inseriu "francês" na barra de pesquisa para encontrar os pacotes de idioma francês.](media/select-language-french.png)

    ![Uma captura de tela da página Instalar recursos de idioma. O francês é selecionado como a língua preferida. As opções selecionadas são "Definir meu idioma de exibição", "Instalar pacote de idiomas", "Reconhecimento de fala" e "Caligrafia".](media/install-language-features.png)

    Depois que seus pacotes de idiomas forem instalados, você deve ver os nomes dos seus pacotes de idiomas aparecerem na lista de idiomas.

    ![Uma captura de tela da página de idioma com os novos pacotes de idiomas instalados. Os pacotes de idiomas francês e holandês estão listados em "línguas preferidas".](media/language-page-complete.png)

9. Se uma janela aparecer pedindo para você sair da sessão. Assine, depois faça login de novo. Seu idioma de exibição deve agora ser o idioma selecionado.

10.  Vá para **Relógio do Painel de** > Controle**e** > **Região**.

11.  Quando a janela **Região** for aberta, selecione a guia **Administração** e selecione **'Copiar'.**

12.  Selecione as caixas de seleção rotuladas **como tela de boas-vindas e contas do sistema** e novas contas de **usuário**.

13.  Selecione **OK**.

14.  Uma janela será aberta e lhe dirá para reiniciar sua sessão. Selecione **Reiniciar agora**.

15.  Depois de ter assinado de volta, volte para o Relógio **do Painel de** > Controle**e** > **Região**.

16.  Selecione a guia **Administração.**

17.  Selecione **Alterar local do sistema**.

18. No menu suspenso em **Current system locale**, selecione o idioma local que deseja usar. Depois disso, selecione **OK**.

19. Selecione **Reiniciar agora** para reiniciar sua sessão mais uma vez.

Parabéns, você satisfez seus pacotes de idiomas!

Antes de continuar, certifique-se de que seu sistema tenha as versões mais recentes do Windows e da loja do Windows instaladas.

## <a name="sysprep"></a>Sysprep

Em seguida, você precisa sysprep sua máquina para prepará-la para o processo de captura de imagem.

Para sysprep sua máquina:

1. Abra o PowerShell como Administrador.
2. Execute o cmdlet a seguir para ir ao diretório correto:
   
    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Em seguida, execute o seguinte cmdlet:
    
    ```powershell
    .\sysprep.exe
    ```

4. Quando a janela da ferramenta de preparação do sistema for aberta, selecione a caixa de seleção rotulada **Generalize**e vá para **'Opções de desligamento'** e **selecione 'Desligar'** no menu suspenso.

>[!NOTE]
>O processo de preparação levará alguns minutos para terminar. À medida que a VM é desligada, sua sessão remota se desconectará.

### <a name="resolve-sysprep-errors"></a>Resolver erros de sysprep

Se você vir uma mensagem de erro durante o processo de sysprep, aqui está o que você deve fazer:

1. Abra **a unidade C** e vá para o **Windows** > **System32 Sysprep** > **Panther**e abra o arquivo **setuperr.**

   O texto no arquivo de erro dirá que você precisa desinstalar um pacote de idioma específico, como mostrado na imagem a seguir. Copie o nome do pacote de idiomas para o próximo passo.

   ![Uma captura de tela do arquivo setuperr. O texto com o nome do pacote é destacado em azul escuro.](media/setuperr-package-name.png)

2. Abra uma nova janela PowerShell e execute o cmdlet a seguir com o nome do pacote que você copiou na etapa 2 para remover o pacote de idiomas:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Verifique se você removeu o pacote `Remove-AppxPackage` executando o cmdlet novamente. Se você removeu o pacote com sucesso, você deve ver uma mensagem que diz que o pacote que você está tentando remover não está lá.

4. Corra `sysprep.exe` o cmdlet novamente.

## <a name="capture-the-image"></a>Capturar a imagem

Agora que seu sistema está pronto, você pode capturar uma imagem para que outros usuários possam começar a usar VMs com base no seu sistema sem ter que repetir o processo de configuração.

Para capturar uma imagem:

1. Vá para o portal Azure e selecione o nome da máquina configurada em [Instalar um pacote de idiomas](#install-a-language-pack) e [sysprep](#sysprep).

2. Selecione **Capturar**.

3. Digite um nome para sua imagem no campo **Nome** e atribua-a ao grupo de recursos usando o menu suspenso do **grupo Recurso,** conforme mostrado na imagem a seguir.

   ![Uma captura de tela da janela Criar imagem. O nome que o usuário deu a esta imagem de teste é "vmwvd-image-fr", e eles a atribuíram ao grupo de recursos "testwvdimagerg".](media/create-image.png)

4. Selecione **Criar**.

5. Aguarde alguns minutos para que o processo de captura termine. Quando a imagem estiver pronta, você deve ver uma mensagem na Central de Notificações informando que a imagem foi capturada.

Agora você pode implantar uma VM usando sua nova imagem. Ao implantar a VM, certifique-se de seguir as instruções em [Criar uma máquina virtual do Windows em uma zona de disponibilidade com o portal Azure](../virtual-machines/windows/create-portal-availability-zone.md).

### <a name="how-to-change-display-language-for-standard-users"></a>Como alterar o idioma de exibição para usuários padrão

Os usuários padrão podem alterar o idioma de exibição em suas VMs.

Para alterar o idioma de exibição:

1. Vá para **Configurações de idioma**. Se você não sabe onde isso está, você pode inserir o **Idioma** na barra de pesquisa no Menu Iniciar.

2. No menu suspenso do idioma do Windows, selecione o idioma que deseja usar como idioma de exibição.

3. Saia da sessão e volte. O idioma de exibição deve agora ser o selecionado na etapa 2.
