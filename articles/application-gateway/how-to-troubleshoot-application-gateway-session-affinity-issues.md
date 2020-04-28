---
title: Solucionando problemas de afinidade de sessão
titleSuffix: Azure Application Gateway
description: Este artigo fornece informações sobre como solucionar problemas de afinidade de sessão no Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: aa3617b30fe1ef9b4d4a6c5fe5aac51bff95bb92
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866683"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Solucionar problemas de afinidade de sessão do Azure Application Gateway

Saiba como diagnosticar e resolver problemas de afinidade de sessão com o Azure Application Gateway.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral

O recurso de afinidade de sessão baseada em cookies é útil quando você deseja manter uma sessão de usuário no mesmo servidor. Usando cookies gerenciados pelo gateway, o Gateway de Aplicativo pode direcionar o tráfego seguinte de uma sessão de usuário para o mesmo servidor para processamento. Isso é importante em casos em que o estado de sessão é salvo localmente no servidor para uma sessão de usuário.

## <a name="possible-problem-causes"></a>Possíveis causas de problemas

O problema em manter a afinidade de sessão baseada em cookies pode acontecer devido às seguintes razões principais:

- A configuração "Afinidade baseada em cookies" não está ativada
- Seu aplicativo não pode lidar com afinidade baseada em cookies
- Aplicativo está usando afinidade baseada em cookies, mas solicitações ainda saltando entre servidores back-end

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Verifique se a configuração "Afinidade baseada em cookies" está ativada

Às vezes, os problemas de afinidade da sessão podem ocorrer quando você esquece de ativar a configuração "Afinidade baseada em Cookies". Para determinar se você habilitou a configuração "Afinidade baseada em cookies" na guia Configurações HTTP no portal Azure, siga as instruções:

