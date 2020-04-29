---
title: Pontuação segura na central de segurança do Azure
description: Descrição da Pontuação segura da central de segurança do Azure e seus controles de segurança
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2020
ms.author: memildin
ms.openlocfilehash: d7eea9cd83e72b6ffeaae319a8e87c065015e6b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79415815"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Pontuação segura aprimorada (visualização) na central de segurança do Azure

## <a name="introduction-to-secure-score"></a>Introdução à pontuação segura

A central de segurança do Azure tem dois objetivos principais: para ajudá-lo a entender sua situação de segurança atual e para ajudá-lo a melhorar sua segurança de forma eficiente e eficaz. O aspecto central da central de segurança que permite alcançar essas metas é a pontuação segura.

A central de segurança avalia continuamente seus recursos, suas assinaturas e a organização em busca de problemas de segurança. Em seguida, ele agrega todas as conclusões em uma única Pontuação para que você possa ver, rapidamente, sua situação de segurança atual: quanto maior a pontuação, menor o nível de risco identificado. Use a pontuação para acompanhar os esforços de segurança e os projetos em sua organização. 

A página de Pontuação segura da central de segurança inclui:

- **A pontuação** -a pontuação segura é mostrada como um valor percentual, mas os valores subjacentes também são claros:

    [![A pontuação segura mostrada como um valor de porcentagem com os números subjacentes também é clara](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **Controles de segurança** -cada controle é um grupo lógico de recomendações de segurança relacionadas e reflete as superfícies de ataque vulneráveis. Um controle é um conjunto de recomendações de segurança, com instruções que ajudam a implementar essas recomendações. Sua pontuação só melhora quando você corrige *todas* as recomendações para um único recurso dentro de um controle.

    Para ver imediatamente como sua organização está protegendo cada superfície de ataque individual, examine as pontuações de cada controle de segurança.

    Para obter mais informações, consulte [como a pontuação segura é calculada](secure-score-security-controls.md#how-the-secure-score-is-calculated) abaixo. 


>[!TIP]
> As versões anteriores da central de segurança receberam pontos no nível de recomendação: quando você corrigiu uma recomendação para um único recurso, sua pontuação segura melhorou. Hoje, sua pontuação só melhora se você corrigir *todas* as recomendações para um único recurso dentro de um controle. Portanto, sua pontuação só melhora quando você melhorou a segurança de um recurso.
> Embora essa versão aprimorada ainda esteja em versão prévia, a experiência de Pontuação segura anterior está disponível como uma opção no portal do Azure. 


## <a name="locating-your-secure-score"></a>Localizando sua pontuação segura

A central de segurança exibe sua pontuação de forma proeminente: é a primeira coisa mostrada na página Visão geral. Se você clicar na página de Pontuação segura dedicada, verá a pontuação dividida por assinatura. Clique em uma única assinatura para ver a lista detalhada de recomendações priorizadas e o impacto potencial que a correção terá na pontuação da assinatura.

## <a name="how-the-secure-score-is-calculated"></a>Como a pontuação segura é calculada 

A contribuição de cada controle de segurança em direção à pontuação segura geral é mostrada claramente na página recomendações.

[![A pontuação segura avançada apresenta os controles de segurança](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Para obter todos os pontos possíveis para um controle de segurança, todos os seus recursos devem estar em conformidade com todas as recomendações de segurança dentro do controle de segurança. Por exemplo, a central de segurança tem várias recomendações sobre como proteger suas portas de gerenciamento. No passado, você poderia corrigir algumas dessas recomendações relacionadas e interdependentes ao deixar outros não resolvidos e sua pontuação segura melhoraria. Quando olhamos objetivamente, é fácil argumentar que sua segurança não foi aprimorada até você ter resolvido todas elas. Agora, você deve corrigir todos eles para fazer uma diferença na sua pontuação segura.

Por exemplo, o controle de segurança chamado "aplicar atualizações do sistema" tem uma pontuação máxima de seis pontos, que você pode ver na dica de ferramenta sobre o valor de aumento potencial do controle:

[![O controle de segurança "aplicar atualizações do sistema"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

O potencial para o controle de segurança "aplicar atualizações do sistema" na captura de tela acima mostra "2% (1 ponto)". Isso significa que, se você corrigir todas as recomendações nesse controle, sua pontuação aumentará em 2% (nesse caso, um ponto). Para simplificar, os valores na coluna "aumento potencial" da lista de recomendações são arredondados para números inteiros. As dicas de ferramenta mostram os valores precisos:

* **Pontuação máxima** – o número máximo de pontos que você pode obter ao concluir todas as recomendações dentro de um controle. A pontuação máxima de um controle indica o significado relativo desse controle. Use os valores máximos de Pontuação para fazer a triagem de quais problemas trabalharem primeiro. 
* **Possível aumento** -os pontos restantes disponíveis para você dentro do controle. Para obter esses pontos adicionados à sua pontuação segura, corrija todas as recomendações do controle. No exemplo acima, o ponto mostrado para o controle é, na verdade, 0,96 pontos.
* **Pontuação atual** -a pontuação atual para este controle. Cada controle contribui para a pontuação total. Neste exemplo, o controle está contribuindo com 5, 4 pontos para o total. 

### <a name="calculations---understanding-your-score"></a>Cálculos-noções básicas sobre sua pontuação

|Métrica|Fórmula e exemplo|
|-|-|
|**Pontuação atual do controle de segurança**|<br>![Equação para calcular a pontuação atual do controle de segurança](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Cada controle de segurança individual contribui para a pontuação de segurança. Cada recurso afetado por uma recomendação dentro do controle, contribui para a pontuação atual do controle. A pontuação atual de cada controle é uma medida do status dos recursos *dentro* do controle.<br>![Dicas de ferramenta mostrando os valores usados ao calcular a pontuação atual do controle de segurança](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Neste exemplo, a pontuação máxima de 6 seria dividida por 78 porque essa é a soma dos recursos íntegros e não íntegros.<br>6/78 = 0, 769<br>Multiplicar isso pelo número de recursos íntegros (4) resulta na pontuação atual:<br>0, 769 * 4 = **0,31**<br><br>|
|**Classificação de segurança**<br>Assinatura única|<br>![Equação para calcular a pontuação segura atual](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Pontuação segura de assinatura única com todos os controles habilitados](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Neste exemplo, há uma única assinatura com todos os controles de segurança disponíveis (uma pontuação máxima potencial de 60 pontos). A pontuação mostra 28 pontos de um possível 60 e os 32 pontos restantes são refletidos nas figuras de "aumento da Pontuação potencial" dos controles de segurança.<br>![Lista de controles e o aumento de Pontuação potencial](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Classificação de segurança**<br>Várias assinaturas|<br>A pontuação atual de todos os recursos em todas as assinaturas é adicionada e o cálculo é o mesmo que para uma única assinatura<br><br>Ao exibir várias assinaturas, a pontuação segura avalia todos os recursos em todas as políticas habilitadas e agrupa seu impacto combinado na pontuação máxima de cada controle de segurança.<br>![Pontuação segura para várias assinaturas com todos os controles habilitados](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>A pontuação combinada **não** é uma média; em vez disso, é a postura avaliada do status de todos os recursos em todas as assinaturas.<br>Aqui também, se você for para a página recomendações e adicionar os pontos potenciais disponíveis, verá que é a diferença entre a pontuação atual (24) e a pontuação máxima disponível (60).|
||||

## <a name="improving-your-secure-score"></a>Melhorando sua pontuação segura

Para melhorar sua pontuação segura, corrija as recomendações de segurança da sua lista de recomendações. Você pode corrigir cada recomendação manualmente para cada recurso ou usando a **correção rápida!** opção (quando disponível) para aplicar rapidamente uma correção para uma recomendação a um grupo de recursos. Para obter mais informações, consulte [corrigir recomendações](security-center-remediate-recommendations.md).

>[!IMPORTANT]
> Somente as recomendações internas têm um impacto na pontuação segura.

## <a name="security-controls-and-their-recommendations"></a>Controles de segurança e suas recomendações

A tabela a seguir lista os controles de segurança na central de segurança do Azure. Para cada controle, você pode ver o número máximo de pontos que pode adicionar à sua pontuação segura se você corrigir *todas* as recomendações listadas no controle para *todos os* seus recursos. 

> [!TIP]
> Se você quiser filtrar ou classificar essa lista de forma diferente, copie e cole-a no Excel.

|Controle de segurança|Máximo de pontos de Pontuação segura|Recomendações|
|----------------|:-------------------:|---------------|
|**Habilitar MFA**|10|-A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura<br>-A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura<br>-A MFA deve ser habilitada para contas com permissões de gravação em sua assinatura|
|**Proteger portas de gerenciamento**|8|-O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais<br>-As máquinas virtuais devem ser associadas a um grupo de segurança de rede<br>-As portas de gerenciamento devem ser fechadas em suas máquinas virtuais|
|**Aplicar atualizações do sistema**|6|-Problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores<br>-O agente de monitoramento deve ser instalado em conjuntos de dimensionamento de máquinas virtuais<br>-O agente de monitoramento deve ser instalado em seus computadores<br>-A versão do sistema operacional deve ser atualizada para suas funções de serviço de nuvem<br>-As atualizações do sistema em conjuntos de dimensionamento de máquinas virtuais devem ser instaladas<br>-As atualizações do sistema devem ser instaladas em seus computadores<br>-Os computadores devem ser reiniciados para aplicar atualizações do sistema<br>-Os serviços Kubernetess devem ser atualizados para uma versão não vulnerável do kubernetes<br>-O agente de monitoramento deve ser instalado em suas máquinas virtuais|
|**Corrigir vulnerabilidades**|6|-A segurança de dados avançada deve ser habilitada em seus servidores SQL<br>-As vulnerabilidades nas imagens do registro de contêiner do Azure devem ser corrigidas<br>-As vulnerabilidades em seus bancos de dados SQL devem ser corrigidas<br>-Vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade<br>-A avaliação de vulnerabilidade deve ser habilitada em suas instâncias gerenciadas do SQL<br>-A avaliação de vulnerabilidade deve ser habilitada em seus servidores SQL<br>-A solução de avaliação de vulnerabilidade deve ser instalada em suas máquinas virtuais|
|**Habilitar criptografia em repouso**|4|-A criptografia de disco deve ser aplicada em máquinas virtuais<br>-Transparent Data Encryption em bancos de dados SQL deve ser habilitado<br>-As variáveis da conta de automação devem ser criptografadas<br>-Service Fabric clusters devem ter a propriedade ClusterProtectionLevel definida como EncryptAndSign<br>-O protetor de TDE do SQL Server deve ser criptografado com sua própria chave|
|**Criptografar dados em trânsito**|4|-O aplicativo de API só deve ser acessível via HTTPS<br>-Aplicativo de funções só deve ser acessível via HTTPS<br>-Somente as conexões seguras para o cache Redis devem ser habilitadas<br>-A transferência segura para contas de armazenamento deve ser habilitada<br>-O aplicativo Web só deve ser acessível via HTTPS|
|**Gerenciar acesso e permissões**|4|-Um máximo de 3 proprietários deve ser designado para sua assinatura<br>-As contas preteridas devem ser removidas da sua assinatura (versão prévia)<br>-Contas preteridas com permissões de proprietário devem ser removidas da sua assinatura (versão prévia)<br>-Contas externas com permissões de proprietário devem ser removidas da sua assinatura (versão prévia)<br>-Contas externas com permissões de leitura devem ser removidas da sua assinatura<br>-Contas externas com permissões de gravação devem ser removidas da sua assinatura (versão prévia)<br>-Deve haver mais de um proprietário atribuído à sua assinatura<br>-RBAC (controle de acesso baseado em função) deve ser usado em serviços Kubernetess (versão prévia)<br>-Service Fabric clusters só devem usar Azure Active Directory para autenticação de cliente|
|**Corrigir configurações de segurança**|4|-As políticas de segurança de Pod devem ser definidas nos serviços Kubernetess<br>-Vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas<br>-Vulnerabilidades na configuração de segurança em seus computadores devem ser corrigidas<br>-As vulnerabilidades na configuração de segurança em seus conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas<br>-O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>-O agente de monitoramento deve ser instalado em seus computadores<br>-O agente de monitoramento deve ser instalado em conjuntos de dimensionamento de máquinas virtuais<br>-Problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores|
|**Restringir o acesso não autorizado à rede**|4|-O encaminhamento IP em sua máquina virtual deve ser desabilitado<br>-Os intervalos de IP autorizados devem ser definidos nos serviços Kubernetess (versão prévia)<br>-(Preterido) o acesso aos serviços de aplicativo deve ser restrito (versão prévia)<br>-(Preterido) as regras para aplicativos Web em IaaS NSGs devem ser protegidas<br>-As máquinas virtuais devem ser associadas a um grupo de segurança de rede<br>-CORS não deve permitir que todos os recursos acessem seu aplicativo de API<br>-CORS não deve permitir que todos os recursos acessem seu Aplicativo de funções<br>-CORS não deve permitir que todos os recursos acessem seu aplicativo Web<br>-A depuração remota deve ser desativada para o aplicativo de API<br>-A depuração remota deve ser desativada para Aplicativo de funções<br>-A depuração remota deve ser desativada para o aplicativo Web<br>-O acesso deve ser restrito para grupos de segurança de rede permissivos com VMs voltadas para a Internet<br>-As regras de grupo de segurança de rede para máquinas virtuais voltadas para a Internet devem ser protegidas|
|**Aplicar controle de aplicativo adaptável**|3|-Os controles de aplicativo adaptáveis devem ser habilitados em máquinas virtuais<br>-O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>-O agente de monitoramento deve ser instalado em seus computadores<br>-Problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores|
|**Aplicar classificação de dados**|2|-Os dados confidenciais em seus bancos de dado SQL devem ser classificados (versão prévia)|
|**Proteger aplicativos contra ataques de DDoS**|2|-A proteção contra DDoS Standard deve ser habilitada|
|**Habilitar o Endpoint Protection**|2|-As falhas de integridade do Endpoint Protection devem ser corrigidas nos conjuntos de dimensionamento de máquinas virtuais<br>-Problemas de integridade do Endpoint Protection devem ser resolvidos em seus computadores<br>-A solução Endpoint Protection deve ser instalada em conjuntos de dimensionamento de máquinas virtuais<br>-Instalar a solução Endpoint Protection em máquinas virtuais<br>-Problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores<br>-O agente de monitoramento deve ser instalado em conjuntos de dimensionamento de máquinas virtuais<br>-O agente de monitoramento deve ser instalado em seus computadores<br>-O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>-Instalar a solução Endpoint Protection em seus computadores|
|**Habilitar auditoria e registro em log**|1|-A auditoria no SQL Server deve ser habilitada<br>-Os logs de diagnóstico nos serviços de aplicativo devem ser habilitados<br>-Os logs de diagnóstico no Azure Data Lake Store devem ser habilitados<br>-Os logs de diagnóstico no Azure Stream Analytics devem ser habilitados<br>-Os logs de diagnóstico em contas do lote devem ser habilitados<br>-Os logs de diagnóstico no Data Lake Analytics devem ser habilitados<br>-Os logs de diagnóstico no Hub de eventos devem ser habilitados<br>-Os logs de diagnóstico no Hub IoT devem ser habilitados<br>-Os logs de diagnóstico no Key Vault devem ser habilitados<br>-Os logs de diagnóstico em aplicativos lógicos devem ser habilitados<br>-Os logs de diagnóstico no serviço de pesquisa devem ser habilitados<br>-Os logs de diagnóstico no barramento de serviço devem ser habilitados<br>-Os logs de diagnóstico em conjuntos de dimensionamento de máquinas virtuais devem ser habilitados<br>-As regras de alerta de métrica devem ser configuradas em contas do lote<br>-As configurações de auditoria do SQL devem ter grupos de ações configurados para capturar atividades críticas<br>-Os servidores SQL devem ser configurados com dias de retenção de auditoria maiores que 90 dias.|
|**Implementar práticas recomendadas de segurança**|0|-O acesso a contas de armazenamento com firewall e configurações de rede virtual deve ser restrito<br>-Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do namespace do hub de eventos<br>-Um administrador de Azure Active Directory deve ser provisionado para servidores SQL<br>-As regras de autorização na instância do hub de eventos devem ser definidas<br>-As contas de armazenamento devem ser migradas para novos recursos de Azure Resource Manager<br>-As máquinas virtuais devem ser migradas para novos recursos de Azure Resource Manager<br>-As configurações de segurança de dados avançadas para o SQL Server devem conter um endereço de email para receber alertas de segurança<br>-A segurança de dados avançada deve ser habilitada em suas instâncias gerenciadas<br>-Todos os tipos de proteção avançada contra ameaças devem ser habilitados na instância gerenciada do SQL configurações de segurança de dados avançadas<br>-Notificações por email para administradores e proprietários de assinatura devem ser habilitados nas configurações de segurança de dados avançada do SQL Server<br>-Tipos de proteção avançada contra ameaças devem ser definidos como ' todos ' nas configurações de segurança de dados avançadas do SQL Server<br>-As sub-redes devem ser associadas a um grupo de segurança de rede<br>-Todos os tipos de proteção avançada contra ameaças devem ser habilitados nas configurações de segurança de dados avançada do SQL Server|
||||

## <a name="secure-score-faq"></a>FAQ de Pontuação segura

### <a name="why-has-my-secure-score-gone-down"></a>Por que minha pontuação segura desapareceu?
A central de segurança mudou para uma pontuação segura aprimorada (atualmente em status de visualização), que inclui alterações na forma como a pontuação é calculada. Agora, você deve resolver todas as recomendações para que um recurso receba pontos. As pontuações também foram alteradas para uma escala de 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Se eu abordar apenas três de quatro recomendações em um controle de segurança, minha pontuação de proteção será alterada?
Não. Ele não será alterado até que você corrija todas as recomendações para um único recurso. Para obter a pontuação máxima de um controle, você deve corrigir todas as recomendações, para todos os recursos.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>A experiência anterior da Pontuação segura ainda está disponível? 
Sim. Por um tempo, eles serão executados lado a lado para facilitar a transição. Espere que o modelo anterior seja dividido no tempo. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Se uma recomendação não for aplicável a mim e eu desabilitá-la na política, meu controle de segurança será atendido e minha pontuação segura é atualizada?
Sim. É recomendável desabilitar as recomendações quando elas não são aplicáveis em seu ambiente. Para obter instruções sobre como desabilitar uma recomendação específica, consulte [Disable Security Policies](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Se um controle de segurança me oferecer zero pontos em direção à minha pontuação segura, devo ignorá-lo?
Em alguns casos, você verá uma pontuação máxima de controle maior que zero, mas o impacto é zero. Quando a pontuação incremental para corrigir recursos é insignificante, ela é arredondada para zero. Não ignore essas recomendações, pois elas ainda trazem melhorias de segurança. A única exceção é o controle "prática recomendada adicional". A correção dessas recomendações não aumentará sua pontuação, mas irá aprimorar a segurança geral.

## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu a pontuação de segurança e os controles de segurança que ele apresenta. Para obter material relacionado, consulte os seguintes artigos:

- [Saiba mais sobre os diferentes elementos de uma recomendação](security-center-recommendations.md)
- [Saiba como corrigir recomendações](security-center-remediate-recommendations.md)