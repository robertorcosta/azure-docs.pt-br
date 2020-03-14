---
title: Azure Active Directory de detecções de riscos | Microsoft Docs
description: Este artice fornece uma visão geral detalhada de quais detecções de risco são.
services: active-directory
keywords: proteção de identidade do azure active directory, segurança, risco, nível de risco, vulnerabilidade, política de segurança
author: MarkusVi
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1f3755d61b5fa082665cfdb9aa91d1e31e2d4e4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266475"
---
# <a name="azure-active-directory-risk-detections"></a>Azure Active Directory de detecções de risco

A grande maioria das violações de segurança ocorre quando os invasores conseguem acessar a um ambiente roubando a identidade de um usuário. Descobrir identidades comprometidas não é uma tarefa fácil. O Azure Active Directory usa algoritmos de aprendizado de máquina e heurística adaptáveis para detectar ações suspeitas relacionadas às contas do usuário. Cada ação suspeita detectada é armazenada em um registro chamado **detecção de risco**.

Há dois locais em que você revisa as detecções de risco relatadas:

 - **Relatórios do Azure ad** – as detecções de risco fazem parte dos relatórios de segurança do Azure AD. Para obter mais informações, consulte o [relatório de segurança de usuários em risco](concept-user-at-risk.md) e o [relatório de segurança de entradas arriscadas](concept-risky-sign-ins.md).

 - As detecções de **Azure ad Identity Protection** risco também fazem parte dos recursos de relatório do [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

Além disso, você pode usar a [API de detecções de risco da proteção de identidade](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) para obter acesso programático a detecções de segurança usando o Microsoft Graph. Para obter mais informações, consulte [Introdução ao Azure Active Directory Identity Protection e Microsoft Graph](../identity-protection/graph-get-started.md). 

Atualmente, Azure Active Directory detecta seis tipos de detecções de risco:

- [Usuários com credenciais vazadas](#leaked-credentials) 
- [Entradas de endereços de IP anônimos](#sign-ins-from-anonymous-ip-addresses) 
- [Viagem impossível a locais atípicos](#impossible-travel-to-atypical-locations) 
- [Entradas de dispositivos infectados](#sign-ins-from-infected-devices) 
- [Entradas de endereços de IP com atividade suspeita](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Entradas de locais desconhecidos](#sign-in-from-unfamiliar-locations) 

![Detecção de riscos](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Às vezes, você pode encontrar uma detecção de risco sem uma entrada de logon correspondente no [relatório](concept-sign-ins.md)de entradas. Isso ocorre porque o Identity Protection avalia o risco para as entradas **interativa** e **não interativa**, enquanto o relatório de entradas mostra apenas as entradas interativas.

O insight que você obtém para uma detecção de risco detectado está vinculado à sua assinatura do Azure AD. 

* Com o **Azure AD Premium P2 Edition**, você obtém as informações mais detalhadas sobre todas as detecções subjacentes. 
* Com a **edição P1 do Azure ad Premium**, as detecções avançadas (como propriedades de entrada desconhecidas) não são cobertas por sua licença e aparecerão sob a entrada do nome **com risco adicional detectado**. Além disso, os campos nível de risco e detalhe de risco estão ocultos.

Embora a detecção de detecções de riscos já represente um aspecto importante da proteção de suas identidades, você também tem a opção de solucioná-los manualmente ou implementar respostas automatizadas Configurando políticas de acesso condicional. Para obter mais informações, consulte [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-detection-types"></a>Tipos de detecção de risco

A propriedade **tipo de detecção de risco** é um identificador para a ação suspeita para a qual um registro de detecção de risco foi criado.

Os investimentos contínuos da Microsoft no processo de detecção levaram a:

- Melhorias na precisão de detecção de detecções de risco existentes 
- Novos tipos de detecção de riscos que serão adicionados no futuro

### <a name="leaked-credentials"></a>Credenciais vazadas

Quando criminosos cibernéticos comprometem senhas válidas de usuários legítimos, geralmente compartilham essas credenciais. Geralmente, isso é feito postando-as publicamente na dark Web ou em paste sites, ou então permutando-as ou vendendo-as no mercado negro. O serviço de credenciais vazadas da Microsoft adquire pares de nome de usuário / senha pelo monitoramento de sites públicos e da dark Web, e também trabalhando com:

- Pesquisadores
- Representantes legais
- Equipes de segurança da Microsoft
- Outras fontes confiáveis 

Quando o serviço obtém pares de nome de usuário / senha, eles são verificados em relação a credenciais válidas atuais de usuários do AAD. Quando uma correspondência é encontrada, isso significa que a senha de um usuário foi comprometida e uma **detecção de risco de credenciais vazadas** é criada.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Entradas de endereços IP anônimos

Esse tipo de detecção de risco identifica os usuários que entraram com êxito de um endereço IP que foi identificado como um endereço IP de proxy anônimo. Esses proxies geralmente são usados por usuários que desejam ocultar o endereço IP de seu dispositivo e podem ser usados com objetivos mal-intencionados.

### <a name="impossible-travel-to-atypical-locations"></a>Viagem impossível a locais atípicos

Esse tipo de detecção de risco identifica duas entradas provenientes de locais geograficamente distantes, em que pelo menos um dos locais também pode ser atípicos para o usuário, dado o comportamento passado. Entre muitos outros fatores, esse algoritmo de aprendizado de máquina leva em consideração o tempo entre as duas entradas e o tempo que seria necessário para o usuário ir do primeiro até o segundo local, indicando que um usuário diferente está usando as mesmas credenciais.

Esse algoritmo ignora “falsos positivos” óbvios que contribuem para condições impossíveis de viagem, como VPNs e locais regularmente usados por outros usuários na organização. O sistema tem um período inicial de aprendizado de 14 dias, durante o qual ele assimila o comportamento de entrada do novo usuário. 

### <a name="sign-in-from-unfamiliar-locations"></a>Entrada de locais desconhecidos

Esse tipo de detecção de risco considera os locais de entrada anteriores (IP, latitude/longitude e ASN) para determinar os locais novos/desconhecidos. O sistema armazena informações sobre locais anteriores usados por um usuário e considera esses locais "familiares". A detecção de risco é disparada quando a entrada ocorre de um local que ainda não está na lista de locais familiares. O sistema tem um período inicial de aprendizado de 30 dias, durante o qual não sinaliza nenhuma nova localização como desconhecida. O sistema também ignora entradas de dispositivos conhecidos e locais que são geograficamente próximos de uma localização familiar. 

O Identity Protection detecta entradas de locais desconhecidos também para autenticação básica/protocolos herdados. Como esses protocolos não possuem recursos conhecidos modernos, como ID do cliente, não há telemetria suficiente para reduzir os falsos positivos. Para reduzir o número de detecções de risco detectadas, você deve mudar para a autenticação moderna.   

### <a name="sign-ins-from-infected-devices"></a>Entradas de dispositivos infectados

Esse tipo de detecção de risco identifica entradas de dispositivos infectados com malware, que são conhecidos por se comunicar ativamente com um servidor de bot. Isso é determinado ao correlacionar os endereços IP do dispositivo do usuário com os endereços IP que estavam em contato com um servidor de bot. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Entradas de endereços IP com atividade suspeita
Esse tipo de detecção de risco identifica os endereços IP dos quais um grande número de tentativas de entrada com falha foram vistas, em várias contas de usuário, em um curto período de tempo. Isso corresponde aos padrões de tráfego de endereços IP usados por invasores e é um forte indicador de que contas já se encontram comprometidas ou estão prestes a ser. Esse é um algoritmo de aprendizado de máquina que ignora falsos positivos óbvios como endereços IP que são regularmente usados por outros usuários na organização.  O sistema tem um período inicial de aprendizado de 14 dias, no qual ele assimila o comportamento de entrada do novo usuário e do novo locatário.

## <a name="detection-type"></a>Tipo de detecção

A propriedade de tipo de detecção é um indicador (**em tempo real** ou **offline**) para o período de tempo de detecção de uma detecção de risco. Atualmente, a maioria das detecções de riscos é detectada offline em uma operação de pós-processamento após a detecção de riscos.

A tabela a seguir lista a quantidade de tempo que leva para um tipo de detecção aparecer em um relatório relacionado:

| Tipo de detecção | Relatório de latência |
| --- | --- |
| Tempo real | De 5 a 10 minutos |
| Offline | De 2 a 4 horas |


Para os tipos de detecção de risco Azure Active Directory detecta, os tipos de detecção são:

| Tipo de detecção de risco | Tipo de detecção |
| :-- | --- | 
| [Usuários com credenciais vazadas](#leaked-credentials) | Offline |
| [Entradas de endereços de IP anônimos](#sign-ins-from-anonymous-ip-addresses) | Tempo real |
| [Viagem impossível a locais atípicos](#impossible-travel-to-atypical-locations) | Offline |
| [Entradas de locais desconhecidos](#sign-in-from-unfamiliar-locations) | Tempo real |
| [Entradas de dispositivos infectados](#sign-ins-from-infected-devices) | Offline |
| [Entradas de endereços de IP com atividade suspeita](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Nível de risco

A propriedade nível de risco de uma detecção de risco é um indicador (**alta**, **média**ou **baixa**) para a severidade e a confiança de uma detecção de risco. Essa propriedade ajuda você a priorizar as ações que deve executar. 

A severidade da detecção de riscos representa a intensidade do sinal como um indicador de comprometimento de identidade. A confiança é um indicador para a possibilidade de falsos positivos. 

Por exemplo, 

* **Alta**: alta confiança e detecção de risco de alta gravidade. Esses eventos são fortes indicadores de que a identidade do usuário foi comprometida e as contas de usuário afetadas devem ser corrigidas imediatamente.

* **Médio**: alta gravidade, mas a detecção de riscos de confiança mais baixa, ou vice-versa. Esses eventos são potencialmente arriscados e quaisquer contas de usuário afetadas devem ser corrigidas.

* **Baixo**: baixa confiança e baixa gravidade de risco de severidade. Esse evento pode não exigir uma ação imediata, mas, quando combinado com outras detecções de risco, pode fornecer uma forte indicação de que a identidade é comprometida.

![Nível de risco](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Credenciais vazadas

As detecções de risco de credenciais vazadas são classificadas como **altas**, pois elas fornecem uma clara indicação de que o nome de usuário e a senha estão disponíveis para um invasor.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Entradas de endereços IP anônimos

O nível de risco para esse tipo de detecção de risco é **médio** porque um endereço IP anônimo não é uma indicação forte de um comprometimento de conta. Recomendamos contatar o usuário imediatamente para verificar se ele estava usando endereços IP anônimos.


### <a name="impossible-travel-to-atypical-locations"></a>Viagem impossível a locais atípicos

Viagens impossíveis geralmente são um bom indicativo de que um hacker conseguiu entrar com êxito. No entanto, podem ocorrer falsos positivos quando um usuário estiver viajando usando um novo dispositivo ou usando uma VPN que normalmente não é usada por outros usuários na organização. Outra fonte de falsos positivos são os aplicativos que transmitem IPs de servidor incorretamente como IPs de cliente, o que pode dar a entender que as entradas estão ocorrendo do datacenter em que o aplicativo de back-end está hospedado (geralmente são datacenters Microsoft, o que dá a entender que as entradas estão ocorrendo de endereços IP de propriedade da Microsoft). Como resultado desses falsos positivos, o nível de risco para essa detecção de risco é **médio**.

> [!TIP]
> Você pode reduzir a quantidade de falsos positivos relatados para esse tipo de detecção de risco Configurando [locais nomeados](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Entrada de locais desconhecidos

Locais desconhecidos podem fornecer uma forte indicação de que um invasor é capaz de usar uma identidade roubada. Falsos positivos podem ocorrer quando um usuário está viajando, experimentando um novo dispositivo ou usando uma nova VPN. Como resultado desses falsos positivos, o nível de risco desse tipo de evento é **Médio**.

### <a name="sign-ins-from-infected-devices"></a>Entradas de dispositivos infectados

Essa detecção de risco identifica os endereços IP e não os dispositivos de usuário. Se vários dispositivos estiverem atrás de um único endereço IP e apenas alguns forem controlados por uma rede de bot, as entradas de outros dispositivos meu disparam esse evento desnecessariamente, e é por isso que essa detecção de risco é classificada como **baixa**.  

Recomendamos entrar em contato com o usuário e verificar todos os seus dispositivos. Também é possível que o dispositivo pessoal de um usuário esteja infectado ou que outra pessoa esteja usando um dispositivo infectado com o mesmo endereço IP do usuário. Os dispositivos infectados geralmente são infectados por malwares que ainda não foram identificados pelo software antivírus e também podem indicar os maus hábitos do usuário que podem ter causado a infecção do dispositivo.

Para obter mais informações sobre como abordar infecções por malware, consulte o [Centro de Proteção contra Malware](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/).

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Entradas de endereços IP com atividade suspeita

Recomendamos contatar o usuário para verificar se ele realmente entrou de um endereço IP que foi marcado como suspeito. O nível de risco desse tipo de evento é “**Médio**”, pois vários dispositivos podem estar atrás do mesmo endereço IP, enquanto apenas alguns podem ser responsáveis pela atividade suspeita. 


## <a name="next-steps"></a>Próximas etapas

* [Usuários em relatório de segurança de risco](concept-user-at-risk.md)
* [Relatório de segurança de entradas arriscadas](concept-risky-sign-ins.md)
* [Azure AD Identity Protection](../active-directory-identityprotection.md).
