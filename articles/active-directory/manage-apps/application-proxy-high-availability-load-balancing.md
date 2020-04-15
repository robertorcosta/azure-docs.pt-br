---
title: Alta disponibilidade e balanceamento de carga - Proxy de aplicativo Ad do Azure
description: Como a distribuição de tráfego funciona com a implantação do Proxy do aplicativo. Inclui dicas de como otimizar o desempenho do conector e usar o balanceamento de carga para servidores back-end.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 992075378737552e890bd2d6fed3c519e6c62aa7
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312942"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Alta disponibilidade e balanceamento de carga de seus conectores e aplicativos proxy de aplicativos

Este artigo explica como a distribuição de tráfego funciona com a implantação do Proxy do aplicativo. Vamos discutir:

- Como o tráfego é distribuído entre usuários e conectores, juntamente com dicas para otimizar o desempenho do conector

- Como o tráfego flui entre conectores e servidores de aplicativos back-end, com recomendações para balanceamento de carga entre vários servidores back-end

## <a name="traffic-distribution-across-connectors"></a>Distribuição de tráfego entre conectores

Os conectores estabelecem suas conexões com base em princípios de alta disponibilidade. Não há garantia de que o tráfego será sempre distribuído uniformemente entre os conectores e não há afinidade de sessão. No entanto, o uso varia e as solicitações são enviadas aleatoriamente para instâncias de serviço proxy de aplicativo. Como resultado, o tráfego é normalmente distribuído quase uniformemente entre os conectores. O diagrama e as etapas abaixo ilustram como as conexões são estabelecidas entre usuários e conectores.

