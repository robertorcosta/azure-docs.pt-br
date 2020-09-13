---
title: Conecte-se em particular a um aplicativo Web usando o Ponto de extremidade privado do Azure
description: Este artigo explica como se conectar de forma privada a um aplicativo Web usando o ponto de extremidade privado do Azure
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 09/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 3d547546c3c0e0bbcdde65a654bf373ab7407be3
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569434"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Conectar-se de forma privada a um aplicativo Web usando o ponto de extremidade privado do Azure (versão prévia)

O ponto de extremidade privado do Azure é o bloco de construção fundamental para o link privado no Azure. Ele permite que você se conecte em particular ao seu aplicativo Web.
Neste guia de início rápido, você aprenderá como implantar um aplicativo Web com um ponto de extremidade privado e conectar-se a esse aplicativo Web de uma máquina virtual.

Para obter mais informações, consulte [usando pontos de extremidade privados para o aplicativo Web do Azure][privatenedpointwebapp].

> [!Note]
>A versão prévia está disponível em regiões públicas para aplicativos Web Windows e Linux PremiumV2 e funções Premium elásticos. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Rede virtual e máquina virtual

Nesta seção, você criará a rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu aplicativo Web por meio do ponto de extremidade privado.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e uma sub-rede.

1. No lado superior esquerdo da tela, selecione **criar um recurso**  >  **rede**  >  **Virtual Network** ou Pesquisar **rede virtual** na caixa de pesquisa.

1. Em **Criar rede virtual**, insira ou selecione estas informações na guia Básico:

   > [!div class="mx-imgBorder"]
   > ![Criar rede virtual][1]

1. Clique em **"Avançar: endereços IP >"** e insira ou selecione estas informações:

   > [!div class="mx-imgBorder"]
   >![Configurar endereços IP][2]

1. Na seção sub-rede, clique em **"+ Adicionar sub-rede"** e insira as informações a seguir e clique em **"Adicionar"**

   > [!div class="mx-imgBorder"]
   >![Adicionar sub-rede][3]

1. Clique em **"revisar + criar"**

1. Depois que a validação for aprovada, clique em **"criar"**

### <a name="create-virtual-machine"></a>Criar máquina virtual

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso**  >  **computação**  >  **máquina virtual**

1. Em Criar uma máquina virtual – Noções básicas, insira ou selecione estas informações:

   > [!div class="mx-imgBorder"]
   >![Máquina virtual básica ][4]

1. Selecione **"Avançar: discos"**

   Mantenha as configurações padrão.

1. Selecione **"Avançar: rede"**, selecione estas informações:

   > [!div class="mx-imgBorder"]
   >![Rede][5]

1. Clique em **"revisar + criar"**

1. Quando a mensagem de validação for aprovada, clique em **"criar"**

## <a name="create-your-web-app-and-private-endpoint"></a>Criar seu aplicativo Web e ponto de extremidade privado

Nesta seção, você criará um aplicativo Web privado usando um ponto de extremidade privado para ele.

> [!Note]
>O recurso de ponto de extremidade privado só está disponível para a SKU Premium v2.

### <a name="web-app"></a>Aplicativo Web

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso**  >  **Web**  >  **aplicativo Web**

1. Em criar aplicativo Web-noções básicas, insira ou selecione estas informações:

   > [!div class="mx-imgBorder"]
   >![Aplicativo Web básico ][6]

1. Selecione **"revisar + criar"**

1. Quando a mensagem de validação for aprovada, clique em **"criar"**

### <a name="create-the-private-endpoint"></a>Criar o ponto de extremidade privado

1. Nas propriedades do aplicativo Web, selecione **configurações**  >  **rede** e clique em **"configurar suas conexões de ponto de extremidade privado"**

   > [!div class="mx-imgBorder"]
   >![Rede do aplicativo Web][7]

1. No assistente, clique em **"+ Adicionar"**

   > [!div class="mx-imgBorder"]
   >![Ponto de extremidade particular do aplicativo Web][8]

1. Preencha as informações de assinatura, VNet e sub-rede e clique em **"OK"**

   > [!div class="mx-imgBorder"]
   >![Rede do aplicativo Web][9]