1. Faça logon no [portal do Azure](https://portal.azure.com/).

2. No painel de **navegação à esquerda,** clique em **Todos os recursos**. Clique no nome do gateway do aplicativo na lâmina Todos os recursos. Se a assinatura que você selecionou já tiver vários recursos nele, você pode inserir o nome do gateway do aplicativo no **Filtro pelo nome...** para acessar facilmente o gateway de aplicativo.

3. Selecione a guia **configurações HTTP** em **CONFIGURAÇÕES**.

   ![solução de problemas-sessão-afinidade-questões-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Clique em **GatewayBackendHttpSettings** no lado direito para verificar se você **selecionou A** afinidade baseada em Cookies.

   ![solução de problemas-sessão-afinidade-questões-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Você também pode verificar o valor do "**CookieBasedAffinity**" definido *como Ativado*em "**backendHttpSettingsCollection**" usando um dos seguintes métodos:

- Executar [get-AzApplicationGatewayBackendhttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) no PowerShell
- Olhe através do arquivo JSON usando o modelo do Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>O aplicativo não pode lidar com afinidade baseada em cookies

#### <a name="cause"></a>Causa

O gateway de aplicativo só pode executar afinidade baseada em sessão usando um cookie.

#### <a name="workaround"></a>Solução alternativa

Se o aplicativo não conseguir lidar com a afinidade baseada em cookies, você deve usar um balanceador de carga azure externo ou interno ou outra solução de terceiros.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Aplicativo está usando afinidade baseada em cookies, mas solicitações ainda saltando entre servidores back-end

#### <a name="symptom"></a>Sintoma

Você habilitou a configuração Affinity baseada em Cookies, quando você acessa o [http://website](http://website/) Gateway de aplicativo usando uma URL de nome curto no Internet Explorer, por exemplo: , a solicitação ainda está saltando entre servidores back-end.

Para identificar este problema, siga as instruções:

1. Faça um rastreamento de depurador da Web no "Cliente" que está se conectando ao aplicativo por trás do Gateway de aplicativo (Estamos usando o Fiddler neste exemplo).
    **Dica** Se você não sabe como usar o Fiddler, verifique a opção "**Eu quero coletar tráfego de rede e analisá-lo usando web depurador**" na parte inferior.

2. Verifique e analise os registros da sessão, para determinar se os cookies fornecidos pelo cliente têm os detalhes do ARRAffinity. Se você não encontrar os detalhes do ARRAffinity, como "**ARRAffinity=** *ARRAffinityValue*" dentro do conjunto de cookies, isso significa que o cliente não está respondendo com o cookie ARRA, que é fornecido pelo Gateway de aplicativo.
    Por exemplo:

    ![solução de problemas-sessão-afinidade-questões-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![solução de problemas-sessão-afinidade-questões-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

O aplicativo continua tentando definir o cookie em cada solicitação até que ele receba resposta.

#### <a name="cause"></a>Causa

Esse problema ocorre porque o Internet Explorer e outros navegadores podem não armazenar ou usar o cookie com uma URL de nome curto.

#### <a name="resolution"></a>Resolução

Para corrigir esse problema, acesse o Gateway de Aplicativo usando um FQDN. Por exemplo, [http://website.com](https://website.com/) [http://appgw.website.com](http://website.com/) use ou .

## <a name="additional-logs-to-troubleshoot"></a>Registros adicionais para solucionar problemas

Você pode coletar registros adicionais e analisá-los para solucionar problemas relacionados à afinidade de sessão baseada em cookies

### <a name="analyze-application-gateway-logs"></a>Analisar registros do Gateway de aplicativos

Para coletar os logs do Gateway do aplicativo, siga as instruções:

Habilitar o log por meio do portal do Azure

1. No [portal Azure,](https://portal.azure.com/)encontre seu recurso e clique **em Registros de diagnóstico**.

   Para o Application Gateway, três logs estão disponíveis: log de acesso, registro de desempenho, registro de firewall

2. Para começar a coletar dados, clique **em Ativar diagnósticos**.

   ![solução de problemas-sessão-afinidade-questões-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. A folha **Configurações de diagnóstico** fornece as configurações dos logs de diagnóstico. Neste exemplo, o Log Analytics armazena os logs. Clique **em Configurar** em Análise de **Log** para definir seu espaço de trabalho. Você também pode usar os hubs de eventos e uma conta de armazenamento para salvar os logs de diagnóstico.

   ![solução de problemas-sessão-afinidade-questões-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Confirme as configurações e clique **em Salvar**.

   ![solução de problemas-sessão-afinidade-questões-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Visualize e analise os logs de acesso do Application Gateway

1. No portal Azure, sob a exibição de recursos do Application Gateway, **selecione Registros de diagnóstico** na seção **MONITORING** .

   ![solução de problemas-sessão-afinidade-questões-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. No lado direito, selecione "**ApplicationGatewayAccessLog**" na lista de desímento em **categorias Log.**  

   ![solução de problemas-sessão-afinidade-questões-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Na lista de log de acesso do gateway de aplicativo, clique no registro que deseja analisar e exporte e exporte o arquivo JSON.

4. Converta o arquivo JSON que você exportou na etapa 3 para o arquivo CSV e visualize-os no Excel, Power BI ou em qualquer outra ferramenta de visualização de dados.

5. Confira os seguintes dados:

- **ClientIP**– Este é o endereço IP do cliente do cliente de conexão.
- **ClientPort** - Esta é a porta de origem do cliente de conexão para a solicitação.
- **SolicitaçãoConsulta –** Isso indica o servidor de destino que a solicitação é recebida.
- **Roteado pelo servidor**: Instância do pool back-end de que a solicitação é recebida.
- **X-AzureApplicationGateway-LOG-ID**: ID de Correlação usada para a solicitação. Ela pode ser usada para solucionar problemas de tráfego nos servidores back-end. Por exemplo: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **SERVER-STATUS**: código de resposta HTTP que o Gateway de Aplicativo recebeu do back-end.

  ![solução de problemas-sessão-afinidade-questões-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Se você vir que dois itens estão vindo da mesma Porta clienteip e cliente, e eles são enviados para o mesmo servidor back-end, isso significa que o Gateway de aplicativo configurado corretamente.

Se você vir que dois itens estão vindo da mesma Porta ClientIP e Client, e eles são enviados para os diferentes servidores back-end, isso significa que a solicitação está saltando entre servidores back-end, selecione "**O aplicativo está usando afinidade baseada em cookies, mas as solicitações ainda saltam entre servidores back-end**" na parte inferior para solucionar problemas.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Use o web depurador para capturar e analisar os tráfegos HTTP ou HTTPS

Ferramentas de depuração da Web, como o Fiddler, podem ajudá-lo a depurar aplicativos web capturando tráfego de rede entre a Internet e computadores de teste. Essas ferramentas permitem que você inspecione dados de entrada e saída à medida que o navegador os recebe/envia. Fiddler, neste exemplo, tem a opção http replay que pode ajudá-lo a solucionar problemas do lado do cliente com aplicativos web, especialmente para o tipo de problema de autenticação.

Use o depurador web de sua escolha. Nesta amostra usaremos o Fiddler para capturar e analisar tráfegos http ou https, siga as instruções:

1. Baixe a ferramenta <https://www.telerik.com/download/fiddler>Fiddler em .

    > [!NOTE]
    > Escolha Fiddler4 se o computador de captura tiver o .NET 4 instalado. Caso contrário, escolha Fiddler2.

2. Clique com o botão direito do mouse no executável de configuração e execute como administrador para instalar.

    ![solução de problemas-sessão-afinidade-questões-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Ao abrir o Fiddler, ele deve começar automaticamente a capturar o tráfego (observe a Captura no canto inferior esquerdo). Pressione F12 para iniciar ou interromper a captura de tráfego.

    ![solução de problemas-sessão-afinidade-questões-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Provavelmente, você estará interessado no tráfego HTTPS descriptografado e poderá ativar a descriptografia HTTPS selecionando**Opções de Fiddler de** **ferramentas** > e verificar a caixa " **Descriptografar o tráfego HTTPS**".

    ![solução de problemas-sessão-afinidade-questões-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Você pode remover sessões não relacionadas anteriores antes de reproduzir o problema clicando em **X** (ícone) > **Remover tudo** da seguinte forma: 

    ![solução de problemas-sessão-afinidade-questões-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Depois de reproduzir o problema, salve o arquivo para revisão selecionando > **"Salvar** > **todas as sessões".** **File** 

    ![solução de problemas-sessão-afinidade-questões-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Verifique e analise os registros da sessão para determinar qual é o problema.

    Por exemplo:

- **Exemplo A:** Você encontra um registro de sessão que a solicitação é enviada do cliente, e ela vai para o endereço IP público do Gateway do aplicativo, clique neste registro para visualizar os detalhes.  No lado direito, os dados na caixa inferior é o que o Gateway de aplicativo está retornando ao cliente. Selecione a guia "RAW" e determine se o cliente está recebendo um **" Set-Cookie: ARRAffinity=** *ARRAffinityValue*." Se não houver cookie, a afinidade de sessão não está definida ou o Gateway de aplicativo não está aplicando cookie de volta ao cliente.

   > [!NOTE]
   > Esse valor ARRAffinity é o cookie-id, que o Gateway de aplicativo define para que o cliente seja enviado para um servidor back-end específico.

   ![solução de problemas-sessão-afinidade-questões-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Exemplo B:** O próximo registro de sessão seguido pelo anterior é o cliente respondendo de volta ao Gateway de aplicativo, que definiu o ARRAAFFINITY. Se o id de cookie ARRAffinity corresponder, o pacote deve ser enviado para o mesmo servidor back-end que foi usado anteriormente. Verifique as próximas várias linhas de comunicação http para ver se o cookie ARRAffinity do cliente está mudando.

   ![solução de problemas-sessão-afinidade-questões-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Para a mesma sessão de comunicação, o cookie não deve mudar. Verifique a caixa superior no lado direito, selecione a guia "Cookies" para ver se o cliente está usando o cookie e enviá-lo de volta para o Gateway do aplicativo. Se não, o navegador cliente não está mantendo e usando o cookie para conversas. Às vezes, o cliente pode mentir.

 

## <a name="next-steps"></a>Próximas etapas

Se as etapas anteriores não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).
