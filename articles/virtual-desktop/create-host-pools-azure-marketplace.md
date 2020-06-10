---
title: Portal do Azure do pool de host da Área de Trabalho Virtual do Windows – Azure
description: Como criar um pool de host da Área de Trabalho Virtual do Windows usando o portal do Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a6ac2fd0ef3414f5b9d7f6551628264b4724b037
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234326"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>Tutorial: Criar um pool de host com o portal do Azure

>[!IMPORTANT]
>Este conteúdo se aplica à atualização da Spring 2020 com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows na versão 2019, sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md). Todos os objetos que você criar com a Área de Trabalho Virtual do Windows Fall 2019 não poderão ser gerenciados com o portal do Azure.
>
> A atualização 2020 da Área de Trabalho Virtual do Windows está em versão prévia pública no momento. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendamos usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. 
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pools de hosts são uma coleção de uma ou mais VMs (máquinas virtuais) idênticas dentro dos ambientes da Área de Trabalho Virtual do Windows. Cada pool de hosts pode conter um grupo de aplicativo com o qual os usuários podem interagir como se eles estivessem em uma área de trabalho física.

Este artigo explicará o processo de instalação para criar um pool de host para um ambiente de Área de Trabalho Virtual do Windows por meio do portal do Azure. Esse método fornece uma interface do usuário baseada em navegador para criar um pool de host na Área de Trabalho Virtual do Windows, criar um grupo de recursos com VMs em uma assinatura do Azure, ingressar essas VMs no domínio do Azure AD (Active Directory) e registrar as VMs com a Área de Trabalho Virtual do Windows.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará inserir os seguintes parâmetros para criar um pool de host:

- O nome da imagem da VM
- Configuração da VM
- Propriedades de domínio e rede
- Propriedades do pool de host da Área de Trabalho Virtual do Windows

Você também precisará saber o seguinte:

- O local em que a origem da imagem que você deseja usar está. É da galeria do Azure ou é uma imagem personalizada?
- Suas credenciais de ingresso no domínio.

Além disso, verifique se você registrou o provedor de recursos Microsoft.DesktopVirtualization. Se você ainda não fez isso, acesse **Assinaturas**, selecione o nome da assinatura e, em seguida, selecione **Provedores de recursos do Azure**.

Ao criar um pool de host da Área de Trabalho Virtual do Windows com o modelo do Azure Resource Manager, você pode criar uma máquina virtual na galeria do Azure, uma imagem gerenciada ou uma imagem não gerenciada. Para saber mais sobre como criar imagens da VM, confira [Preparar um VHD ou VHDX do Windows para carregar no Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) e [Criar uma imagem gerenciada de uma VM generalizada no Azure](../virtual-machines/windows/capture-image-resource.md).

Se você ainda não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar a seguir estas instruções.

## <a name="begin-the-host-pool-setup-process"></a>Iniciar o processo de instalação do pool de host

Para começar a criar seu pool de host:

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

2. Insira **Área de Trabalho Virtual do Windows** na barra de pesquisa e, em seguida, localize e selecione **Área de Trabalho Virtual do Windows** em serviços.

3. Na página visão geral da **Área de Trabalho Virtual do Windows**, selecione **Criar um pool de host**.

4. Na guia **Noções básicas**, selecione a assinatura correta em Detalhes do projeto.

5. Selecione **Criar** para criar um grupo de recursos ou selecione um grupo de recursos no menu suspenso.

6. Insira um nome exclusivo para seu pool de host.

7. No campo Localização, selecione a região em que você deseja criar o pool de host no menu suspenso.
   
   Os metadados para esse pool de host e seus objetos relacionados serão armazenados na geografia do Azure associada às regiões que você selecionou. Escolha as regiões dentro da geografia em que você deseja que os metadados de serviço sejam armazenados.

     ![Uma captura de tela do portal do Azure mostrando o campo Localização com a localização Leste dos EUA selecionada. Ao lado do campo está o texto que diz "Os metadados serão armazenados no Leste dos EUA".](media/portal-location-field.png)