1. Examinar a criação do ponto de extremidade privado

   > [!div class="mx-imgBorder"]
   >![Examinar ][10]
   > ![ a exibição final do ponto de extremidade privado][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

1. Na barra de pesquisa do portal, insira **myVm**
1. Selecione o **botão conectar**. Depois de selecionar o botão conectar, conectar à máquina virtual é aberto, selecione **RDP**

   > [!div class="mx-imgBorder"]
   >![Botão RDP][12]

1. O Azure cria um arquivo de protocolo RDP (. RDP) e o baixa em seu computador depois que você clica em **baixar arquivo RDP**

   > [!div class="mx-imgBorder"]
   >![Baixar arquivo RDP][13]

1. Abra o arquivo. rdp baixado.

   - Se solicitado, selecione Conectar.
   - Insira o nome de usuário e a senha que você especificou ao criar a VM.

   > [!Note]
   > Talvez seja necessário selecionar mais escolhas > usar uma conta diferente, para especificar as credenciais inseridas quando você criou a VM.

   - Selecione OK.

1. Você pode receber um aviso do certificado durante o processo de logon. Se você receber um aviso de certificado, selecione Sim ou Continuar.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.

## <a name="access-web-app-privately-from-the-vm"></a>Acessar o aplicativo Web de forma privada da VM

Nesta seção, você se conectará de forma privada ao aplicativo Web usando o ponto de extremidade privado.

1. Obtenha o IP privado do seu ponto de extremidade privado, no **link privado**do tipo de barra de pesquisa e selecione link privado

   > [!div class="mx-imgBorder"]
   >![Link privado][14]

1. No centro de links privado, selecione **pontos de extremidade privados** para listar todos os seus pontos de extremidade privados

   > [!div class="mx-imgBorder"]
   >![Centro de links privado][15]

1. Selecione o link do ponto de extremidade privado para seu aplicativo Web e sua sub-rede

   > [!div class="mx-imgBorder"]
   >![Propriedades do ponto de extremidade privado][16]

1. Copie o IP privado do seu ponto de extremidade privado e o FQDN do seu aplicativo Web, no nosso caso, webappdemope.azurewebsites.net 10.10.2.4

1. No myVM, verifique se o aplicativo Web não está acessível por meio do IP público. Abra um navegador e cole o nome do aplicativo Web, você deve ter uma página de erro 403 Proibido

   > [!div class="mx-imgBorder"]
   >![erro proibido ao tentar usar o endereço IP][17]

   > [!Important]
   > Como esse recurso está em versão prévia, você precisa gerenciar manualmente a entrada DNS.

   Para o DNS, você tem duas opções:
   - usar o arquivo de host da VM 
   - ou use o serviço de zona privada do DNS do Azure.

1. Primeira solução: você pode criar uma zona privada DNS chamada privatelink.azurewebsites.net e vinculá-la à VNet
1. Em seguida, você precisa criar os dois registros A (nome do aplicativo e nome do SCM) com o endereço IP do seu ponto de extremidade privado
   > [!div class="mx-imgBorder"]
   >![Registros de zona privada DNS][21]

1. Segunda solução: criar a entrada de host, abrir o explorador de arquivos e localizar o arquivo de hosts

   > [!div class="mx-imgBorder"]
   >![Arquivo de hosts][18]

1. Adicione uma entrada com o endereço IP privado e o nome público do seu aplicativo Web editando o arquivo de hosts com o bloco de notas

   > [!div class="mx-imgBorder"]
   >![Hospeda conteúdo][19]

1. Salve o arquivo

1. Abra um navegador e digite a URL do seu aplicativo Web

   > [!div class="mx-imgBorder"]
   >![Site com PE][20]

1. Você está acessando seu aplicativo Web por meio do ponto de extremidade privado

## <a name="clean-up-resources"></a>Limpar recursos

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
[4]: ./media/create-private-endpoint-webapp-portal/virtual-machine.png
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
[21]: ./media/create-private-endpoint-webapp-portal/dns-private-zone-records.png


<!--Links-->
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
