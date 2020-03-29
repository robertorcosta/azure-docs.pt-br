---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671534"
---
Embora o recurso seja fácil de configurar, isso não significa que sua experiência estará livre de problemas. Se você encontrar problemas ao acessar o ponto de extremidade desejado, há utilitários que você pode usar para testar a conectividade do console do aplicativo. Há dois consoles que você pode usar. Uma é o console do Kudu e a outra é o console no portal do Azure. Para acessar o console do Kudu do aplicativo, vá para Ferramentas -> Kudu. Você também pode acessar o console Kudo em [sitename].scm.azurewebsites.net. Uma vez que o site seja carregado, vá para a guia Debug console. Para chegar ao console hospedado no portal Azure, acesse o console de ferramentas -> Console. 

#### <a name="tools"></a>Ferramentas
As ferramentas **ping**, **nslookup**e **tracert** não funcionarão através do console devido a restrições de segurança. Para preencher o vazio, duas ferramentas separadas foram adicionadas. Para testar a funcionalidade do DNS, adicionamos uma ferramenta chamada nameresolver.exe. A sintaxe do é:

    nameresolver.exe hostname [optional: DNS Server]

Você pode usar **nameresolver** para verificar os nomes de host de que seu aplicativo depende. Dessa forma, você pode testar se não há nada mal configurado no DNS ou se não tem acesso ao seu servidor DNS. Você pode ver o servidor DNS que seu aplicativo usará no console olhando para as variáveis ambientais WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

A próxima ferramenta permite testar a conectividade do TCP de uma combinação de host e porta. Essa ferramenta é chamada **tcpping** e a sintaxe é:

    tcpping.exe hostname [optional: port]

O **utilitário tcpping** informa se você pode alcançar um host e porta específicos. Ele só pode mostrar êxito se: houver um aplicativo escutando na combinação de porta e host, e houver acesso de rede de seu aplicativo para o host e porta especificados.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Depurar o acesso a recursos hospedados por VNet
Há várias coisas que podem impedir que seu aplicativo alcance um host e uma porta específicos. Na maioria das vezes, é uma dentre três coisas:

* **Há um firewall no caminho.** Se houver um firewall no caminho, você atingirá o tempo limite de TCP. O tempo limite de TCP é 21 segundos neste caso. Use a ferramenta **tcpping** para testar a conectividade. Tempos limite de TCP podem ocorrer por muitos motivos, além de firewalls, mas comece com isso. 
* **O DNS não está acessível.** O tempo limite do DNS é de três segundos por servidor DNS. Se você tiver dois servidores DNS, o tempo limite será de seis segundos. Use nameresolver para ver se o DNS está funcionando. Lembre-se de que você não pode usar nslookup, pois ele não usa o DNS com o qual sua VNet está configurada. Se inacessível, você pode ter um firewall ou NSG bloqueando o acesso ao DNS ou ele pode estar desligado.

Se esses itens não responderem aos seus problemas, procure primeiro por coisas como: 

**Integração regional do VNet**
* seu destino é um endereço não RFC1918 e você não tem WEBSITE_VNET_ROUTE_ALL definido como 1?
* existe um NSG bloqueando a saída de sua sub-rede de integração?
* se passar pelo ExpressRoute ou por uma VPN, o gateway local está configurado para direcionar o tráfego de volta para o Azure? Se você pode alcançar pontos finais em seu VNet, mas não no local, verifique suas rotas.
* você tem permissões suficientes para definir a delegação na sub-rede de integração? Durante a configuração regional de Integração VNet, sua sub-rede de integração será delegada ao Microsoft.Web. A UI de integração VNet delegará a sub-rede para microsoft.web automaticamente. Se sua conta não tiver permissões de rede suficientes para definir a delegação, você precisará de alguém que possa definir atributos em sua sub-rede de integração para delegar a sub-rede. Para delegar manualmente a sub-rede de integração, vá até a ui da rede virtual do Azure e defina a delegação para microsoft.Web. 

**Gateway necessário VNet Integration**
* é a faixa de endereço ponto a local nas faixas RFC 1918 (10.0.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* O Gateway é mostrado como estando em execução no Portal? Se o gateway estiver inativo, ative-o.
* Os certificados mostram como estando em sincronia ou você suspeita que a configuração da rede foi alterada?  Se seus certificados estiverem fora de sincronia ou você suspeitar que houve uma alteração na configuração do VNet que não foi sincronizada com seus ASPs, então clique em "Sync Network".
* Se passar por uma VPN, o gateway local está configurado para direcionar o tráfego de volta para o Azure? Se você pode alcançar pontos finais em seu VNet, mas não no local, verifique suas rotas.
* Você está tentando usar um gateway de coexistência que suporte tanto o ponto para o site quanto o ExpressRoute? Os gateways de coexistência não são suportados com a Integração VNet.

Depurar problemas de rede é um desafio porque não é possível ver o que está bloqueando o acesso a uma combinação específica de host:port. Algumas das causas incluem:

* Você tem um firewall no host impedindo o acesso à porta de aplicativos do seu ponto para o intervalo IP do site. O cruzamento de sub-redes geralmente exige acesso Público.
* Seu alvo está abatido.
* Sua inscrição foi baixa.
* Você tinha o IP ou o nome do host errados.
* Seu aplicativo está ouvindo em uma porta diferente do que você esperava. Você pode corresponder a sua ID de processo com a porta de escuta usando "netstat -aon" no host do ponto de extremidade. 
* Seus grupos de segurança de rede são configurados de tal forma que impedem o acesso ao host e à porta do seu ponto para o local.

Lembre-se de que você não sabe qual endereço seu aplicativo realmente usará. Pode ser qualquer endereço na sub-rede de integração ou na faixa de endereço ponto a ponto, então você precisa permitir o acesso de toda a gama de endereços. 

As etapas de depuração adicionais incluem:

* Conecte-se a uma VM em seu VNet e tente alcançar seu host de recursos:porta a partir daí. Para testar o acesso TCP, use o comando do PowerShell **test-netconnection**. A sintaxe do é:

      test-netconnection hostname [optional: -Port]

* Traga um aplicativo em uma VM e teste o acesso a esse host e porta a partir do console a partir do seu aplicativo usando **tcpping**

#### <a name="on-premises-resources"></a>Recursos locais ####

Se o aplicativo não puder acessar um recurso local, verifique se é possível acessar o recurso da sua VNet. Use o comando do PowerShell **test-netconnection** para verificar se há acesso TCP. Se a VM não conseguir acessar seu recurso local, sua conexão VPN ou ExpressRoute pode não estar configurada corretamente.

Se a VM hospedada na VNet puder acessar seu sistema local, mas seu aplicativo não, isso provavelmente ocorrerá devido a um dos seguintes motivos:

* Suas rotas não estão configuradas com sua sub-rede ou apontar para as faixas de endereço do site no gateway local.
* Seus grupos de segurança de rede estão bloqueando o acesso à sua faixa IP point-to-site.
* Seus firewalls no local estão bloqueando o tráfego da faixa IP ponto a ponto.
* Você está tentando alcançar um endereço não RFC 1918 usando o recurso regional VNet Integration.