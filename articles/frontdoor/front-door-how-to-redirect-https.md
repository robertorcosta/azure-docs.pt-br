---
title: Criar uma porta frontal com o redirecionamento de HTTP para HTTPS usando o portal do Azure
description: Saiba como criar uma porta frontal com tráfego Redirecionado de HTTP para HTTPS usando o portal do Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 5/21/2019
ms.author: duau
ms.openlocfilehash: fe2159f0eeb9d01081e6a25e7a88ceff4f1e361c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399683"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Criar uma porta frontal com o redirecionamento de HTTP para HTTPS usando o portal do Azure

Você pode usar o portal do Azure para criar uma [porta frontal](front-door-overview.md) com um certificado para terminação TLS. Uma regra de roteamento é usada para redirecionar o tráfego HTTP para HTTPS.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Criar uma porta frontal com um recurso de aplicativo Web existente
> * Adicionar um domínio personalizado com certificado TLS/SSL 
> * Configurar o redirecionamento de HTTPS no domínio personalizado

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Criar uma porta frontal com um recurso de aplicativo Web existente

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Criar um recurso** encontrado na parte superior esquerda do portal do Azure.
3. Pesquise **porta frontal** usando a barra de pesquisa e, quando encontrar o tipo de recurso, clique em **criar**.
4. Escolha uma assinatura e, em seguida, use um grupo de recursos existente ou crie um novo. Observe que o local solicitado na interface do usuário é apenas para o grupo de recursos. A configuração da porta de front-end será implantada em todos os [locais pop da porta frontal do Azure](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    ![Configurar noções básicas para uma nova porta de front-end](./media/front-door-url-redirect/front-door-create-basics.png)

5. Clique em **Avançar** para inserir a guia configuração. A configuração da porta frontal ocorre em três etapas-adicionando um host de front-end padrão, adicionando back-ends em um pool de back-end e criando regras de roteamento para mapear o comportamento de roteamento para o host de front-end. 

     ![Designer de configuração de porta frontal](./media/front-door-url-redirect/front-door-designer.png)

6. Clique no **+** ícone ' ' nos _hosts de front_ -end para criar um host de front-end, insira um nome globalmente exclusivo para seu host de front-end padrão para sua porta frontal ( `\<**name**\>.azurefd.net` ). Clique em **Adicionar** para prosseguir para a próxima etapa.

     ![Adicionar um host de front-end](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Clique no **+** ícone ' ' nos _pools de back-end_ para criar um pool de back-end. Forneça um nome para o pool de back-end e clique em '**Adicionar um back-end**'.
8. Selecione o tipo de host de back-end como _serviço de aplicativo_. Selecione a assinatura na qual seu aplicativo Web está hospedado e, em seguida, selecione o aplicativo Web específico no menu suspenso para **nome de host de back-end**.
9. Clique em **Adicionar** para salvar o back-end e clique em **Adicionar** novamente para salvar a configuração do pool de back-end.   ![Adicionar um back-end em um pool de back-end](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Clique no **+** ícone ' ' nas _regras de roteamento_ para criar uma rota. Forneça um nome para a rota, diga ' HttpToHttpsRedirect ' e defina o campo _protocolos aceitos_ como **' somente http '**. Verifique se o _host front-end_ apropriado está selecionado.  
11. Na seção _detalhes da rota_ , defina o _tipo de rota_ como **redirecionar**, verifique se o _tipo de redirecionamento_ está definido como **encontrado (302)** e se o _protocolo de redirecionamento_ está definido como **somente HTTPS**. 
12. Clique em Adicionar para salvar a regra de roteamento para redirecionamento HTTP para HTTPS.
     ![Adicionar uma rota de redirecionamento de HTTP para HTTPS](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Adicione outra regra de roteamento para lidar com o tráfego HTTPS. Clique no **+** sinal ' ' nas _regras de roteamento_ e forneça um nome para a rota, digamos ' DefaultForwardingRoute ' e, em seguida, defina o campo _protocolos aceitos_ como **' somente HTTPS '**. Verifique se o _host front-end_ apropriado está selecionado.
14. Na seção detalhes da rota, defina o _tipo de rota_ como **encaminhar**, verifique se o pool de back-end correto está selecionado e se o _protocolo de encaminhamento_ está definido como **somente HTTPS**. 
15. Clique em Adicionar para salvar a regra de roteamento para encaminhamento de solicitação.
     ![Adicionar uma rota de avanço para o tráfego HTTPS](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Clique em **revisar + criar** e **criar**, para criar o perfil de porta frontal. Vá para o recurso uma vez criado.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Adicionar um domínio personalizado à sua porta frontal e habilitar HTTPS nele
As etapas a seguir mostram como você pode adicionar um domínio personalizado em um recurso de porta frontal existente e, em seguida, habilitar o redirecionamento de HTTP para HTTPS. 

### <a name="add-a-custom-domain"></a>Adicionar um domínio personalizado

Neste exemplo, você adiciona um registro CNAME para o `www` subdomínio (por exemplo, `www.contosonews.com` ).

#### <a name="create-the-cname-record"></a>Criar um registro CNAME

Adicione um registro CNAME para mapear um subdomínio para o host de front-end padrão da porta frontal ( `<name>.azurefd.net` , em que `<name>` é o nome do seu perfil de porta frontal).

Para o `www.contoso.com` domínio, como um exemplo, adicione um registro CNAME que mapeie o nome `www` para `<name>.azurefd.net` .

Depois de adicionar o CNAME, a página de registros DNS será parecida com o seguinte exemplo:

![Domínio personalizado CNAME para porta frontal](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Carregar o domínio personalizado na sua porta frontal

1. Na guia Designer de porta frontal, clique no ícone "+" na seção hosts de front-end para adicionar um novo domínio personalizado. 
2. Insira o nome DNS personalizado totalmente qualificado no campo nome do host personalizado, exemplo `www.contosonews.com` . 
3. Depois que o mapeamento CNAME do domínio para sua porta frontal for validado, clique em **Adicionar** para adicionar o domínio personalizado.
4. Clique em **salvar** para enviar as alterações.

![Menu de domínio personalizado](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Habilitar HTTPS em seu domínio personalizado

1. Clique no domínio personalizado que foi adicionado e sob a seção **domínio personalizado https**, altere o status para **habilitado**.
2. Você pode deixar o **tipo de gerenciamento de certificado** definido como _porta frontal gerenciado_ para o certificado gratuito mantido, gerenciado e girado de acordo com a porta frontal. Você também pode optar por usar seu próprio certificado TLS/SSL personalizado armazenado com o Azure Key Vault. Este tutorial pressupõe o uso do certificado gerenciado de porta de recepção.
![Habilitando HTTPS para domínio personalizado](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Clique em **Atualizar** para salvar a seleção e, em seguida, clique em **salvar**.
4. Clique em **Atualizar** após alguns minutos e, em seguida, clique no domínio personalizado novamente para ver o progresso do provisionamento de certificado. 

> [!WARNING]
> A habilitação de HTTPS para um domínio personalizado pode levar vários minutos e também depende da validação de Propriedade do domínio se o CNAME não for mapeado diretamente para o host da porta frontal `<name>.azurefd.net` . Saiba mais sobre [como habilitar HTTPS para um domínio personalizado](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Configurar as regras de roteamento para o domínio personalizado

1. Clique na regra de roteamento de redirecionamento criada anteriormente.
2. Clique na lista suspensa para hosts front-end e selecione seu domínio personalizado para aplicar essa rota também para seu domínio.
3. Clique em **Atualizar**.
4. Faça a mesma operação para a outra regra de roteamento, ou seja, para a rota de encaminhamento para adicionar o domínio personalizado.
5. Clique em **salvar** para enviar suas alterações.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
- Saiba mais sobre o [redirecionamento de URL na porta frontal](front-door-url-redirect.md).