![Diagrama mostrando conexões entre usuários e conectores](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. Um usuário em um dispositivo cliente tenta acessar um aplicativo local publicado através do Proxy do aplicativo.
2. A solicitação passa por um Balanceador de carga do Azure para determinar qual instância de serviço proxy de aplicativo deve levar a solicitação. Por região, há dezenas de instâncias disponíveis para aceitar a solicitação. Esse método ajuda a distribuir uniformemente o tráfego nas instâncias de serviço.
3. A solicitação é enviada para [a Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/).
4. Os sinais de ônibus de serviço para um conector disponível. O conector então pega a solicitação do Service Bus.
   - Na etapa 2, as solicitações vão para diferentes instâncias de serviço proxy de aplicativo, de modo que as conexões são mais propensas a serem feitas com conectores diferentes. Como resultado, os conectores são quase uniformemente usados dentro do grupo.
5. O conector passa a solicitação para o servidor back-end do aplicativo. Em seguida, o aplicativo envia a resposta de volta para o conector.
6. O conector completa a resposta abrindo uma conexão de saída para a instância de serviço de onde a solicitação veio. Então essa conexão é imediatamente fechada. Por padrão, cada conector é limitado a 200 conexões simultâneas de saída.
7. A resposta é então repassada ao cliente a partir da instância de serviço.
8. Solicitações subseqüentes da mesma conexão repetem as etapas acima.

Um aplicativo muitas vezes tem muitos recursos e abre várias conexões quando está carregado. Cada conexão passa pelas etapas acima para ser alocada em uma instância de serviço, selecione um novo conector disponível se a conexão ainda não tiver sido emparelhada anteriormente com um conector.


## <a name="best-practices-for-high-availability-of-connectors"></a>Melhores práticas para alta disponibilidade de conectores

- Devido à forma como o tráfego é distribuído entre conectores para alta disponibilidade, é essencial ter sempre pelo menos dois conectores em um grupo de conectores. Três conectores são preferidos para fornecer buffer adicional entre os conectores. Para determinar o número correto de conectores necessários, siga a documentação de planejamento de capacidade.

- Coloque conectores em diferentes conexões de saída para evitar um único ponto de falha. Se os conectores usarem a mesma conexão de saída, um problema de rede com a conexão pode afetar todos os conectores que a utilizam.

- Evite forçar os conectores a reiniciar quando conectados a aplicativos de produção. Isso pode impactar negativamente a distribuição do tráfego entre os conectores. A reinicialização dos conectores faz com que mais conectores não estejam disponíveis e força as conexões ao conector disponível restante. O resultado é um uso desigual dos conectores inicialmente.

- Evite todas as formas de inspeção inline nas comunicações TLS de saída entre conectores e Azure. Este tipo de inspeção inline causa degradação ao fluxo de comunicação.

- Certifique-se de manter as atualizações automáticas em execução para seus conectores. Se o serviço application Proxy Connector Updater estiver sendo executado, seus conectores serão atualizados automaticamente e receberão o mais recente atualizado. Caso você não veja o serviço Atualizador do Conector no servidor, precisará reinstalar o conector para obter todas as atualizações.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Fluxo de tráfego entre conectores e servidores de aplicativos back-end

Outra área-chave onde a alta disponibilidade é um fator é a conexão entre conectores e os servidores back-end. Quando um aplicativo é publicado através do Proxy do aplicativo Azure AD, o tráfego dos usuários para os aplicativos flui através de três saltos:

1. O usuário se conecta ao ponto final público do serviço proxy do aplicativo Azure AD no Azure. A conexão é estabelecida entre o endereço IP do cliente de origem (público) do cliente e o endereço IP do ponto final do Proxy do aplicativo.
2. O conector Proxy do aplicativo retira a solicitação HTTP do cliente do Serviço proxy do aplicativo.
3. O conector Proxy do aplicativo se conecta ao aplicativo de destino. O conector usa seu próprio endereço IP para estabelecer a conexão.

![Diagrama de conexão do usuário a um aplicativo via Proxy de Aplicativo](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-Encaminhado para considerações de campo de cabeçalho
Em algumas situações (como auditoria, balanceamento de carga etc.), compartilhar o endereço IP de origem do cliente externo com o ambiente local é um requisito. Para atender ao requisito, o conector proxy do aplicativo Azure AD adiciona o campo de cabeçalho X-Forwarded-For com o endereço IP do cliente de origem (público) à solicitação HTTP. O dispositivo de rede apropriado (balanceador de carga, firewall) ou o servidor web ou aplicativo back-end podem então ler e usar as informações.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Práticas recomendadas para balanceamento de carga entre vários servidores de aplicativos
Quando o grupo de conectores atribuídoao aplicativo Proxy de aplicativo tem dois ou mais conectores, e você está executando o aplicativo web back-end em vários servidores (fazenda de servidores), uma boa estratégia de balanceamento de carga é necessária. Uma boa estratégia garante que os servidores peguem as solicitações dos clientes uniformemente e previne a utilização excessiva ou subutilização dos servidores na fazenda de servidores.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Cenário 1: A aplicação back-end não requer persistência da sessão
O cenário mais simples é quando o aplicativo web back-end não requer pegajosa de sessão (persistência da sessão). Qualquer solicitação do usuário pode ser tratada por qualquer instância de aplicativo back-end na fazenda do servidor. Você pode usar um balanceador de carga de camada 4 e configurá-lo sem afinidade. Algumas opções incluem o Balanceamento de carga de rede da Microsoft e o Balanceador de Carga Azure ou um balanceador de carga de outro fornecedor. Alternativamente, o DNS round-robin pode ser configurado.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Cenário 2: A aplicação back-end requer persistência da sessão
Neste cenário, o aplicativo web back-end requer pegajosa de sessão (persistência da sessão) durante a sessão autenticada. Todas as solicitações do usuário devem ser tratadas pela instância do aplicativo back-end que é executada no mesmo servidor na fazenda do servidor.
Esse cenário pode ser mais complicado porque o cliente geralmente estabelece várias conexões ao serviço Proxy de aplicativo. Solicitações sobre diferentes conexões podem chegar a diferentes conectores e servidores na fazenda. Como cada conector usa seu próprio endereço IP para esta comunicação, o balanceador de carga não pode garantir a pegajosa da sessão com base no endereço IP dos conectores. A Affinity IP de origem também não pode ser usada.
Aqui estão algumas opções para o cenário 2:

- Opção 1: Baseie a persistência da sessão em um cookie de sessão definido pelo balanceador de carga. Essa opção é recomendada porque permite que a carga seja espalhada de forma mais uniforme entre os servidores back-end. Ele requer um balanceador de carga de camada 7 com este recurso e que pode lidar com o tráfego HTTP e encerrar a conexão TLS. Você pode usar o Azure Application Gateway (Session Affinity) ou um balanceador de carga de outro fornecedor.

- Opção 2: Baseie a persistência da sessão no campo de cabeçalho X-Forwarded-For. Esta opção requer um balanceador de carga de camada 7 com esse recurso e que pode lidar com o tráfego HTTP e encerrar a conexão TLS.  

- Opção 3: Configure o aplicativo back-end para não exigir persistência da sessão.

Consulte a documentação do seu fornecedor de software para entender os requisitos de balanceamento de carga do aplicativo back-end.

## <a name="next-steps"></a>Próximas etapas

- [Habilitar Proxy de aplicativo](application-proxy-add-on-premises-application.md)
- [Habilitar o logon único](application-proxy-configure-single-sign-on-with-kcd.md)
- [Habilitar acesso condicional](application-proxy-integrate-with-sharepoint-server.md)
- [Solucionar problemas que surgirem com o Proxy de Aplicativo](application-proxy-troubleshoot.md)
- [Saiba como a arquitetura Azure AD suporta alta disponibilidade](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)
