---
title: Novidades no Gateway de Aplicativo do Azure
description: Conheça as novidades do Gateway de Aplicativo do Azure, como as últimas notas sobre a versão, problemas conhecidos, correções de bug, funcionalidades preteridas e alterações futuras.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 06/10/2020
ms.author: victorh
ms.openlocfilehash: a98a06dedf1d0ed9b92cc2028dfc208ff26df056
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517613"
---
# <a name="whats-new-in-azure-application-gateway"></a>Novidades no Gateway de Aplicativo do Azure?

O Gateway de Aplicativo do Azure é atualizado continuamente. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug
- Funcionalidades preteridas

## <a name="new-features"></a>Novos recursos

|Recurso  |Descrição  |Data de adição  |
|---------|---------|---------|
| Nomes do host curinga em ouvintes (versão prévia) | Agora você pode definir nomes do host curinga em um ouvinte multissite e até cinco nomes do host por ouvinte. Para saber mais, confira [Nomes do host curinga no ouvinte (versão prévia)](multiple-site-overview.md#wildcard-host-names-in-listener-preview) e [Hospedar vários sites em um Gateway de Aplicativo usando o portal do Azure](create-multiple-sites-portal.md) do guia passo a passo. | Julho de 2020 |
| Reescrita de URL (versão prévia) | Agora você pode reescrever o caminho da URL e os parâmetros da cadeia de consulta usando a reescrita de URL. Confira [Reescrever os cabeçalhos HTTP e a URL](rewrite-http-headers-url.md) para saber mais e [Reescrever a URL no Gateway de Aplicativo usando o portal do Azure](rewrite-url-portal.md) do guia passo a passo. | Julho de 2020 |
| Porta personalizada para investigações de integridade | A SKU do Gateway de Aplicativo v2 agora oferece a capacidade de fornecer uma porta personalizada na configuração de investigação de integridade. Para obter mais informações, confira a [visão geral da investigação de integridade](application-gateway-probe-overview.md) | Julho de 2020 |
| Complemento do AKS do AGIC (Controlador de Entrada do Gateway de Aplicativo) (Versão prévia) |O Controlador de Entrada do Gateway de Aplicativo agora pode ser implantado como um complemento do AKS nativo em uma linha por meio da CLI do Azure. Ser um complemento do AKS permite que o AGIC se torne um serviço totalmente gerenciado enquanto ainda está em execução no cluster do AKS do cliente. Para obter mais informações, confira [Diferenças no complemento do AGIC](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). |Junho de 2020 |
| UDR (Rotas Definidas de Usuário) na v2 (versão prévia) |As rotas definidas pelo usuário agora são compatíveis em alguns cenários em SKUs do Gateway de Aplicativo v2. Para obter mais informações, confira [Visão geral da configuração do Gateway de Aplicativo](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet). |Março de 2020 |
|Alterações de cookie de afinidade |Quando a afinidade baseada em cookie está habilitada, o Gateway de Aplicativo injeta outro cookie idêntico chamado *ApplicationGatewayAffinityCORS* além do cookie ApplicationGatewayAffinity existente. O *ApplicationGatewayAffinityCORS* tem mais dois atributos adicionados a ele (*SameSite=None; Secure*), de modo, de modo que as sessões temporárias sejam mantidas mesmo para solicitações entre origens. Confira [Afinidade baseada em cookie do Gateway de Aplicativo](configuration-overview.md#cookie-based-affinity) para obter mais informações. |Fevereiro de 2020 |
|Melhorias na investigação |Com as melhorias na investigação personalizada no SKU do Gateway de Aplicativo v2, simplificamos a [configuração da investigação](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku), facilitamos [testes de integridade de back-end sob demanda](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) e adicionamos [mais informações de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) para ajudar você a solucionar problemas de integridade de back-end.  |Outubro de 2019 |
|Mais métricas |Adicionamos as seguintes novas métricas para ajudar você a monitorar seu SKU do Gateway de Aplicativo v2: [Métricas relacionadas a tempo](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), status de resposta do back-end, bytes recebidos, bytes enviados, protocolo TLS do cliente e unidades de computação atuais. Confira [Métricas com suporte do SKU do Gateway de Aplicativo V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Agosto de 2019 |
|Regras personalizadas de WAF |O WAF_v2 go Gateway de Aplicativo agora dá suporte à criação de regras personalizadas. Confira [Regras personalizadas do Gateway de Aplicativo](custom-waf-rules-overview.md). |Junho de 2019 |
|Disponibilidade geral de dimensionamento automático, redundância de zona e suporte ao VIP estático |Disponibilidade geral para o SKU v2, que dá suporte a dimensionamento automático, redundância de zona, melhoria de desempenho, VIPs estáticos, Key Vault e reescrita de cabeçalho. Confira [Documentação de dimensionamento automático do Gateway de Aplicativo](application-gateway-autoscaling-zone-redundant.md). |Abril de 2019 |
|Integração do Key Vault |Agora, o Gateway de Aplicativo dá suporte à integração com o Key Vault (em versão prévia) para certificados de servidor que estejam anexados a ouvintes habilitados para HTTPS. Confira [Encerramento de TLS com certificados do Key Vault](key-vault-certs.md). |Abril de 2019 |
|Cabeçalho CRUD/reescritas     |Agora é possível reescrever cabeçalhos HTTP. Confira o [Tutorial: Criar um gateway de aplicativo e reescrever cabeçalhos HTTP](tutorial-http-header-rewrite-powershell.md) para saber mais.|Dezembro de 2018|
|Configuração do WAF e lista de exclusões     |Adicionamos mais opções para ajudar você a configurar o WAF e reduzir falsos positivos. Para obter mais informações, confira [Limites de tamanho de solicitação e listas de exclusões do firewall do aplicativo Web](application-gateway-waf-configuration.md).|Dezembro de 2018|
|Dimensionamento automático, redundância de zona e suporte ao VIP estático      |Com o SKU v2, há muitas melhorias como o Dimensionamento automático, melhor desempenho e mais. Confira [O que é o Gateway de Aplicativo do Azure?](overview.md) para saber mais.|Setembro de 2018|
|Descarregamento de conexão     |O descarregamento de conexão permite que você remova membros normalmente dos seus pools de back-end. Para saber mais, confira [Connection draining](features.md#connection-draining) (Descarregamento de conexão).|Setembro de 2018|
|Páginas de erro personalizadas     |Com páginas de erro personalizadas, é possível criar uma página de erro dentro do formato do restante dos seus sites. Para habilitar isso, confira [Criar páginas de erro personalizadas do Gateway de Aplicativo](custom-error.md).|Setembro de 2018|
|Melhorias de métricas     |É possível obter uma melhor exibição do estado do seu Gateway de Aplicativo com métricas aprimoradas. Para habilitar as métricas no seu Gateway de Aplicativo, confira [Back-end health, diagnostic logs, and metrics for Application Gateway](application-gateway-diagnostics.md) (Integridade do back-end, logs de diagnóstico e métricas do Gateway de Aplicativo).|Junho de 2018|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Gateway de Aplicativo, confira [O que é o Gateway de Aplicativo do Azure?](overview.md)