8. Em Tipo de pool de host, selecione se o pool de host será **Pessoal** ou **Em pool**.

    - Se você escolher **Pessoal**, selecione **Automático** ou **Direto** no campo Tipo de Atribuição.

      ![Uma captura de tela do menu suspenso do campo de tipo de atribuição. O usuário selecionou Automático.](media/assignment-type-field.png)

9. Se você escolher **Em pool**, insira as seguintes informações:

     - Para **Limite máximo de sessão**, insira o número máximo de usuários cuja carga você deseja balancear para um host de sessão.
     - Para **Algoritmo de balanceamento de carga**, escolha amplitude primeiro ou profundidade primeiro conforme seu padrão de uso.

       ![Uma captura de tela do campo de tipo de atribuição com "Em pool" selecionado. O usuário está focalizando o cursor sobre Amplitude primeiro no menu suspenso de balanceamento de carga.](media/pooled-assignment-type.png)

10. Selecione **Avançar: Detalhes da VM**.

11. Se você já tiver criado máquinas virtuais e quiser usá-las com o novo pool de host, selecione **Não**. Se você quiser criar máquinas virtuais e registrá-las no novo pool de host, selecione **Sim**.

Agora que você concluiu a primeira parte, vamos passar para a próxima parte do processo de instalação em que criamos a VM.

## <a name="virtual-machine-details"></a>Detalhes da máquina virtual

Agora que estamos na primeira parte, você precisará configurar sua VM.

Para configurar sua máquina virtual dentro do processo de configuração do pool de host:

1. Em Grupo de Recursos, escolha o grupo de recursos no qual você deseja criar as máquinas virtuais. Esse pode ser um grupo de recursos diferente daquele usado para o pool de host.

2. Escolha a **Região da máquina virtual** em que você deseja criar as máquinas virtuais. Podem ser iguais ou diferentes da região selecionada para o pool de host.

3. Em seguida, escolha o tamanho da máquina virtual que você deseja criar. Você pode manter o tamanho padrão no estado em que se encontra ou selecionar **Alterar tamanho** para alterar o tamanho. Se você selecionar **Alterar tamanho**, na janela exibida, escolha o tamanho da máquina virtual adequado para sua carga de trabalho.

4. Em Número de VMs, forneça o número de VMs que você deseja criar para o pool de host.

    >[!NOTE]
    >O processo de instalação pode criar até 400 VMs durante a configuração do pool de host. Cada processo de instalação da VM cria quatro objetos em seu grupo de recursos. Como os processos de criação não verificam sua cota de assinatura, verifique se o número de VMs inseridas está dentro dos limites de API e de VM do Azure para seu grupo de recursos e assinatura. Você pode adicionar mais VMs depois de concluir a criação do pool de host.

5. Depois disso, forneça um **Prefixo de nome** para nomear as máquinas virtuais que o processo de configuração cria. O sufixo será `-` com números começando em 0.

6. Em seguida, escolha a imagem que precisa ser usada para criar a máquina virtual. Você pode escolher **Galeria** ou **Blob de Armazenamento**.

    - Se você escolher **Galeria**, selecione uma das imagens recomendadas no menu suspenso:

      - Sessão múltipla do Windows 10 Enterprise, versão 1909 + Office 365 ProPlus – Gen 1
      - Windows 10 Enterprise de várias sessões, versão 1909 – Gen 1
      - Windows Server 2019 Datacenter – Gen 1

     Se você não vir a imagem desejada, selecione **Procurar todas as imagens e discos**, o que permite que você selecione outra imagem na galeria ou uma imagem fornecida pela Microsoft e outros editores.

     ![Uma captura de tela do Marketplace com uma lista de imagens da Microsoft exibida.](media/marketplace-images.png)

     Você também pode ir até **Meus Itens** e escolher uma imagem personalizada que você já carregou.

     ![Uma captura de tela da guia Meus Itens.](media/my-items.png)

    - Se você escolher **Blob de Armazenamento**, poderá aproveitar seu build de imagem por meio do Hyper-V ou em uma VM do Azure. Basta inserir a localização da imagem no blob de armazenamento como um URI.

