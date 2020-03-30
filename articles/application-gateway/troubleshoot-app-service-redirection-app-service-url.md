---
title: Redirecionamento de solução de problemas para URL do Serviço de Aplicativo
titleSuffix: Azure Application Gateway
description: Este artigo fornece informações sobre como solucionar problemas de redirecionamento quando o Azure Application Gateway é usado com o Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 961ed17bcef19b445c2546a557725bb6bd8653cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293534"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Solucionar problemas de serviço de aplicativos no Gateway de aplicativo

Saiba como diagnosticar e resolver problemas que você pode encontrar quando o Azure App Service é usado como um destino de back-end com o Azure Application Gateway.

## <a name="overview"></a>Visão geral

Neste artigo, você aprenderá como solucionar os seguintes problemas:

> [!div class="checklist"]
> * A URL do serviço de aplicativo é exposta no navegador quando há um redirecionamento.
> * O domínio de cookies ARRAffinity do serviço de aplicativo está definido como o nome do host do serviço de aplicativo, example.azurewebsites.net, em vez do host original.

Quando um aplicativo back-end envia uma resposta de redirecionamento, você pode querer redirecionar o cliente para uma URL diferente da especificada pelo aplicativo back-end. Você pode querer fazer isso quando um serviço de aplicativo está hospedado atrás de um gateway de aplicativo e exige que o cliente faça um redirecionamento para seu caminho relativo. Um exemplo é um redirecionamento da contoso.azurewebsites.net/path1 para a contoso.azurewebsites.net/path2. 

Quando o serviço de aplicativo envia uma resposta de redirecionamento, ele usa o mesmo nome host no cabeçalho de localização de sua resposta como o da solicitação que recebe do gateway do aplicativo. Por exemplo, o cliente faz a solicitação diretamente para contoso.azurewebsites.net/path2 em vez de passar pelo gateway de aplicativo contoso.com/path2. Você não quer contornar o gateway de aplicativo.

Este problema pode acontecer pelas seguintes razões principais:

- Você tem o redirecionamento configurado no seu serviço de aplicativo. O redirecionamento pode ser tão simples quanto adicionar uma barra de arrasto à solicitação.
- Você tem autenticação do Azure Active Directory, o que causa o redirecionamento.

Além disso, quando você usa serviços de aplicativo atrás de um gateway de aplicativo, o nome de domínio associado ao gateway de aplicativo (example.com) é diferente do nome de domínio do serviço do aplicativo (por exemplo, example.azurewebsites.net). O valor de domínio para o cookie ARRAffinity definido pelo serviço do aplicativo carrega o nome de domínio example.azurewebsites.net, o que não é desejável. O nome de host original, example.com, deve ser o valor do nome de domínio no cookie.

## <a name="sample-configuration"></a>Exemplo de configuração

- Ouvinte HTTP: Básico ou multi-site
- Pool de endereços back-end: Serviço de aplicativos
- Configurações HTTP: **Escolha o nome do host do endereço backend** ativado
- Teste: **Escolha o nome do host das configurações HTTP** ativadas

## <a name="cause"></a>Causa

O App Service é um serviço multilocatário, por isso usa o cabeçalho host na solicitação para encaminhar a solicitação para o ponto final correto. O nome de domínio padrão dos Serviços de Aplicativo, *.azurewebsites.net (digamos, contoso.azurewebsites.net), é diferente do nome de domínio do gateway do aplicativo (digamos, contoso.com). 

A solicitação original do cliente tem o nome de domínio do gateway do aplicativo, contoso.com, como o nome do host. Você precisa configurar o gateway do aplicativo para alterar o nome do host na solicitação original para o nome de host do serviço de aplicativo quando ele encaminha a solicitação para o back-end do serviço do aplicativo. Use o switch **Escolha o nome do host do endereço backend** na configuração http do gateway de aplicativo. Use o switch **Escolha o nome do host do Backend HTTP Configurações** na configuração do teste de saúde.



