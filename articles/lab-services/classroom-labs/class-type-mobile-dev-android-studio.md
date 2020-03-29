---
title: Criar um laboratório para ensinar desenvolvimento de aplicativos móveis com Android Studio
titleSuffix: Azure Lab Services
description: Aprenda a configurar um laboratório para ensinar a aula de desenvolvimento de aplicativos móveis de dados que usa o Android Studio.  O artigo também discutirá ajustes a serem realizados ao usar o Android Studio em uma máquina virtual no Azure.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849780"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Criar um laboratório para ensinar o desenvolvimento de aplicativos móveis de dados com o Android Studio

Este artigo mostrará como configurar uma classe de desenvolvimento de aplicativos móveis introdutórios.  Esta classe se concentra nos aplicativos para dispositivos móveis Android que podem ser publicados na [Google Play Store](https://play.google.com/store/apps).  Os alunos aprendem como usar o [Android Studio](https://developer.android.com/studio) para criar aplicativos.  [O emulador do Visual Studio para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) é usado para testar o aplicativo localmente.

## <a name="lab-configuration"></a>Configuração do laboratório

Para configurar este laboratório, você precisa de uma assinatura do Azure e uma conta de laboratório para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você pode criar uma nova conta de laboratório no Azure Lab Services. Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [o tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).  Você também pode usar uma conta de laboratório existente.

Siga o tutorial de [laboratório de sala de aula](tutorial-setup-classroom-lab.md) para criar um novo laboratório e, em seguida, aplique as seguintes configurações:

| Tamanho da máquina virtual | Imagem |
| -------------------- | ----- |
| Média (Virtualização Aninhado) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Configuração da máquina de modelo

Quando a criação da máquina de modelo estiver concluída, [inicie a máquina e conecte-se a ela para](how-to-create-manage-template.md#update-a-template-vm) completar as seguintes tarefas:

1. Adicionar função Hyper-V
2. Baixe e instale Java.  
3. Baixe e instale o Visual Studio Emulator para Android.
4. Baixe e instale o Android Studio.
5. Configure o Visual Studio Emulator para Android Studio.

## <a name="add-hyper-v-role"></a>Adicionar função Hyper-V

O Hyper-V deve ser habilitado para a instalação bem-sucedida do Visual Studio Emulador para Android.  Siga as instruções [de como ativar a virtualização aninhada em um artigo de máquina virtual de modelo.](how-to-enable-nested-virtualization-template-vm.md)

## <a name="install-java"></a>Instalar o Java

Android Studio requer Java.  Siga os passos abaixo para baixar a versão mais recente do Java.

1. Navegue até a [página de download java](https://www.java.com/download/). Clique no botão **Java Download.**
2. Na página web do Windows for Java de 64 bits, clique no botão denominado **Concordar e Iniciar o Download Gratuito**.
3. Quando o instalador **de configuração Java** for exibido, clique **em Instalar**.
4. Aguarde até que o título do instalador mude para **Configuração Java – Concluído**.  Clique **em Fechar** o botão.

## <a name="install-visual-studio-emulator-for-android"></a>Instale o Visual Studio Emulador para Android

Para testar um aplicativo Android localmente, ele deve usar uma versão virtualizada de um dispositivo Android.  Existem alguns emuladores android disponíveis que permitirão que um desenvolvedor teste seu aplicativo a partir de sua máquina.  Estamos usando o Visual Studio Emmulator para Android porque é um emulador que suporta virtualização aninhada.  Como a VM de Serviço de Laboratório já é uma máquina virtual, precisamos de um emulador que suporte a virtualização aninhada.  O emulador embutido para Android Studio não suporta virtualização aninhada.  Para ver quais emuladores suportam a virtualização aninhada, consulte [a aceleração do hardware para o desempenho do emulador (Hyper-V & HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration).

Use as seguintes instruções para baixar e instalar o Visual Studio Emulator para Android.

1. Navegue até [o Visual Studio Emulator para a](https://visualstudio.microsoft.com/vs/msft-android-emulator/) página inicial do Android.
2. Clique no **botão Baixar o Emulador.**
3. Quando vs_emulatorsetup.exe for baixado, execute o executável.
4. Quando a caixa de diálogo de configuração do Visual Studio for exibida, clique no botão **Instalar.**
5. Aguarde a conclusão do instalador.  Clique no botão **Reiniciar agora** para reiniciar o computador e terminar a instalação.

Inicie o emulador primeiro antes de implantar seu aplicativo usando o Android Studio.  Para obter mais informações sobre o Emulador visual studio para Android, consulte [Visual Studio Emulador para documentação do Android](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android).

## <a name="install-android-studio"></a>Instale o Android Studio

Siga as instruções abaixo para baixar e instalar [o Android Studio](https://developer.android.com/studio).

1. Navegue até a [página de download do Android Studio](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > O Internet Explorer não é suportado por este site.
2. Clique no pacote Android Studio executável do Windows (64 bits).
3. Leia os termos legais escritos no pop-up.  Quando estiver pronto para continuar, verifique **se li e concordo com os termos e condições acima** e clique no botão Baixar Android Studio para **Windows.**
4. Uma vez que na configuração do Android Studio executável é baixado, execute o executável.
5. Na página **de configuração do Estúdio De Boas-vindas ao Android** do instalador de configuração do Android **Studio,** clique em **Next**.
6. Na página **Configurações configurações,** clique **em Next**.
7. Na página **'Escolher menu',** clique em **Instalar**.
8. Aguarde a configuração para ser concluída.
9. Na página **Instalação Completa,** clique **em Next**.
10. Na página **De configuração do Android Studio.**  Clique em **concluir**.
11. O Android Studio será iniciado automaticamente após o término da configuração.
12. Na **marcação Importar configurações do Android De...** diálogo, **selecione Não importar configurações**. Clique em **OK**.
13. Na página **de boas-vindas** do Assistente de **Configuração do Estúdio Android**, clique em **Next**.
14. Na página **Tipo de instalação,** escolha **Padrão**. Clique em **Avançar**.
15. Na página **Selecionar tema da UI,** selecione o tema desejado. Clique em **Avançar**.
16. Na **página 'Verificar configurações',** clique **em 'Avançar**' .
17. Na página **Downloadde Components,** aguarde até que todos os componentes sejam baixados.  Clique em **concluir**.

    > [!IMPORTANT]
    > Espera-se que a instalação do HAXM falhe.  HaxM não suporta virtualização aninhada, e é por isso que instalamos o Visual Studio Emular para Android no início deste artigo.

18. A caixa de diálogo **Welcome to Android Studio** aparecerá quando o assistente de configuração estiver concluído.

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Configure Android Studio e Visual Studio Emulador para Android

Android Studio está quase pronto para uso.  Ainda precisamos dizer ao Visual Studio Emulator para Android onde o Android SDK está instalado.  Isso fará com que todos os emuladores em execução no Visual Studio para Android show como alvos de implantação para depuração Android Studio.

Precisamos definir uma chave de registro específica para dizer visual studio emulador para Android onde o Android Sdk está localizado.  Para definir a chave de registro necessária, execute o script abaixo.  O script PowerShell abaixo assume o local de instalação padrão para o Android Sdk.  Se você instalou seu Android Sdk em `$androidSdkPath` outro local, modifique o valor para antes de executar o script.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Reiniciar o Visual Studio Emulator para Android e Android Studio para que a nova configuração seja usada.

Inicie a versão que você precisa no Emulador do Estúdio Visual.  Ele aparecerá como um alvo de implantação para o seu aplicativo Android no estúdio Android.  A versão mínima para o projeto Android Studio deve suportar a versão em execução no Visual Studio Emulador para Android.  Agora você está pronto para criar e depurar projetos usando Android Studio e Visual Studio Emulator para Android.  Se você tiver algum problema, consulte a solução de problemas do emulador do Android.

## <a name="cost"></a>Custo

Se você quiser estimar o custo deste laboratório, você pode seguir o exemplo abaixo.
Para uma turma de 25 alunos com 20 horas de aula programadas e 10 horas de cota para lição de casa ou tarefas, o preço para o laboratório seria  

25 \* alunos (20 horas programadas + 10 cotas) * 55 Unidades de Laboratório * 0,01 USD por hora = 412,5 USD

Mais detalhes sobre preços, consulte [Preços de Serviços do Laboratório Azure](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Próximas etapas

Os próximos passos são comuns à criação de qualquer laboratório.

- [Criar e gerenciar um modelo](how-to-create-manage-template.md)
- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Defina um cronograma](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição por e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)
