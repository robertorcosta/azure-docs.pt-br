---
title: Conecte-se privadamente a um aplicativo web usando o Azure Private Endpoint
description: Conecte-se privadamente a um aplicativo web usando o Azure Private Endpoint
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 2f10c7378ae7681b14df6e96b6a6f1adac832d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287808"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Conecte-se privadamente a um aplicativo da Web usando o Azure Private Endpoint (Preview)

O Azure Private Endpoint é o bloco fundamental de construção do Private Link no Azure. Ele permite que você se conecte privadamente ao seu Web App.
Neste Quickstart, você aprenderá como implantar um Web App com private endpoint e se conectar a este Aplicativo Web a partir de uma Máquina Virtual.

Para obter mais informações, consulte [Usando pontos finais privados para o azure Web App][privatenedpointwebapp].

> [!Note]
>A pré-visualização está disponível nas regiões leste dos EUA e Oeste dos EUA 2 para todos os Aplicativos Web PremiumV2 Windows e Linux. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Rede virtual e máquina virtual

Nesta seção, você criará a rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu Aplicativo web através do Ponto Final Privado.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e uma sub-rede.

1. No lado superior esquerdo da tela, selecione Criar uma**rede virtual** **de rede de** >  **recursos** > ou procurar rede **Virtual** na caixa de pesquisa.

1. Em **Criar rede virtual,** digite ou selecione essas informações na guia Noções Básicas:

   > [!div class="mx-imgBorder"]
   > ![Criar rede virtual][1]

1. Clique **em "Próximo: Endereços IP >"** e digite ou selecione essas informações:

   > [!div class="mx-imgBorder"]
   >![Configurar endereços IP][2]

1. Na seção sub-rede, clique em **"+ Adicionar sub-rede"** e digite as seguintes informações e clique em **"Adicionar"**

   > [!div class="mx-imgBorder"]
   >![Adicionar sub-rede][3]

1. Clique **em "Revisar + criar"**

1. Depois que a validação passar, clique em **"Criar"**

### <a name="create-virtual-machine"></a>Criar máquina virtual

1. No lado superior esquerdo da tela no portal Azure, selecione Criar uma**máquina Virtual** **de computação** >  **de recursos** > 

1. Em Criar uma máquina virtual – Noções básicas, insira ou selecione estas informações:

   > [!div class="mx-imgBorder"]
   >![Máquina Virtual básica][4]

1. Selecione **"Próximo: Discos"**

   Mantenha as configurações padrão.

1. Selecione **"Next: Networking"** e selecione essas informações:

   > [!div class="mx-imgBorder"]
   >![Rede][5]

1. Clique em **"Review + Create"**

1. Quando a validação passou mensagem, clique em **"Criar"**

## <a name="create-your-web-app-and-private-endpoint"></a>Crie seu aplicativo web e endpoint privado

Nesta seção, você criará um Aplicativo web privado usando um Ponto Final Privado para ele.

> [!Note]
>O recurso Private Endpoint só está disponível para o Premium V2 SKU.

### <a name="web-app"></a>Aplicativo Web

1. No lado superior esquerdo da tela no portal Azure, selecione **Criar um aplicativo** > **web** > **Web App** de recurso

1. Em Criar Web App - Noções básicas, digite ou selecione essas informações:

   > [!div class="mx-imgBorder"]
   >![App básico da Web App][6]

1. Selecione **"Revisar + criar"**

1. Quando a validação passou mensagem, clique em **"Criar"**

### <a name="create-the-private-endpoint"></a>Criar o ponto final privado

1. Nas propriedades do Web App, selecione **Configurações** > **de rede** e clique em **"Configurar suas conexões de ponto final privado"**

   > [!div class="mx-imgBorder"]
   >![Rede de aplicativos web][7]

1. No assistente, clique em **"+ adicionar"**

   > [!div class="mx-imgBorder"]
   >![Ponto final privado do aplicativo web][8]