![O gateway de aplicativo altera o nome do host](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Quando o serviço de aplicativo faz um redirecionamento, ele usa o nome do host substituído contoso.azurewebsites.net no cabeçalho de localização em vez do nome de host original contoso.com, a menos que configurado em contrário. Verifique o exemplo a seguir de solicitação e cabeçalhos de resposta.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
No exemplo anterior, observe que o cabeçalho de resposta tem um código de status de 301 para redirecionamento. O cabeçalho de localização tem o nome de `www.contoso.com`host do serviço de aplicativo em vez do nome de host original .

## <a name="solution-rewrite-the-location-header"></a>Solução: Reescrever o cabeçalho de localização

Defina o nome do host no cabeçalho de localização para o nome de domínio do gateway do aplicativo. Para isso, crie uma [regra de reescrita](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) com uma condição que avalia se o cabeçalho de localização na resposta contém azurewebsites.net. Ele também deve executar uma ação para reescrever o cabeçalho de localização para ter o nome de host do gateway do aplicativo. Para obter mais informações, consulte instruções sobre [como reescrever o cabeçalho de localização](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> O suporte de regravação de cabeçalho HTTP está disponível apenas para o [Standard_v2 e WAF_v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) do Application Gateway. Se você usar v1 SKU, recomendamos que você [migre de v1 para v2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2). Você deseja usar regravação e outros [recursos avançados](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) que estão disponíveis com v2 SKU.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Solução alternativa: Use um nome de domínio personalizado

Se você usar v1 SKU, não poderá reescrever o cabeçalho de localização. Este recurso só está disponível para v2 SKU. Para resolver o problema de redirecionamento, passe o mesmo nome de host que o gateway de aplicativo recebe para o serviço do aplicativo também, em vez de fazer uma substituição de host.

O serviço de aplicativo agora faz o redirecionamento (se houver) no mesmo cabeçalho de host original que aponta para o gateway do aplicativo e não o seu próprio.

Você deve possuir um domínio personalizado e seguir este processo:

- Registre o domínio na lista de domínio sustal do serviço do aplicativo. Você deve ter um CNAME em seu domínio personalizado que aponte para o FQDN do serviço de aplicativo. Para obter mais informações, consulte [Mapear um nome de DNS personalizado existente para Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

    ![Lista de domínios personalizados do serviço de aplicativos](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Seu serviço de aplicativo está `www.contoso.com`pronto para aceitar o nome do host . Altere sua entrada CNAME no DNS para apontá-la de volta `appgw.eastus.cloudapp.azure.com`ao FQDN do gateway de aplicativo, por exemplo, .

- Certifique-se de `www.contoso.com` que seu domínio se resolve com o FQDN do gateway de aplicativo quando você fizer uma consulta de DNS.

- Defina o teste personalizado para desativar **O nome de escolha do host das configurações HTTP do Backend**. No portal Azure, limpe a caixa de seleção nas configurações da sonda. No PowerShell, não use o switch **-PickHostNameFromBackendHttpSettings** no comando **Set-AzApplicationGatewayProbeConfig.** No campo nome de host do teste, digite o FQDN do serviço de aplicativo, example.azurewebsites.net. As solicitações de teste enviadas do gateway de aplicativo carregam este FQDN no cabeçalho do host.

  > [!NOTE]
  > Para o próximo passo, certifique-se de que seu teste personalizado não esteja associado às configurações HTTP back-end. As configurações HTTP ainda têm o nome de escolha de host do switch **Endereço backend** ativado neste momento.

- Defina as configurações HTTP do gateway de aplicativo para desativar **Pick Hostname do Endereço backend**. No portal Azure, limpe a caixa de seleção. No PowerShell, não use o switch **-PickHostNameFromBackendAddress** no comando **Set-AzApplicationGatewayBackendHttpConfigurações.**

- Associe a sonda personalizada de volta às configurações HTTP back-end e verifique se o back-end é saudável.

- O gateway de aplicativo deve agora `www.contoso.com`encaminhar o mesmo nome de host, para o serviço do aplicativo. O redirecionamento acontece no mesmo nome do hospedeiro. Verifique o exemplo a seguir de solicitação e cabeçalhos de resposta.

Para implementar as etapas anteriores usando o PowerShell para uma configuração existente, use o script PowerShell de exemplo a seguir. Observe como não usamos os switches **-PickHostname** na configuração de configurações do teste e HTTP.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Próximas etapas

Se as etapas anteriores não resolverem o problema, abra um [bilhete de suporte](https://azure.microsoft.com/support/options/).
