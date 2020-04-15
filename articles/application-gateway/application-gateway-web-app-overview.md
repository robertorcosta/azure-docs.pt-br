---
title: Extremidades traseiras de vários inquilinos
titleSuffix: Azure Application Gateway
description: Esta página fornece uma visão geral do suporte a vários sites do Gateway de Aplicativo para back-ends com vários locatários.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: a171dc795e685655b5a3c73d088d3963c2aaa4ae
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312315"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Suporte ao Application Gateway para back ends de vários locatários, como serviço de aplicativo

Em projetos arquitetônicos de vários locatários em servidores web, vários sites estão sendo executados na mesma instância do servidor web. Os nomes de host são usados para diferenciar entre os diferentes aplicativos hospedados. Por padrão, o gateway de aplicativo não altera o cabeçalho de host HTTP recebido do cliente e envia o cabeçalho inalterado para o back-end. Isso funciona bem para membros do pool backend, como NICs, conjuntos de escala de máquinavirtual, endereços IP públicos, endereços IP internos e FQDN, pois estes não dependem de um cabeçalho de host específico ou extensão SNI para resolver até o ponto final correto. No entanto, existem muitos serviços, como aplicativos web de serviço do Azure App e gerenciamento de API do Azure que são de natureza multi-inquilino e dependem de um cabeçalho de host específico ou extensão SNI para resolver até o ponto final correto. Normalmente, o nome DNS do aplicativo, que por sua vez é o nome DNS associado ao gateway do aplicativo, é diferente do nome de domínio do serviço back-end. Portanto, o cabeçalho host na solicitação original recebida pelo gateway de aplicativo não é o mesmo que o nome de host do serviço backend. Por causa disso, a menos que o cabeçalho host na solicitação do gateway de aplicativo para o backend seja alterado para o nome de host do serviço backend, os backends de vários inquilinos não são capazes de resolver a solicitação para o ponto final correto. 

O Gateway de Aplicativo fornece uma funcionalidade que permite aos usuários substituir o cabeçalho do host HTTP na solicitação com base no nome do host do back-end. Esse recurso habilita o suporte a back-ends com vários locatários, como os aplicativos Web do Azure e ao gerenciamento de APIs. Esse recurso está disponível para as SKUs v1 e v2 padrão e WAF. 

![dispositivo de hospedagem](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Isso não se aplica ao ambiente de serviços do Azure App (ASE), uma vez que o ASE é um recurso dedicado ao contrário do serviço do Azure App, que é um recurso de vários locatários.

## <a name="override-host-header-in-the-request"></a>Substituir o cabeçalho do host na solicitação

A capacidade de especificar uma substituição de host é definida nas [configurações HTTP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) e pode ser aplicada a qualquer pool de back-end durante a criação de regras. As duas maneiras a seguir de sobrepor o cabeçalho do host e a extensão SNI para back ends de vários inquilinos são suportadas:

- A capacidade de definir o nome do host como um valor fixo foi inserida explicitamente nas configurações HTTP. Esse recurso garante que o cabeçalho do host seja substituído a esse valor para todo o tráfego para o pool de back-end onde as configurações HTTP particulares são aplicadas. Ao usar o TLS de ponta a ponta, este nome de host substituído é usado na extensão SNI. Esse recurso habilita cenários onde um farm de pool de back-end espera um cabeçalho de host que seja diferente do cabeçalho de host de entrada do cliente.

- A capacidade de derivar o nome do host do IP ou FQDN dos membros do pool de back-end. As configurações HTTP também fornecem uma opção para escolher dinamicamente o nome do host a partir do FQDN de um membro do pool de back-end se configurado com a opção de derivar o nome do host de um membro de pool de back-end individual. Ao usar o TLS de ponta a ponta, este nome host é derivado do FQDN e é usado na extensão SNI. Esse recurso habilita cenários em que um pool de back-end pode ter dois ou mais serviços PaaS com vários locatários, como aplicativos Web do Azure, e o cabeçalho de host da solicitação para cada membro pode conter o nome de host derivado do seu FQDN. Para implementar este cenário, usamos um switch nas configurações HTTP chamado [Pick hostname a partir do endereço backend](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) que substituirá dinamicamente o cabeçalho do host na solicitação original ao mencionado no pool de backend.  Por exemplo, se o seu pool de backend FQDN contiver "contoso11.azurewebsites.net" e "contoso22.azurewebsites.net", o cabeçalho de host da solicitação original, que é contoso.com, será substituído para contoso11.azurewebsites.net ou contoso22.azurewebsites.net quando a solicitação for enviada para o servidor backend apropriado. 

  ![cenário de aplicativo Web](./media/application-gateway-web-app-overview/scenario.png)

Com essa funcionalidade, os clientes especificam as opções de configuração apropriadas das investigações personalizadas e das configurações de HTTP. Essa configuração é, em seguida, vinculada a um ouvinte e a um pool de back-end por meio de uma regra.

## <a name="special-considerations"></a>Considerações especiais

### <a name="tls-termination-and-end-to-end-tls-with-multi-tenant-services"></a>TLS termina e fim a fim do TLS com serviços multilocatários

Tanto a criptografia TLS de término quanto de ponta a ponta tLS é suportada com serviços de vários inquilinos. Para o término do TLS no gateway de aplicativo, o certificado TLS continua a ser necessário para ser adicionado ao ouvinte do gateway do aplicativo. No entanto, em caso de TLS de ponta a ponta, serviços confiáveis do Azure, como os aplicativos web de serviço do Azure App, não exigem a listagem branca dos backends no gateway do aplicativo. Portanto, não há necessidade de adicionar quaisquer certificados de autenticação. 

![TLS de ponta a ponta](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Observe que na imagem acima, não há necessidade de adicionar certificados de autenticação quando o serviço do App é selecionado como backend.

### <a name="health-probe"></a>Investigação de integridade

A substituição do cabeçalho do host nas **configurações HTTP** afeta apenas a solicitação e seu roteamento. não afeta o comportamento da sonda de saúde. Para a funcionalidade de ponta a ponta funcionar, a investigação e as configurações de HTTP devem ser modificadas para refletir a configuração correta. Além de fornecer a capacidade de especificar um cabeçalho host na configuração do teste, os testes personalizados também suportam a capacidade de derivar o cabeçalho do host das configurações HTTP configuradas no momento. Essa configuração pode ser especificada usando o parâmetro `PickHostNameFromBackendHttpSettings` na configuração da investigação.

### <a name="redirection-to-app-services-url-scenario"></a>Redirecionamento para o cenário de URL do App Service

Pode haver cenários em que o nome de host na resposta do serviço app pode direcionar o navegador do usuário final para o nome de host *.azurewebsites.net em vez do domínio associado ao Gateway do aplicativo. Este problema pode acontecer quando:

- Você tem o redirecionamento configurado no seu Serviço de Aplicativo. O redirecionamento pode ser tão simples quanto adicionar uma barra de arrasto à solicitação.
- Você tem autenticação Azure AD que causa o redirecionamento.

Para resolver esses casos, consulte [Redirecionamento de solução de problemas para o problema de URL do serviço do App](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar um gateway de aplicativo com um aplicativo multi-inquilino, como o aplicativo web do serviço Azure App, como membro do pool back-end, visitando [aplicativos web do Configure App Service com o Application Gateway](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal)