7. Escolha o tipo de discos do SO que você deseja que suas VMs usem: SSD Standard, SSD Premium ou HDD Standard.

8. Em Rede e Segurança, selecione a rede virtual e a sub-rede em que você deseja colocar as máquinas virtuais criadas. Verifique se a rede virtual consegue se conectar ao controlador de domínio, pois você precisará ingressar as máquinas virtuais dentro da rede virtual ao domínio. Em seguida, selecione se deseja ou não um IP público para as máquinas virtuais. Recomendamos que você selecione **Não**, pois um IP privado é mais seguro.

9. Selecione o tipo de grupo de segurança desejado: **Básico**, **Avançado** ou **Nenhum**.

    Se você selecionar **Básico**, precisará selecionar se deseja que qualquer porta de entrada seja aberta. Se você selecionar **Sim**, escolha na lista de portas padrão para permitir conexões de entrada.

    >[!NOTE]
    >Para maior segurança, recomendamos não abrir portas de entrada públicas.

    ![Uma captura de tela da página do grupo de segurança que mostra uma lista de portas disponíveis em um menu suspenso.](media/available-ports.png)
    
    Se você escolher **Avançado**, selecione um grupo de segurança de rede existente que você já configurou.

10. Depois disso, selecione se deseja que as máquinas virtuais sejam ingressadas em um domínio específico e em uma unidade organizacional. Se você escolher **Sim**, especifique o domínio para ingressar. Você também pode adicionar uma unidade organizacional específica na qual deseja que as máquinas virtuais estejam.

11. Em Conta do administrador, insira as credenciais para o administrador da rede virtual do Domínio do Active Directory que você selecionou.

12. Selecione **Workspace**.

Com isso, estamos prontos para iniciar a próxima fase de configuração do pool de host: registrar seu grupo de aplicativos em um workspace.

## <a name="workspace-information"></a>Informações do workspace

O processo de instalação do pool de host cria um grupo de aplicativos de desktop por padrão. Para que o pool de host funcione como pretendido, você precisará publicar esse grupo de aplicativos para usuários ou grupos de usuários e registrar o grupo de aplicativos em um workspace. 

Para registrar o grupo de aplicativos da área de trabalho em um workspace:

1. Selecione **Sim** na barra superior.

   Se você selecionar **Não**, poderá registrar o grupo de aplicativos mais tarde, mas recomendamos que você faça o registro do workspace assim que puder para que seu pool de host funcione corretamente.

2. Em seguida, escolha se deseja criar um workspace ou selecionar um dos workspaces existentes. Somente workspaces criados na mesma localização que o pool de host terão permissão para registrar o grupo de aplicativos.

3. Opcionalmente, você pode selecionar **Marcas**.

    Aqui, você pode adicionar marcas para que possa agrupar os objetos a metadados para facilitar o trabalho dos administradores.

4. Quando terminar, selecione **Review + create**. 

     >[!NOTE]
     >O processo de examinar + criar validação não verifica se a sua senha cumpre os padrões de segurança ou se sua arquitetura está correta, portanto, você precisará verificar se há algum problema com algum desses itens. 

5. Examine as informações sobre sua implantação para verificar se tudo está correto. Quando terminar, selecione **Criar**. Isso inicia o processo de implantação, que cria os seguintes objetos:

     - Seu novo pool de host.
     - Um grupo de aplicativos da área de trabalho.
     - Um workspace, se você optar por criá-lo.
     - Se você optar por registrar o grupo de aplicativos da área de trabalho, o registro será concluído
     - Máquinas virtuais, se você optar por criá-las, que são ingressadas no domínio e registradas com o novo pool de host.
     - Um link de download para um modelo de gerenciamento de recursos do Azure com base em sua configuração.

Depois disso, está tudo pronto.

## <a name="next-steps"></a>Próximas etapas

Agora que você já criou seu pool de host, você pode preenchê-lo com programas RemoteApp. Para saber mais sobre como gerenciar aplicativos na Área de Trabalho Virtual do Windows, veja nosso próximo tutorial:

> [!div class="nextstepaction"]
> [Tutorial Gerenciar grupos de aplicativos](./manage-app-groups.md)
