---
title: Solucionaproblemas problemas de saúde no Gateway de aplicativos do Azure
description: Descreve como solucionar problemas de saúde backend para o Gateway de aplicativos do Azure
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: a16120194b1b8015466005f42336828c2b4ace6c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983833"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Solucionar problemas de saúde no Gateway de aplicativos
==================================================

<a name="overview"></a>Visão geral
--------

Por padrão, o Azure Application Gateway testa os servidores backend para verificar seu estado de saúde e verificar se eles estão prontos para atender às solicitações. Os usuários também podem criar testes personalizados para mencionar o nome do host, o caminho a ser sondados e os códigos de status a serem aceitos como Saudáveis. Em cada caso, se o servidor backend não responder com sucesso, o Application Gateway marca o servidor como insalubre e pára de encaminhar solicitações para o servidor. Depois que o servidor começa a responder com sucesso, o Application Gateway retoma o encaminhamento das solicitações.

### <a name="how-to-check-backend-health"></a>Como verificar a saúde do backend

Para verificar a saúde da sua piscina de backend, você pode usar a página **Backend Health** no portal Azure. Ou, você pode usar [a Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0) [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)ou [Rest API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth).

O status recuperado por qualquer um desses métodos pode ser qualquer um dos seguintes:

- Healthy

- Não Íntegro

- Unknown (desconhecido)

Se o status de saúde back-end de um servidor for saudável, significa que o Application Gateway encaminhará as solicitações para esse servidor. Mas se a saúde do backend para todos os servidores em um pool de backend for insalubre ou desconhecida, você pode encontrar problemas quando tentar acessar aplicativos. Este artigo descreve os sintomas, causa e resolução de cada um dos erros apresentados.

<a name="backend-health-status-unhealthy"></a>Estado de saúde backend: Insalubre
-------------------------------

Se o estado de saúde do backend for insalubre, a exibição do portal se assemelhará à seguinte captura de tela:

![Aplicativo Gateway backend saúde - Insalubre](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Ou se você estiver usando uma consulta de Azure PowerShell, CLI ou Azure REST, você receberá uma resposta que se assemelha ao seguinte:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
Depois de receber um status de servidor backend insalubre para todos os servidores em um pool de backend, as solicitações não são encaminhadas para os servidores e o Application Gateway retorna um erro "502 Bad Gateway" para o cliente solicitante. Para solucionar esse problema, verifique a coluna **Detalhes** na guia **Backend Health.**

A mensagem exibida na coluna **Detalhes** fornece insights mais detalhados sobre o problema e, com base nisso, você pode começar a solucionar problemas.

> [!NOTE]
> A solicitação padrão do teste \<é\>enviada no formato do protocolo\<://127.0.0.1: porta\>/. Por exemplo, http://127.0.0.1:80 para um teste http na porta 80. Apenas códigos de status HTTP de 200 a 399 são considerados saudáveis. O protocolo e a porta de destino são herdados das configurações HTTP. Se você quiser que o Application Gateway faça um teste em um protocolo diferente, nome do host ou caminho e para reconhecer um código de status diferente como Saudável, configure um teste personalizado e associe-o às configurações HTTP.

<a name="error-messages"></a>Mensagens de erro
------------------------
#### <a name="backend-server-timeout"></a>Tempo de tempo do servidor backend

**Mensagem:** O tempo levado pelo backend\'para responder ao teste de saúde do gateway de aplicativo é mais do que o limite de tempo limite na configuração do teste.

**Causa:** Depois que o Application Gateway envia uma solicitação de teste HTTP(S) para o servidor backend, ele aguarda uma resposta do servidor backend por um período configurado. Se o servidor back-end não responder dentro do período configurado (o valor do tempo de tempo), ele será marcado como insalubre até que ele comece a responder dentro do período de tempo configurado novamente.

**Resolução:** Verifique por que o servidor ou aplicativo backend não está respondendo dentro do período de tempo configurado e também verifique as dependências do aplicativo. Por exemplo, verifique se o banco de dados tem algum problema que possa desencadear um atraso na resposta. Se você estiver ciente do comportamento do aplicativo e ele deve responder somente após o valor de tempo limite, aumente o valor de tempo limite das configurações personalizadas do teste. Você deve ter um teste personalizado para alterar o valor do tempo. Para obter informações sobre como configurar um teste personalizado, [consulte a página de documentação](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Para aumentar o valor de tempo limite, siga estas etapas:

1.  Acesse diretamente o servidor backend e verifique o tempo que o servidor deve responder nessa página. Você pode usar qualquer ferramenta para acessar o servidor backend, incluindo um navegador usando ferramentas de desenvolvedor.

1.  Depois de descobrir o tempo que o aplicativo deve responder, selecione a guia **Testes de saúde** e selecione o teste associado às configurações HTTP.

1.  Digite qualquer valor de tempo que seja maior do que o tempo de resposta do aplicativo, em segundos.

1.  Salve as configurações personalizadas da sonda e verifique se a saúde backend se mostra como saudável agora.

#### <a name="dns-resolution-error"></a>Erro de resolução de DNS

**Mensagem:** O Application Gateway não pôde criar um teste para esse backend. Isso geralmente acontece quando o FQDN do back-end não foi inserido corretamente. 

**Causa:** Se o pool de backend for do tipo Endereço IP/FQDN ou Serviço de Aplicativo, o Application Gateway resolverá para o endereço IP do FQDN inserido através do DNS (Domain Name System, sistema de nomes de domínio) (padrão personalizado ou Azure) e tentará se conectar ao servidor na porta TCP mencionada nas Configurações HTTP. Mas se essa mensagem for exibida, sugere que o Application Gateway não conseguiu resolver com sucesso o endereço IP do FQDN inserido.

**Solução:**

1.  Verifique se o FQDN inserido no pool de backend está correto e que é um domínio público e, em seguida, tente resolvê-lo a partir de sua máquina local.

1.  Se você puder resolver o endereço IP, pode haver algo errado com a configuração de DNS na rede virtual.

1.  Verifique se a rede virtual está configurada com um servidor DNS personalizado. Se for, verifique o servidor DNS sobre por que ele não pode resolver para o endereço IP do FQDN especificado.

1.  Se você estiver usando o DNS padrão do Azure, verifique com o registrador de nome de domínio se o mapeamento de registro a um ou cname adequado foi concluído.

1.  Se o domínio for privado ou interno, tente resolvê-lo a partir de uma VM na mesma rede virtual. Se você puder resolvê-lo, reinicie o Gateway de aplicativo e verifique novamente. Para reiniciar o Gateway de aplicativos, você precisa [parar](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) e [iniciar](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) usando os comandos PowerShell descritos nesses recursos vinculados.

#### <a name="tcp-connect-error"></a>Erro de conexão TCP

**Mensagem:** O Gateway do aplicativo não pôde se conectar ao backend.
Verifique se o backend responde na porta usada para a sonda.
Verifique também se algum NSG/UDR/Firewall está bloqueando o acesso ao Ip e à porta deste backend

**Causa:** Após a fase de resolução do DNS, o Application Gateway tenta conectar-se ao servidor back-end na porta TCP configurada nas configurações HTTP. Se o Gateway de aplicativo não puder estabelecer uma sessão TCP na porta especificada, o teste será marcado como insalubre com esta mensagem.

**Solução:** Se você receber esse erro, siga estas etapas:

1.  Verifique se você pode se conectar ao servidor backend na porta mencionada nas configurações HTTP usando um navegador ou powerShell. Por exemplo, execute o seguinte comando:`Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Se a porta mencionada não for a porta desejada, digite o número de porta correto para O Gateway de aplicativo para se conectar ao servidor back-end

1.  Se você não pode se conectar na porta a partir de sua máquina local também, então:

    a.  Verifique as configurações do grupo de segurança de rede (NSG) do adaptador de rede e da sub-rede do servidor backend e se as conexões de entrada na porta configurada são permitidas. Se não forem, crie uma nova regra para permitir as conexões. Para saber como criar regras do NSG, [consulte a página de documentação](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Verifique se as configurações do NSG da sub-rede Application Gateway permitem tráfego público e privado de saída, para que uma conexão possa ser feita. Verifique a página do documento fornecida na etapa 3a para saber mais sobre como criar regras do NSG.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Verifique as configurações de udr (Configurações de uDR) definidas pelo usuário do Application Gateway e a sub-rede do servidor backend para obter quaisquer anomalias de roteamento. Certifique-se de que o UDR não está direcionando o tráfego para longe da sub-rede backend. Por exemplo, verifique se há rotas para dispositivos virtuais de rede ou rotas padrão sendo anunciadas na sub-rede Application Gateway via Azure ExpressRoute e/ou VPN.

    d.  Para verificar as rotas e regras eficazes de um adaptador de rede, você pode usar os seguintes comandos PowerShell:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Se você não encontrar problemas com O NSG ou UDR, verifique o servidor backend para problemas relacionados ao aplicativo que estão impedindo que os clientes estabeleçam uma sessão TCP nas portas configuradas. Algumas coisas para verificar:

    a.  Abra um prompt de comando\> (Win+R - cmd), digite `netstat`e selecione Enter.

    b.  Verifique se o servidor está ouvindo na porta configurada. Por exemplo:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Se não estiver ouvindo na porta configurada, verifique as configurações do servidor web. Por exemplo: vinculações de site no IIS, bloqueio de servidor no NGINX e host virtual no Apache.

    d.  Verifique as configurações do firewall do sistema operacional para garantir que o tráfego de entrada na porta seja permitido.

#### <a name="http-status-code-mismatch"></a>Incompatibilidade de código de status HTTP

**Mensagem:** O código de\'status da resposta HTTP do backend não correspondia à configuração do teste. Esperado:{HTTPStatusCode0} Recebido:{HTTPStatusCode1}.

**Causa:** Depois que a conexão TCP for estabelecida e um aperto de mão TLS for feito (se o TLS estiver habilitado), o Application Gateway enviará o teste como uma solicitação HTTP GET para o servidor backend. Como descrito anteriormente, o \<teste\>padrão será o protocolo ://127.0.0.1:\<porta\>/, e considera os códigos de status de resposta na raiva 200 a 399 como Saudáveis. Se o servidor retornar qualquer outro código de status, ele será marcado como Insalubre com esta mensagem.

**Solução:** Dependendo do código de resposta do servidor backend, você pode tomar as seguintes etapas. Alguns dos códigos de status comuns estão listados aqui:

| **Erro** | **Ações** |
| --- | --- |
| Incompatibilidade de código de status do teste: Recebida 401 | Verifique se o servidor back-end requer autenticação. Os testes do Application Gateway não podem passar credenciais para autenticação neste momento. Permita \"HTTP 401\" em uma correspondência de código de status do teste ou teste para um caminho onde o servidor não exija autenticação. | |
| Incompatibilidade de código de status do teste: Recebida 403 | Acesso proibido. Verifique se o acesso ao caminho é permitido no servidor back-end. | |
| Incompatibilidade de código de status do teste: Recebida 404 | Página não encontrada. Verifique se o caminho do nome do host está acessível no servidor back-end. Altere o nome do host ou o parâmetro de caminho para um valor acessível. | |
| Incompatibilidade de código de status do teste: Recebeu 405 | As solicitações de teste para o Application Gateway usam o método HTTP GET. Verifique se seu servidor permite esse método. | |
| Incompatibilidade de código de status do teste: Recebeu 500 | Erro interno do servidor. Verifique a integridade do servidor de back-end e se os serviços estão em execução. | |
| Incompatibilidade de código de status do teste: Recebeu 503 | Serviço indisponível. Verifique a integridade do servidor de back-end e se os serviços estão em execução. | |

Ou, se você acha que a resposta é legítima e quer que o Application Gateway aceite outros códigos de status como Healthy, você pode criar um teste personalizado. Essa abordagem é útil em situações em que o site backend precisa de autenticação. Como as solicitações de teste não possuem credenciais de usuário, elas falharão e um código de status HTTP 401 será devolvido pelo servidor backend.

Para criar um teste personalizado, siga [estas etapas](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

#### <a name="http-response-body-mismatch"></a>Incompatibilidade do corpo de resposta HTTP

**Mensagem:** O corpo da\'resposta HTTP do backend não correspondia à configuração do teste. O corpo de resposta recebido não contém {string}.

**Causa:** Quando você cria um teste personalizado, você tem a opção de marcar um servidor backend como Saudável, combinando uma seqüência do corpo de resposta. Por exemplo, você pode configurar o Application Gateway para aceitar "não autorizado" como uma string a combinar. Se a resposta do servidor backend para a solicitação do teste contiver a seqüência **não autorizada,** ela será marcada como Saudável. Caso contrário, será marcado como insalubre com esta mensagem.

**Solução:** Para resolver esse problema, siga estas etapas:

1.  Acesse o servidor backend localmente ou a partir de uma máquina cliente no caminho do teste e verifique o corpo de resposta.

1.  Verifique se o corpo de resposta na configuração do teste personalizado do Application Gateway corresponde ao que está configurado.

1.  Se eles não corresponderem, altere a configuração do teste de modo que tenha o valor de seqüência correto para aceitar.

Saiba mais sobre [a correspondência do teste Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching).

#### <a name="backend-server-certificate-invalid-ca"></a>Certificado de servidor backend inválido CA

**Mensagem:** O certificado de servidor usado pelo backend não é assinado por uma conhecida Autoridade de Certificado (CA). Liste o backend no Gateway de aplicativo carregando o certificado raiz do certificado de servidor usado pelo backend.

**Causa:** O SSL de ponta a ponta com o Application Gateway v2 requer que o certificado do servidor backend seja verificado para julgar o servidor saudável.
Para que um certificado TLS/SSL seja confiável, esse certificado do servidor backend deve ser emitido por um CA incluído na loja confiável do Application Gateway. Se o certificado não foi emitido por um CA confiável (por exemplo, se um certificado auto-assinado foi usado), os usuários devem carregar o certificado do emissor para o Application Gateway.

**Solução:** Siga estas etapas para exportar e carregar o certificado raiz confiável para o Application Gateway. (Essas etapas são para clientes Windows.)

1.  Faça login na máquina onde seu aplicativo está hospedado.

1.  Selecione Win+R ou botão com o botão Com o botão **Iniciar** e, em seguida, **selecione Executar**.

1.  Digite `certmgr.msc` e selecione Enter. Você também pode procurar por Gerenciador de Certificados no menu **Iniciar.**

1.  Localize o certificado, `\Certificates - Current User\\Personal\\Certificates\`normalmente em , e abra-o.

1.  Selecione o certificado raiz e, em seguida, **selecione Exibir certificado**.

1.  Nas propriedades Certificado, selecione a guia **Detalhes.**

1.  Na guia **Detalhes,** selecione a opção **Copiar para Arquivo** e salve o arquivo na Base-64 codificada X.509 (. FORMATO CER).

1.  Abra a página **Configurações** HTTP do Gateway de aplicativo no portal Azure.

1. Abra as configurações HTTP, **selecione Adicionar certificado**e localize o arquivo de certificado que você acabou de salvar.

1. Selecione **Salvar** para salvar as configurações HTTP.

Alternativamente, você pode exportar o certificado raiz de uma máquina cliente acessando diretamente o servidor (contornando o Gateway do Aplicativo) através do navegador e exportando o certificado raiz do navegador.

Para obter mais informações sobre como extrair e carregar certificados raiz confiável no Gateway de aplicativo, consulte [Exportar certificado raiz confiável (para v2 SKU)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Incompatibilidade de certificado raiz confiável

**Mensagem:** O certificado raiz do certificado de servidor usado pelo backend não corresponde ao certificado raiz confiável adicionado ao gateway do aplicativo. Certifique-se de adicionar o certificado raiz correto para listar o backend

**Causa:** O SSL de ponta a ponta com o Application Gateway v2 requer que o certificado do servidor backend seja verificado para julgar o servidor saudável.
Para que um certificado TLS/SSL seja confiável, o certificado de servidor backend deve ser emitido por um CA incluído na loja confiável do Application Gateway. Se o certificado não foi emitido por um CA confiável (por exemplo, um certificado auto-assinado foi usado), os usuários devem carregar o certificado do emissor para o Gateway de aplicativo.

O certificado que foi carregado nas configurações HTTP do Application Gateway deve corresponder ao certificado raiz do certificado de servidor backend.

**Solução:** Se você receber esta mensagem de erro, há uma incompatibilidade entre o certificado que foi carregado no Application Gateway e o que foi carregado para o servidor backend.

Siga as etapas 1-11 no método anterior para carregar o certificado raiz confiável correto para o Gateway de aplicativo.

Para obter mais informações sobre como extrair e carregar certificados raiz confiável no Gateway de aplicativo, consulte [Exportar certificado raiz confiável (para v2 SKU)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Esse erro também pode ocorrer se o servidor backend não trocar a cadeia completa do cert, incluindo o Root > Intermediate (se aplicável) > Leaf durante o aperto de mão TLS. Para verificar, você pode usar comandos OpenSSL de qualquer cliente e conectar-se ao servidor backend usando as configurações configuradas no teste Application Gateway.

Por exemplo:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Se a saída não mostrar a cadeia completa do certificado que está sendo devolvido, exporte o certificado novamente com a cadeia completa, incluindo o certificado raiz. Configure esse certificado no servidor backend. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>Nome comum inválido do certificado backend (CN)

**Mensagem:** O nome comum (CN) do certificado backend não corresponde ao cabeçalho de host do teste.

**Causa:** O Gateway de aplicativo verifica se o nome do host especificado nas configurações HTTP backend corresponde ao da CN apresentada pelo certificado TLS/SSL do servidor backend. Este é Standard_v2 e WAF_v2 comportamento SKU. O SNI (Server Name Indication, indicação de nome do servidor padrão e waf sku) é definido como o FQDN no endereço do pool de backend.

No V2 SKU, se houver um teste padrão (nenhum teste personalizado foi configurado e associado), o SNI será definido a partir do nome do host mencionado nas configurações HTTP. Ou, se "Escolher o nome do host a partir do endereço backend" for mencionado nas configurações HTTP, onde o pool de endereços back-end contém um FQDN válido, essa configuração será aplicada.

Se houver um teste personalizado associado às configurações HTTP, o SNI será definido a partir do nome do host mencionado na configuração do teste personalizado. Ou, se **Escolher nome de host das configurações HTTP do backend** for selecionada no teste personalizado, o SNI será definido a partir do nome do host mencionado nas configurações HTTP.

Se **Escolher nome de host do endereço backend** estiver definido nas configurações HTTP, o pool de endereços back-end deve conter um FQDN válido.

Se você receber esta mensagem de erro, a CN do certificado backend não corresponde ao nome do host configurado no teste personalizado ou nas configurações HTTP (se **Escolher nome de host das configurações HTTP do backend** for selecionada). Se você estiver usando um teste padrão, o nome do host será definido como **127.0.0.1**. Se esse não for um valor desejado, você deve criar um teste personalizado e associá-lo às configurações HTTP.

**Solução:**

Para resolver o problema, siga estas etapas.

Para Windows:

1.  Faça login na máquina onde seu aplicativo está hospedado.

1.  Selecione Win+R ou com o botão com o botão **Iniciar** e selecione **Executar**.

1.  Digite **certmgr.msc** e selecione Enter. Você também pode procurar por Gerenciador de Certificados no menu **Iniciar.**

1.  Localize o certificado `\Certificates - Current User\\Personal\\Certificates`(normalmente em ), e abra o certificado.

1.  Na guia **Detalhes,** verifique o certificado **Assunto**.

1.  Verifique a CN do certificado a partir dos detalhes e digite o mesmo no campo nome do host do teste personalizado ou nas configurações HTTP (se **Escolher nome de host das configurações HTTP backend** for selecionada). Se esse não for o nome de host desejado para o seu site, você deve obter um certificado para esse domínio ou digitar o nome de host correto na configuração de configuração personalizada ou HTTP.

Para Linux usando OpenSSL:

1.  Execute este comando no OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  A partir das propriedades exibidas, encontre a CN do certificado e digite o mesmo no campo nome de host das configurações http. Se esse não for o nome de host desejado para o seu site, você deve obter um certificado para esse domínio ou digitar o nome de host correto na configuração de configuração personalizada ou HTTP.

#### <a name="backend-certificate-is-invalid"></a>O certificado backend é inválido

**Mensagem:** O certificado backend é inválido. A data atual \"não\" está \"dentro\" da faixa válida e válida para data no certificado.

**Causa:** Cada certificado vem com uma faixa de validade, e a conexão HTTPS não será segura a menos que o certificado TLS/SSL do servidor seja válido. Os dados atuais devem estar dentro **do intervalo válido** e **válido.** Se não for, o certificado é considerado inválido, e isso criará um problema de segurança no qual o Application Gateway marca o servidor backend como insalubre.

**Solução:** Se o certificado TLS/SSL tiver expirado, renove o certificado com seu fornecedor e atualize as configurações do servidor com o novo certificado. Se for um certificado auto-assinado, você deve gerar um certificado válido e carregar o certificado raiz para as configurações HTTP do Gateway de aplicativo. Para fazer isso, siga estas etapas:

1.  Abra as configurações HTTP do Gateway de aplicativo no portal.

1.  Selecione a configuração que tem o certificado expirado, **selecione Adicionar certificado**e abra o novo arquivo de certificado.

1.  Remova o certificado antigo usando o ícone **Excluir** ao lado do certificado e, em seguida, **selecione Salvar**.

#### <a name="certificate-verification-failed"></a>Falha na verificação do certificado

**Mensagem:** A validade do certificado backend não pôde ser verificada. Para descobrir o motivo, verifique os diagnósticos do OpenSSL para obter a mensagem associada ao código de erro {errorCode}

**Causa:** Esse erro ocorre quando o Gateway de aplicativo não pode verificar a validade do certificado.

**Solução:** Para resolver esse problema, verifique se o certificado em seu servidor foi criado corretamente. Por exemplo, você pode usar [o OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) para verificar o certificado e suas propriedades e, em seguida, tentar recarregar o certificado para as configurações HTTP do Gateway de aplicativo.

<a name="backend-health-status-unknown"></a>Estado de saúde backend: desconhecido
-------------------------------
Se a saúde backend for mostrada como Desconhecida, a exibição do portal se assemelhará à seguinte captura de tela:

![Saúde backend do Application Gateway - Desconhecido](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Esse comportamento pode ocorrer por uma ou mais das seguintes razões:

1.  O NSG na sub-rede Application Gateway está bloqueando o acesso de entrada às portas 65503-65534 (v1 SKU) ou 65200-65535 (v2 SKU) da "Internet".
1.  O UDR na sub-rede Application Gateway é definido para a rota padrão (0.0.0.0/0) e o próximo salto não é especificado como "Internet".
1.  A rota padrão é anunciada por uma conexão ExpressRoute/VPN a uma rede virtual via BGP.
1.  O servidor DNS personalizado está configurado em uma rede virtual que não pode resolver nomes de domínio público.
1.  O Application Gateway está em um estado insalubre.

**Solução:**

1.  Verifique se seu NSG está bloqueando o acesso às portas 65503-65534 (v1 SKU) ou 65200-65535 (v2 SKU) da **Internet**:

    a.  Na guia **Visão geral** do gateway do aplicativo, selecione o link **Rede Virtual/Sub-rede.**

    b.  Na guia **Subnets** da sua rede virtual, selecione a sub-rede onde o Gateway de aplicativo foi implantado.

    c.  Verifique se algum NSG está configurado.

    d.  Se um NSG estiver configurado, procure por esse recurso NSG na guia **Pesquisar** ou em **Todos os recursos**.

    e.  Na seção **Regras de Entrada,** adicione uma regra de entrada para permitir a faixa de porta de destino 65503-65534 para v1 SKU ou 65200-65535 v2 SKU com o conjunto **De Origem** como **Qualquer** ou **Internet**.

    f.  Selecione **Salvar** e verifique se você pode visualizar o backend como Saudável. Alternativamente, você pode fazer isso através [do PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

1.  Verifique se o seu UDR tem uma rota padrão (0.0.0.0/0) com o próximo salto não definido como **Internet**:
    
    a.  Siga as etapas 1a e 1b para determinar sua sub-rede.

    b.  Verifique se há algum UDR configurado. Se houver, procure o recurso na barra de pesquisa ou em **Todos os recursos**.

    c.  Verifique se há rotas padrão (0.0.0.0/0) com o próximo salto não definido como **Internet**. Se a configuração for **virtual appliance** ou virtual gateway de **rede,** você deve ter certeza de que seu dispositivo virtual ou o dispositivo local podem direcionar corretamente o pacote de volta para o destino da Internet sem modificar o pacote.

    d.  Caso contrário, altere o próximo salto para **Internet,** selecione **Salvar**e verifique a saúde do backend.

1.  Rota padrão anunciada pela conexão ExpressRoute/VPN à rede virtual sobre BGP:

    a.  Se você tiver uma conexão ExpressRoute/VPN com a rede virtual via BGP e se estiver anunciando uma rota padrão, você deve ter certeza de que o pacote é encaminhado de volta para o destino da Internet sem modificá-lo. Você pode verificar usando a opção **Solução de problemas de conexão** no portal Do gateway do aplicativo.

    b.  Escolha o destino manualmente como qualquer endereço IP que possa ser routable na Internet como 1.1.1.1. Defina a porta de destino como qualquer coisa e verifique a conectividade.

    c.  Se o próximo salto for gateway de rede virtual, pode haver uma rota padrão anunciada via ExpressRoute ou VPN.

1.  Se houver um servidor DNS personalizado configurado na rede virtual, verifique se o servidor (ou servidores) pode resolver domínios públicos. A resolução de nomes de domínio público pode ser necessária em cenários onde o Application Gateway deve entrar em contato com domínios externos, como servidores OCSP, ou verificar o status de revogação do certificado.

1.  Para verificar se o Portal de Aplicativos está saudável e funcionando, acesse a opção **Saúde de Recursos** no portal e verifique se o estado é **saudável.** Se você vir um estado **insalubre** ou **degradado,** entre [em contato com o suporte](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Próximas etapas
----------

Saiba mais sobre [os diagnósticos e registros do Application Gateway.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
