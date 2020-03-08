---
title: Conectar-se de forma privada a um aplicativo Web usando o ponto de extremidade privado do Azure
description: Conectar-se de forma privada a um aplicativo Web usando o ponto de extremidade privado do Azure
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: f19e4e34b2ec8cebc9e1841f277f26fba941bb89
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673921"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Conectar-se de forma privada a um aplicativo Web usando o ponto de extremidade privado do Azure (versão prévia)

O ponto de extremidade privado do Azure é o bloco de construção fundamental para o link privado no Azure. Ele permite que você se conecte em particular ao seu aplicativo Web.
Neste guia de início rápido, você aprenderá como implantar um aplicativo Web com um ponto de extremidade privado e conectar-se a esse aplicativo Web de uma máquina virtual.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Rede virtual e máquina virtual

Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu aplicativo Web por meio do ponto de extremidade privado.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e uma sub-rede.

1. No lado superior esquerdo da tela, selecione **criar um recurso** > **rede** > **rede virtual** ou pesquise pela **rede virtual** na caixa de pesquisa.

1. Em **criar rede virtual**, insira ou selecione essas informações na guia noções básicas:

 ![Criar Rede Virtual][1]

1. Clique em **"Avançar: endereços IP >"** e insira ou selecione estas informações:

![Configurar endereços IP][2]

1. Na seção sub-rede, clique em **"+ Adicionar sub-rede"** e insira as informações a seguir e clique em **"Adicionar"**

![Adicionar sub-rede][3]

1. Clique em **"revisar + criar"**

1. Depois que a validação for aprovada, clique em **"criar"**

### <a name="create-virtual-machine"></a>Criar máquina virtual

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **computação** > **máquina virtual**

1. Em criar uma máquina virtual-noções básicas, insira ou selecione estas informações:

![Máquina virtual básica ][4]

1. Selecione **"Avançar: discos"**

Mantenha as configurações padrão.

1. Selecione **"Avançar: rede"** , selecione estas informações:

![Rede ][5]

1. Clique em **"revisar + criar"**

1. Quando a mensagem de validação for aprovada, clique em **"criar"**

## <a name="create-your-web-app-and-private-endpoint"></a>Criar seu aplicativo Web e ponto de extremidade privado

Nesta seção, você criará um aplicativo Web privado usando um ponto de extremidade privado para ele.

### <a name="web-app"></a>Aplicativo Web

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **Web** > **aplicativo Web**

1. Em criar aplicativo Web-noções básicas, insira ou selecione estas informações:

![Aplicativo Web básico ][6]

1. Selecione **"revisar + criar"**

1. Quando a mensagem de validação for aprovada, clique em **"criar"**

### <a name="create-the-private-endpoint"></a>Criar o ponto de extremidade privado

1. Nas propriedades do aplicativo Web, selecione **configurações** > **rede** e clique em **"configurar suas conexões de ponto de extremidade privado"**

![Rede do aplicativo Web][7]

1. No assistente, clique em **"+ Adicionar"**

![Ponto de extremidade particular do aplicativo Web][8]

1. Preencha as informações de assinatura, vnet e sub-rede e clique em **"OK"**

![Rede do aplicativo Web][9]

1. Examinar a criação do ponto de extremidade privado

![examinar][10]
![exibição final do ponto de extremidade privado][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

1. Na barra de pesquisa do portal, insira **myVm**
1. Selecione o **botão conectar**. Depois de selecionar o botão conectar, conectar à máquina virtual é aberto, selecione **RDP**

![Botão RDP][12]

1. O Azure cria um arquivo de protocolo RDP (. RDP) e o baixa em seu computador depois que você clica em **baixar arquivo RDP**

![Baixar arquivo RDP][13]

1. Abra o arquivo. rdp baixado.

- Se solicitado, selecione conectar.
- Insira o nome de usuário e a senha que você especificou ao criar a VM.

> [!Note]
> Talvez seja necessário selecionar mais escolhas > usar uma conta diferente, para especificar as credenciais inseridas quando você criou a VM.

- Selecione OK.

1. Você pode receber um aviso do certificado durante o processo de logon. Se você receber um aviso de certificado, selecione Sim ou continuar.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.

## <a name="access-web-app-privately-from-the-vm"></a>Acessar o aplicativo Web de forma privada da VM

Nesta seção, você se conectará de forma privada ao aplicativo Web usando o ponto de extremidade privado.

1. Obtenha o IP privado do seu ponto de extremidade privado, no **link privado**do tipo de barra de pesquisa e selecione link privado

![Link Privado][14]

1. No centro de links privado, selecione **pontos de extremidade privados** para listar todos os seus pontos de extremidade privados

![Centro de links privado][15]

1. Selecione o link do ponto de extremidade privado para seu aplicativo Web e sua sub-rede

![Propriedades do ponto de extremidade privado][16]

1. Copie o IP privado do seu ponto de extremidade privado e o FQDN do seu aplicativo Web, no nosso caso, webappdemope.azurewebsites.net 10.10.2.4

1. No myVM, verifique se o aplicativo Web não está acessível por meio do IP público. Abra um navegador e copie o nome do aplicativo Web, você deve ter uma página de erro 403 Proibido

![Proibido][17]

> [!Note]
> Como esse recurso está em versão prévia, você precisa gerenciar manualmente a entrada DNS.

1. Criar a entrada de host, abrir o explorador de arquivos e localizar o arquivo de hosts

![Arquivo de hosts][18]

1. Adicione uma entrada com o endereço IP privado e o nome público do seu aplicativo Web editando o arquivo de hosts com o bloco de notas

![Hospeda conteúdo][19]

1. Salve o arquivo

1. Abra um navegador e digite a URL do seu aplicativo Web

![Site com PE][20]

1. Você está acessando seu aplicativo Web por meio do ponto de extremidade privado

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você terminar de usar o ponto de extremidade privado, o aplicativo Web e a VM, exclua o grupo de recursos e todos os recursos que ele contém:

1. Digite Ready-RG na caixa de pesquisa na parte superior do portal e selecione Ready-RG nos resultados da pesquisa.
1. Selecione Excluir grupo de recursos.
1. Digite Ready-RG para digitar o nome do grupo de recursos e selecione Excluir.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma VM em uma rede virtual, um aplicativo Web e um ponto de extremidade privado. Você se conectou a uma VM da Internet e se comunica com segurança ao aplicativo Web usando o link privado. Para saber mais sobre o ponto de extremidade privado, confira [o que é o ponto de extremidade privado do Azure][privateendpoint].

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtualmachine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png

<!--Links-->
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