1. Preencha as informações de assinatura, VNet e Subnet e clique em **"OK"**

   > [!div class="mx-imgBorder"]
   >![Rede de aplicativos web][9]

1. Revisar a criação do ponto final privado

   > [!div class="mx-imgBorder"]
   >![Revisão][10]
   >![Visão final do ponto final privado][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

1. Na barra de pesquisa do portal, digite **myVm**
1. Selecione o **botão Conectar**. Depois de selecionar o botão Conectar, Conecte-se às aberturas de máquinas virtuais, selecione **RDP**

   > [!div class="mx-imgBorder"]
   >![Botão RDP][12]

1. O Azure cria um arquivo Remote Desktop Protocol (.rdp) e o baixa para o seu computador depois de clicar em **Baixar arquivo RDP**

   > [!div class="mx-imgBorder"]
   >![Baixar arquivo RDP][13]

1. Abra o arquivo downloaded.rdp.

- Se solicitado, selecione Conectar.
- Insira o nome de usuário e a senha que você especificou ao criar a VM.

> [!Note]
> Você pode precisar selecionar Mais opções > Use uma conta diferente, para especificar as credenciais inseridas quando criou a VM.

- Selecione OK.

1. Você pode receber um aviso do certificado durante o processo de logon. Se você receber um aviso de certificado, selecione Sim ou Continuar.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.

## <a name="access-web-app-privately-from-the-vm"></a>Acesse o Web App em particular da VM

Nesta seção, você se conectará privadamente ao Aplicativo web usando o Ponto Final Privado.

1. Obtenha o IP privado do seu Ponto Final Privado, na barra de pesquisa tipo **Private Link,** e selecione Link privado

   > [!div class="mx-imgBorder"]
   >![Link privado][14]

1. No Centro de Link Privado, selecione **Pontos finais privados** para listar todos os seus pontos finais privados

   > [!div class="mx-imgBorder"]
   >![Centro de Link Privado][15]

1. Selecione o link private endpoint para seu Aplicativo web e sua sub-rede

   > [!div class="mx-imgBorder"]
   >![Propriedades de ponto final privado][16]

1. Copie o IP privado do seu Ponto Final Privado e o FQDN do seu Aplicativo Web, no nosso caso webappdemope.azurewebsites.net 10.10.2.4

1. No myVM, verifique se o Web App não está acessível através do IP público. Abra um navegador e cole o nome do Web App, você deve ter uma página de erro proibida 403

   > [!div class="mx-imgBorder"]
   >![Proibido][17]

> [!Important]
> Como este recurso está em visualização, você precisa gerenciar manualmente a entrada DNS.

1. Crie a entrada do host, abra o explorador de arquivos e localize o arquivo hosts

   > [!div class="mx-imgBorder"]
   >![Arquivo Hosts][18]

1. Adicione uma entrada com o endereço IP privado e o nome público do seu Aplicativo web editando o arquivo hosts com bloco de notas

   > [!div class="mx-imgBorder"]
   >![Conteúdo de hosts][19]

1. Salve o arquivo

1. Abra um navegador e digite a url do seu aplicativo web

   > [!div class="mx-imgBorder"]
   >![Site com PE][20]

1. Você está acessando seu Aplicativo web através do Ponto Final Privado

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar de usar o Private Endpoint, o Web App e a VM, exclua o grupo de recursos e todos os recursos que ele contém:

1. Digite ready-rg na caixa de pesquisa na parte superior do portal e selecione ready-rg dos resultados da pesquisa.
1. Selecione Excluir grupo de recursos.
1. Digite ready-rg para digitar o nome do grupo de recursos e selecione Excluir.

## <a name="next-steps"></a>Próximas etapas

Neste Quickstart, você criou uma VM em uma rede virtual, um Web App e um Private Endpoint. Você se conectou a uma VM da Internet e se comunicou com segurança ao Web App usando o Private Link. Para saber mais sobre o Private Endpoint, consulte [O que é o Azure Private Endpoint][privateendpoint].

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
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
