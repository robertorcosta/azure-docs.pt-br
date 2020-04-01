---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 652d42d6e2d9e909c3a03bd82a3a36f91bc73807
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419528"
---
O recurso é fácil de configurar, mas isso não significa que sua experiência será livre de problemas. Se você encontrar problemas para acessar o ponto final desejado, existem alguns utilitários que você pode usar para testar a conectividade a partir do console do aplicativo. Há dois consoles que você pode usar. Um é o console Kudu, e o outro é o console no portal Azure. Para acessar o console Kudu a partir do seu aplicativo, vá para **Tools** > **Kudu**. Você também pode acessar o console Kudo em [sitename].scm.azurewebsites.net. Depois que o site for carregado, vá para a guia **Debug console.** Para chegar ao console hospedado no portal Azure a partir do seu aplicativo, vá para **Tools** > **Console**.

#### <a name="tools"></a>Ferramentas
As ferramentas **ping**, **nslookup**e **tracert** não funcionarão através do console por causa de restrições de segurança. Para preencher o vazio, duas ferramentas separadas são adicionadas. Para testar a funcionalidade do DNS, adicionamos uma ferramenta chamada **nameresolver.exe**. A sintaxe do é:

    nameresolver.exe hostname [optional: DNS Server]

Você pode usar a nameresolver para verificar os nomes de host de que seu aplicativo depende. Desta forma, você pode testar se você tem algo desconfigurado com o seu DNS ou talvez não tenha acesso ao seu servidor DNS. Você pode ver o servidor DNS que seu aplicativo usa no console olhando para as variáveis ambientais WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

Você pode usar a próxima ferramenta para testar a conectividade TCP a uma combinação de host e porta. Essa ferramenta é chamada **tcpping** e a sintaxe é:

    tcpping.exe hostname [optional: port]

O **utilitário tcpping** informa se você pode alcançar um host e porta específicos. Ele só pode mostrar sucesso se houver um aplicativo ouvindo na combinação host e porta, e há acesso de rede do seu aplicativo ao host e porta especificados.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Depurar o acesso a recursos virtuais hospedados em rede
Uma série de coisas podem impedir que seu aplicativo chegue a um host e porta específicos. Na maioria das vezes é uma dessas coisas:

* **Há um firewall no caminho.** Se você tiver um firewall no caminho, você atinge o tempo de intervalo do TCP. O tempo limite de TCP é 21 segundos neste caso. Use a ferramenta **tcpping** para testar a conectividade. Os tempos de intervalo do TCP podem ser causados por muitas coisas além dos firewalls, mas comecem por aí.
* **O DNS não está acessível.** O tempo de intervalo do DNS é de 3 segundos por servidor DNS. Se você tiver dois servidores DNS, o tempo limite será de seis segundos. Use nameresolver para ver se o DNS está funcionando. Você não pode usar nslookup, porque isso não usa o DNS com o que sua rede virtual está configurada. Se inacessível, você pode ter um firewall ou NSG bloqueando o acesso ao DNS ou ele pode estar desligado.

Se esses itens não responderem aos seus problemas, procure primeiro por coisas como:

**Integração VNet Regional**
* Seu destino é um endereço não RFC1918 e você não tem WEBSITE_VNET_ROUTE_ALL definido como 1?
* Existe um NSG bloqueando a saída da sua sub-rede de integração?
* Se você está atravessando o Azure ExpressRoute ou uma VPN, o seu gateway no local está configurado para direcionar o tráfego de volta para o Azure? Se você pode alcançar pontos finais em sua rede virtual, mas não no local, verifique suas rotas.
* Você tem permissões suficientes para definir a delegação na sub-rede de integração? Durante a configuração regional de Integração VNet, sua sub-rede de integração é delegada ao Microsoft.Web. A UI de integração VNet delega a sub-rede ao Microsoft.Web automaticamente. Se sua conta não tiver permissões de rede suficientes para definir a delegação, você precisará de alguém que possa definir atributos em sua sub-rede de integração para delegar a sub-rede. Para delegar manualmente a sub-rede de integração, vá até a ui da rede virtual do Azure e defina a delegação para Microsoft.Web.

**Integração VNet necessária ao Gateway**
* A faixa de endereço ponto a local nas faixas RFC 1918 (10.0.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* O gateway mostra como estar no portal? Se o gateway estiver inativo, ative-o.
* Os certificados mostram como sendo em sincronia, ou você suspeita que a configuração da rede foi alterada?  Se seus certificados estiverem fora de sincronia ou você suspeitar que uma alteração foi feita na configuração da rede virtual que não foi sincronizada com suas ASPs, selecione **Sync Network**.
* Se você está atravessando uma VPN, o gateway local está configurado para direcionar o tráfego de volta para o Azure? Se você pode alcançar pontos finais em sua rede virtual, mas não no local, verifique suas rotas.
* Você está tentando usar um gateway de coexistência que suporte tanto o ponto para o site quanto o ExpressRoute? Os gateways de coexistência não são suportados com a Integração VNet.

Depurar problemas de rede é um desafio porque você não pode ver o que está bloqueando o acesso a uma combinação específica de host:port. Algumas causas incluem:

* Você tem um firewall no seu host que impede o acesso à porta de aplicativo a partir da sua faixa IP ponto a ponto. Cruzar sub-redes muitas vezes requer acesso público.
* Seu alvo está abatido.
* Sua inscrição foi baixa.
* Você tinha o IP ou o nome do host errados.
* Seu aplicativo está ouvindo em uma porta diferente do que você esperava. Você pode corresponder a sua ID de processo com a porta de escuta usando "netstat -aon" no host do ponto de extremidade.
* Seus grupos de segurança de rede são configurados de tal forma que impedem o acesso ao host e à porta do seu aplicativo a partir do seu intervalo IP ponto a ponto.

Você não sabe qual endereço seu aplicativo realmente usa. Pode ser qualquer endereço na sub-rede de integração ou na faixa de endereço ponto a ponto, então você precisa permitir o acesso de toda a gama de endereços.

As etapas de depuração adicionais incluem:

* Conecte-se a uma VM em sua rede virtual e tente alcançar seu host de recursos:porta a partir daí. Para testar o acesso TCP, use o comando do PowerShell **test-netconnection**. A sintaxe do é:

      test-netconnection hostname [optional: -Port]

* Traga um aplicativo em uma VM e teste o acesso a esse host e porta a partir do console a partir do seu aplicativo usando **tcpping**.

#### <a name="on-premises-resources"></a>Recursos locais ####

Se o seu aplicativo não conseguir alcançar um recurso no local, verifique se você pode acessar o recurso a partir de sua rede virtual. Use o comando do PowerShell **test-netconnection** para verificar se há acesso TCP. Se a VM não conseguir acessar seu recurso local, sua conexão VPN ou ExpressRoute pode não estar configurada corretamente.

Se sua VM virtual hospedada em rede pode entrar em contato com seu sistema local, mas seu aplicativo não pode, a causa provavelmente é uma das seguintes razões:

* Suas rotas não estão configuradas com as faixas de endereço situado na sub-rede ou ponto a ponto no gateway local.
* Seus grupos de segurança de rede estão bloqueando o acesso para sua faixa ip ponto a local.
* Seus firewalls no local estão bloqueando o tráfego do seu intervalo IP ponto a ponto.
* Você está tentando alcançar um endereço não RFC 1918 usando o recurso regional VNet Integration.