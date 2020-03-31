---
title: Gerencie o tráfego para aplicativos multilocatários usando o portal
titleSuffix: Azure Application Gateway
description: Este artigo fornece orientações sobre como configurar os aplicativos web de serviço do Azure App como membros no pool de backend em um gateway de aplicativo existente ou novo.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075175"
---
# <a name="configure-app-service-with-application-gateway"></a>Configurar Serviço de Aplicativo com Gateway de Aplicativo

Como o serviço de aplicativo é um serviço de vários locatários em vez de uma implantação dedicada, ele usa o cabeçalho do host na solicitação recebida para resolver a solicitação para o ponto final de serviço de aplicativo correto. Normalmente, o nome DNS do aplicativo, que por sua vez é o nome DNS associado ao gateway de aplicativo em frente ao serviço do aplicativo, é diferente do nome de domínio do serviço de aplicativo backend. Portanto, o cabeçalho host na solicitação original recebida pelo gateway de aplicativo não é o mesmo que o nome de host do serviço backend. Por causa disso, a menos que o cabeçalho host na solicitação do gateway de aplicativo para o backend seja alterado para o nome de host do serviço backend, os backends de vários inquilinos não são capazes de resolver a solicitação para o ponto final correto.

O Application Gateway `Pick host name from backend address` fornece um switch chamado que substitui o cabeçalho host na solicitação com o nome do host do back-end quando a solicitação é roteada do Gateway de aplicativo para o backend. Esse recurso permite o suporte para back-ends de vários inquilinos, como serviço de aplicativos Azure e gerenciamento de API. 

Neste artigo, você aprenderá como:

> [!div class="checklist"]
>
> - Crie um pool de back-end e adicione um serviço de aplicativo a ele
> - Crie configurações HTTP e teste personalizado com switches "Pick Hostname" ativado

## <a name="prerequisites"></a>Pré-requisitos

- Gateway de aplicativo: Se você não tiver um gateway de aplicativo existente, veja como [criar um gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- Serviço de aplicativo: Se você não tem um serviço de aplicativo existente, consulte [a documentação do serviço do Aplicativo](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Adicionar serviço de aplicativo como pool de backend

1. No portal Azure, abra a visualização de configuração do gateway de aplicativo.

2. Em **pools Backend,** clique em **Adicionar** para criar um novo pool de back-end.

3. Forneça um nome adequado para a piscina de backend. 

4. Em **Targets,** clique no dropdown e escolha **Serviços de aplicativos** como opção.

5. Uma lista de paradas imediatamente abaixo da lista de alvos será exibida, que conterá uma lista de **serviços** de aplicativos. A partir deste dropdown, escolha o Serviço de aplicativo que deseja adicionar como membro do pool de backend e clique em Adicionar.

   ![Backend de serviço de aplicativo](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > A isto será apenas preenchida pelos serviços de aplicativos que estão na mesma assinatura do gateway do aplicativo. Se você quiser usar um serviço de aplicativo que esteja em uma assinatura diferente da em que o Gateway de aplicativo está, em vez de escolher **serviços** de aplicativo na lista **de alvos,** escolha endereço IP ou opção **de nome de host** e digite o nome do host (exemplo. azurewebsites.net) do serviço de aplicativo.

## <a name="create-http-settings-for-app-service"></a>Crie configurações HTTP para o serviço do aplicativo

1. Em **Configurações HTTP,** clique **em Adicionar** para criar uma nova configuração HTTP.

2. Insira um nome para a configuração HTTP e você pode ativar ou desativar a Afinidade Baseada em Cookies conforme sua exigência.

3. Escolha o protocolo como HTTP ou HTTPS conforme o seu caso de uso. 

   > [!NOTE]
   > Se você selecionar HTTPS, você não precisará carregar nenhum certificado de autenticação ou certificado raiz confiável para listar o backend do serviço de aplicativo, já que o serviço do aplicativo é um serviço Azure confiável.

4. Verifique a caixa para **uso para o serviço de aplicativo** . Observe que os `Create a probe with pick host name from backend address` `Pick host name from backend address` switches e serão automaticamente ativados.`Pick host name from backend address` substituirá o cabeçalho do host na solicitação com o nome do host do back-end quando a solicitação for roteada do Gateway de aplicativo para o backend.  

   `Create a probe with pick host name from backend address`criará automaticamente um teste de saúde e o associará a esta configuração HTTP. Você não precisa criar nenhum outro teste de saúde para esta configuração HTTP. Você pode verificar se um <HTTP Setting name> <Unique GUID> novo teste com o nome foi adicionado na `Pick host name from backend http settings enabled`lista de sondas de saúde e já tem o switch .

   Se você já tem uma ou mais configurações HTTP que estão sendo usadas para o serviço do App e se essas configurações HTTP usarem o mesmo protocolo que você está usando no que você está criando, então, em vez do `Create a probe with pick host name from backend address` switch, você receberá uma parada para selecionar um dos testes personalizados . Isso porque como já existe uma Configuração HTTP com serviço de aplicativo, portanto, `Pick host name from backend http settings enabled` também existiria um teste de saúde que tem o switch . Escolha essa sonda personalizada no dropdown.

5. Clique em **OK** para criar a configuração HTTP.

   ![Configuração HTTP1](./media/configure-web-app-portal/http-setting1.png)

   ![Configuração HTTP2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Criar regra para amarrar o ouvinte, o backend pool e a configuração HTTP

1. Em **Regras,** clique **em Básico** para criar uma nova regra Básica.

2. Forneça um nome adequado e selecione o ouvinte que aceitará as solicitações recebidas para o serviço do App.

3. No **pool de backend,** escolha o pool de back-end que você criou acima.

4. Na **configuração HTTP,** escolha a configuração HTTP que você criou acima.

5. Clique em **OK** para salvar esta regra.

   ![Regra](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Configuração adicional em caso de redirecionamento para o caminho relativo do serviço de aplicativo

Quando o serviço de aplicativo envia uma resposta de redirecionamento ao cliente para redirecionar para seu caminho relativo (Por exemplo, um redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2), ele usa o mesmo nome de host no cabeçalho de localização de sua resposta como o da solicitação recebida do gateway do aplicativo. Assim, o cliente fará a solicitação diretamente para contoso.azurewebsites.net/path2 em vez de passar pelo gateway de aplicativo (contoso.com/path2). Ignorar o gateway do aplicativo não é desejável.

Se no seu caso de uso, houver cenários em que o serviço do App precisará enviar uma resposta de redirecionamento ao cliente, executar as [etapas adicionais para reescrever o cabeçalho de localização](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration).

## <a name="restrict-access"></a>Restringir acesso

Os aplicativos web implantados nesses exemplos usam endereços IP públicos que podem ser acessados diretamente da internet. Isso ajuda com solução de problemas quando você estiver aprendendo sobre um novo recurso e tentar novas coisas. Mas se você pretende implantar um recurso na produção, você desejará adicionar mais restrições.

Uma forma de você restringir o acesso a seus aplicativos web de uma maneira é usar [restrições de IP estático do Serviço de Aplicativo do Azure](../app-service/app-service-ip-restrictions.md). Por exemplo, você pode restringir o aplicativo web para que ele só recebe tráfego de gateway de aplicativo. Use o recurso de restrição de IP de serviço de aplicativo para listar o VIP do gateway de aplicativo como o único endereço com o acesso.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o serviço do App e outros suportes a vários inquilinos com gateway de aplicativo, consulte [suporte a serviços multilocatários com gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).
