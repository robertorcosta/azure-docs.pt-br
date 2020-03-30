---
title: Pontuação segura no Azure Security Center
description: Descrição da pontuação segura do Azure Security Center e seus controles de segurança
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415815"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Pontuação segura aprimorada (visualização) no Azure Security Center

## <a name="introduction-to-secure-score"></a>Introdução à pontuação segura

O Azure Security Center tem dois objetivos principais: ajudá-lo a entender sua situação de segurança atual e ajudá-lo a melhorar sua segurança de forma eficiente e eficaz. O aspecto central do Security Center que permite alcançar esses objetivos é a pontuação segura.

O Security Center avalia continuamente seus recursos, assinaturas e organização para problemas de segurança. Em seguida, ele agrega todas as descobertas em uma única pontuação para que você possa dizer, de relance, a sua situação de segurança atual: quanto maior a pontuação, menor o nível de risco identificado. Use a pontuação para acompanhar os esforços e projetos de segurança em sua organização. 

A página de pontuação segura do Security Center inclui:

- **A pontuação** - A pontuação segura é mostrada como um valor percentual, mas os valores subjacentes também são claros:

    [![Pontuação segura mostrada como um valor percentual com os números subjacentes claros também](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **Controles de segurança** - Cada controle é um grupo lógico de recomendações de segurança relacionadas, e reflete suas superfícies de ataque vulneráveis. Um controle é um conjunto de recomendações de segurança, com instruções que ajudam a implementar essas recomendações. Sua pontuação só melhora quando você remedia *todas as* recomendações para um único recurso dentro de um controle.

    Para ver imediatamente o quão bem sua organização está protegendo cada superfície de ataque individual, revise as pontuações de cada controle de segurança.

    Para obter mais informações, consulte [Como a pontuação segura é calculada](secure-score-security-controls.md#how-the-secure-score-is-calculated) abaixo. 


>[!TIP]
> Versões anteriores do Security Center atribuíram pontos no nível de recomendação: quando você remedia uma recomendação para um único recurso, sua pontuação segura melhorou. Hoje, sua pontuação só melhora se você remediar *todas as* recomendações para um único recurso dentro de um controle. Assim, sua pontuação só melhora quando você melhora a segurança de um recurso.
> Enquanto esta versão aprimorada ainda está em visualização, a experiência de pontuação segura anterior está disponível como uma opção do Portal Azure. 


## <a name="locating-your-secure-score"></a>Localizando sua pontuação segura

O Security Center exibe sua pontuação com destaque: é a primeira coisa mostrada na página Visão Geral. Se você clicar na página de pontuação segura dedicada, verá a pontuação dividida por assinatura. Clique em uma única assinatura para ver a lista detalhada de recomendações priorizadas e o impacto potencial que corrigi-las terá na pontuação da assinatura.

## <a name="how-the-secure-score-is-calculated"></a>Como a pontuação segura é calculada 

A contribuição de cada controle de segurança para a pontuação geral segura é mostrada claramente na página de recomendações.

[![A pontuação segura aprimorada introduz controles de segurança](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Para obter todos os pontos possíveis para um controle de segurança, todos os seus recursos devem cumprir todas as recomendações de segurança dentro do controle de segurança. Por exemplo, o Security Center tem várias recomendações sobre como proteger suas portas de gerenciamento. No passado, você poderia remediar algumas dessas recomendações relacionadas e interdependentes, deixando outras sem solução, e sua pontuação segura melhoraria. Quando olhado objetivamente, é fácil argumentar que sua segurança não melhorou até que você tenha resolvido todos eles. Agora, você deve remediar todos eles para fazer a diferença para a sua pontuação segura.

Por exemplo, o controle de segurança chamado "Aplicar atualizações do sistema" tem uma pontuação máxima de seis pontos, que você pode ver na dica de ferramenta sobre o potencial aumento do valor do controle:

[![O controle de segurança "Aplicar atualizações do sistema"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

O potencial para o controle de segurança "Aplicar atualizações do sistema" na captura de tela acima mostra "2% (1 Ponto)". Isso significa que se você remediar todas as recomendações neste controle, sua pontuação aumentará em 2% (neste caso, um ponto). Para simplificar, os valores na coluna "Aumento potencial" da lista de recomendações são arredondados para números inteiros. As dicas de ferramentas mostram os valores precisos:

* **Pontuação máxima** - O número máximo de pontos que você pode ganhar completando todas as recomendações dentro de um controle. A pontuação máxima para um controle indica o significado relativo desse controle. Use os valores máximos de pontuação para triagem em quais problemas trabalhar primeiro. 
* **Aumento potencial** - Os pontos restantes disponíveis para você dentro do controle. Para obter esses pontos adicionados à sua pontuação segura, remediar todas as recomendações do controle. No exemplo acima, o único ponto mostrado para o controle é, na verdade, 0,96 pontos.
* **Pontuação atual** - A pontuação atual para este controle. Cada controle contribui para a pontuação total. Neste exemplo, o controle está contribuindo com 5,04 pontos para o total. 

### <a name="calculations---understanding-your-score"></a>Cálculos - entendendo sua pontuação

|Métrica|Fórmula e exemplo|
|-|-|
|**Pontuação atual do controle de segurança**|<br>![Equação para calcular a pontuação atual de um controle de segurança](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Cada controle de segurança individual contribui para o Score de Segurança. Cada recurso afetado por uma recomendação dentro do controle contribui para a pontuação atual do controle. A pontuação atual para cada controle é uma medida do status dos recursos *dentro* do controle.<br>![Dicas de ferramentas mostrando os valores utilizados no cálculo da pontuação atual do controle de segurança](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Neste exemplo, a pontuação máxima de 6 seria dividida por 78 porque essa é a soma dos recursos saudáveis e insalubres.<br>6 / 78 = 0,0769<br>Multiplicar isso pelo número de recursos saudáveis (4) resulta na pontuação atual:<br>0,0769 * 4 = **0,31**<br><br>|
|**Pontuação segura**<br>Assinatura única|<br>![Equação para calcular a pontuação segura atual](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Pontuação segura de assinatura única com todos os controles ativados](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Neste exemplo, há uma única assinatura com todos os controles de segurança disponíveis (uma pontuação máxima potencial de 60 pontos). A pontuação mostra 28 pontos de um possível 60 e os 32 pontos restantes se refletem nos números de "Potencial aumento de pontuação" dos controles de segurança.<br>![Lista de controles e o aumento da pontuação potencial](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Pontuação segura**<br>Várias assinaturas|<br>A pontuação atual para todos os recursos em todas as assinaturas são adicionadas e o cálculo é então o mesmo que para uma única assinatura<br><br>Ao visualizar várias assinaturas, o secure score avalia todos os recursos dentro de todas as políticas habilitadas e agrupa seu impacto combinado na pontuação máxima de cada controle de segurança.<br>![Pontuação segura para várias assinaturas com todos os controles ativados](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>A pontuação combinada **não** é uma média; em vez disso, é a postura avaliada do status de todos os recursos em todas as assinaturas.<br>Aqui também, se você for à página de recomendações e somar os pontos potenciais disponíveis, você verá que é a diferença entre a pontuação atual (24) e a pontuação máxima disponível (60).|
||||

## <a name="improving-your-secure-score"></a>Melhorando sua pontuação segura

Para melhorar sua pontuação segura, remediar as recomendações de segurança da sua lista de recomendações. Você pode remediar cada recomendação manualmente para cada recurso, ou usando o **Quick Fix!** opção (quando disponível) aplicar uma remediação para uma recomendação a um grupo de recursos rapidamente. Para obter mais informações, consulte [Recomendações de Reparação](security-center-remediate-recommendations.md).

>[!IMPORTANT]
> Apenas recomendações incorporadas têm um impacto na pontuação segura.

## <a name="security-controls-and-their-recommendations"></a>Controles de segurança e suas recomendações

A tabela abaixo lista os controles de segurança do Azure Security Center. Para cada controle, você pode ver o número máximo de pontos que você pode adicionar à sua pontuação segura se você remediar *todas as* recomendações listadas no controle, para *todos os* seus recursos. 

> [!TIP]
> Se você quiser filtrar ou classificar esta lista de forma diferente, copie e cole-a no Excel.

|Controle de segurança|Pontos de pontuação máximas e seguros|Recomendações|
|----------------|:-------------------:|---------------|
|**Habilitar MFA**|10|- O MFA deve ser habilitado em contas com permissões do proprietário em sua assinatura<br>- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura<br>- MFA deve ser habilitado contas com permissões de gravação em sua assinatura|
|**Portas de gerenciamento seguras**|8|- O controle de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais<br>- Máquinas virtuais devem ser associadas a um Grupo de Segurança de Rede<br>- As portas de gestão devem ser fechadas em suas máquinas virtuais|
|**Aplicar atualizações do sistema**|6|- Os problemas de saúde dos agentes de monitoramento devem ser resolvidos em suas máquinas<br>- O agente de monitoramento deve ser instalado em conjuntos de escala de máquinas virtuais<br>- O agente de monitoramento deve ser instalado em suas máquinas<br>- A versão do OS deve ser atualizada para suas funções de serviço em nuvem<br>- Atualizações do sistema em conjuntos de escala de máquinas virtuais devem ser instaladas<br>- Atualizações do sistema devem ser instaladas em suas máquinas<br>- Suas máquinas devem ser reiniciadas para aplicar atualizações do sistema<br>- Kubernetes Services deve ser atualizado para uma versão kubernetes não vulnerável<br>- O agente de monitoramento deve ser instalado em suas máquinas virtuais|
|**Corrigir vulnerabilidades**|6|- A segurança avançada de dados deve ser ativada em seus servidores SQL<br>- Vulnerabilidades em imagens do Registro de Contêineres do Azure devem ser corrigidas<br>- Vulnerabilidades em seus bancos de dados SQL devem ser corrigidas<br>- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades<br>- A avaliação da vulnerabilidade deve ser ativada em suas instâncias gerenciadas pelo SQL<br>- A avaliação de vulnerabilidades deve ser ativada em seus servidores SQL<br>- A solução de avaliação de vulnerabilidades deve ser instalada em suas máquinas virtuais|
|**Habilite a criptografia em repouso**|4|- A criptografia de disco deve ser aplicada em máquinas virtuais<br>- A criptografia de dados transparente em bancos de dados SQL deve ser habilitada<br>- Variáveis de conta de automação devem ser criptografadas<br>- Clusters de malha de serviço devem ter a propriedade ClusterProtectionLevel definida para EncryptAndSign<br>- O protetor TDE do servidor SQL deve ser criptografado com sua própria chave|
|**Criptografe dados em trânsito**|4|- O App API só deve ser acessível via HTTPS<br>- Function App só deve ser acessível em HTTPS<br>- Somente conexões seguras ao cache Redis devem ser ativadas<br>- A transferência segura para contas de armazenamento deve ser ativada<br>- A aplicação web só deve ser acessível em HTTPS|
|**Gerenciar acessos e permissões**|4|- Um máximo de 3 proprietários devem ser designados para sua assinatura<br>- Contas depreciadas devem ser removidas de sua assinatura (Visualização)<br>- Contas depreciadas com permissões de proprietário devem ser removidas de sua assinatura (Visualização)<br>- Contas externas com permissões de proprietário devem ser removidas de sua assinatura (Visualização)<br>- Contas externas com permissões de leitura devem ser removidas de sua assinatura<br>- Contas externas com permissões de gravação devem ser removidas de sua assinatura (Visualização)<br>- Deve haver mais de um proprietário designado para sua assinatura<br>- O RBAC (Role-Based Access Control, controle de acesso baseado em função) deve ser usado no Kubernetes Services (Preview)<br>- Os clusters de malha de serviço só devem usar o Azure Active Directory para autenticação de clientes|
|**Corrigir configurações de segurança**|4|- Políticas de segurança de pod devem ser definidas nos Serviços Kubernetes<br>- As vulnerabilidades nas configurações de segurança de contêineres devem ser corrigidas<br>- Vulnerabilidades na configuração de segurança em suas máquinas devem ser corrigidas<br>- Vulnerabilidades na configuração de segurança em seus conjuntos de escala de máquina virtual devem ser corrigidas<br>- O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>- O agente de monitoramento deve ser instalado em suas máquinas<br>- O agente de monitoramento deve ser instalado em conjuntos de escala de máquinas virtuais<br>- Os problemas de saúde dos agentes de monitoramento devem ser resolvidos em suas máquinas|
|**Restringir o acesso não autorizado à rede**|4|- O encaminhamento ip em sua máquina virtual deve ser desativado<br>- As faixas de IP autorizadas devem ser definidas nos Serviços Kubernetes (Preview)<br>- (DEPRECATED) O acesso aos Serviços de Aplicativos deve ser restrito (Visualização)<br>- (DEPRECATED) As regras para aplicações web em NSGs Iaas devem ser endurecidas<br>- Máquinas virtuais devem ser associadas a um Grupo de Segurança de Rede<br>- O CORS não deve permitir que todos os recursos acessem seu app de API<br>- O CORS não deve permitir que todos os recursos acessem seu Aplicativo de Função<br>- O CORS não deve permitir que todos os recursos acessem seu Aplicativo Web<br>- Depuração remota deve ser desligada para o App API<br>- Depuração remota deve ser descompletada para o Function App<br>- A depuração remota deve ser descompletada para o Aplicativo Web<br>- O acesso deve ser restrito para grupos de segurança de rede permissivos com VMs voltadas para a Internet<br>- As regras do Grupo de Segurança de Rede para máquinas virtuais voltadas para a Internet devem ser endurecidas|
|**Aplicar controle de aplicação adaptativo**|3|- Controles de aplicativos adaptativos devem ser ativados em máquinas virtuais<br>- O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>- O agente de monitoramento deve ser instalado em suas máquinas<br>- Os problemas de saúde dos agentes de monitoramento devem ser resolvidos em suas máquinas|
|**Aplicar classificação de dados**|2|- Dados confidenciais em seus bancos de dados SQL devem ser classificados (Visualização)|
|**Proteger aplicativos contra ataques DDoS**|2|- O Padrão de Proteção DDoS deve ser ativado|
|**Habilite a proteção de ponto final**|2|- Falhas de saúde de proteção de endpoint devem ser corrigidas em conjuntos de escala de máquinas virtuais<br>- Problemas de saúde de proteção de endpoint devem ser resolvidos em suas máquinas<br>- A solução de proteção de endpoint deve ser instalada em conjuntos de escala de máquinas virtuais<br>- Instalar solução de proteção de ponto final em máquinas virtuais<br>- Os problemas de saúde dos agentes de monitoramento devem ser resolvidos em suas máquinas<br>- O agente de monitoramento deve ser instalado em conjuntos de escala de máquinas virtuais<br>- O agente de monitoramento deve ser instalado em suas máquinas<br>- O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>- Instale a solução de proteção de ponto final em suas máquinas|
|**Permitir auditoria e registro**|1|- A auditoria no servidor SQL deve ser ativada<br>- Os logs de diagnóstico nos Serviços de Aplicativos devem ser ativados<br>- Os registros de diagnóstico no Azure Data Lake Store devem ser ativados<br>- Os registros de diagnóstico no Azure Stream Analytics devem ser ativados<br>- Os registros de diagnóstico em contas em lote devem ser ativados<br>- Os registros de diagnóstico no Data Lake Analytics devem ser ativados<br>- Os registros de diagnóstico no Event Hub devem ser ativados<br>- Os registros de diagnóstico no IoT Hub devem ser ativados<br>- Os registros de diagnóstico no Key Vault devem ser ativados<br>- Os logs de diagnóstico em Logic Apps devem ser ativados<br>- Os registros de diagnóstico no serviço de pesquisa devem ser habilitados<br>- Os registros de diagnóstico no Ônibus de Serviço devem ser ativados<br>- Os registros de diagnóstico em conjuntos de escala de máquinavirtual devem ser ativados<br>- As regras de alerta métrica devem ser configuradas em contas em lote<br>- As configurações de Auditoria SQL devem ter grupos de ação configurados para capturar atividades críticas<br>- Os servidores SQL devem ser configurados com dias de retenção de auditoria superiores a 90 dias.|
|**Implementar práticas recomendadas de segurança**|0|- O acesso a contas de armazenamento com configurações de firewall e rede virtual deve ser restrito<br>- Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do namespace do Event Hub<br>- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL<br>- As regras de autorização na instância do Event Hub devem ser definidas<br>- Contas de armazenamento devem ser migradas para novos recursos do Azure Resource Manager<br>- Máquinas virtuais devem ser migradas para novos recursos do Azure Resource Manager<br>- Configurações avançadas de segurança de dados para servidor SQL devem conter um endereço de e-mail para receber alertas de segurança<br>- A segurança avançada dos dados deve ser ativada em suas instâncias gerenciadas<br>- Todos os tipos avançados de proteção contra ameaças devem ser habilitados em configurações avançadas de segurança de dados gerenciadas pela SQL<br>- Notificações por e-mail para admins e proprietários de assinaturas devem ser habilitadas em configurações avançadas de segurança de dados do servidor SQL<br>- Os tipos avançados de proteção contra ameaças devem ser definidos como 'Todos' nas configurações avançadas de segurança de dados do servidor SQL<br>- As sub-redes devem ser associadas a um Grupo de Segurança de Rede<br>- Todos os tipos avançados de proteção contra ameaças devem ser ativados em configurações avançadas de segurança de dados do servidor SQL|
||||

## <a name="secure-score-faq"></a>Faq pontuação segura

### <a name="why-has-my-secure-score-gone-down"></a>Por que minha pontuação segura caiu?
O Security Center mudou para uma pontuação segura aprimorada (atualmente em status de visualização) que inclui alterações na forma como a pontuação é calculada. Agora, você deve resolver todas as recomendações para que um recurso receba pontos. As pontuações também mudaram para uma escala de 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Se eu abordar apenas três de quatro recomendações em um controle de segurança, minha pontuação segura mudará?
Não. Não mudará até que você remediatodas as recomendações para um único recurso. Para obter a pontuação máxima para um controle, você deve remediar todas as recomendações, para todos os recursos.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>A experiência anterior da pontuação segura ainda está disponível? 
Sim. Por um tempo eles estarão correndo lado a lado para facilitar a transição. Espere que o modelo anterior seja eliminado gradualmente. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Se uma recomendação não for aplicável a mim, e eu desabilitá-la na apólice, meu controle de segurança será cumprido e minha pontuação segura atualizada?
Sim. Recomendamos desativar recomendações quando elas são inaplicáveis em seu ambiente. Para obter instruções sobre como desativar uma recomendação específica, consulte [Desativar as políticas de segurança](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Se um controle de segurança me oferece zero pontos para a minha pontuação segura, devo ignorá-lo?
Em alguns casos, você verá um resultado máximo de controle maior que zero, mas o impacto é zero. Quando a pontuação incremental para fixação de recursos é insignificante, é arredondada para zero. Não ignore essas recomendações, pois elas ainda trazem melhorias de segurança. A única exceção é o controle "Práticas Recomendadas Adicionais". Remediar essas recomendações não aumentará sua pontuação, mas aumentará sua segurança geral.

## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu a pontuação segura e os controles de segurança que ele introduz. Para material relacionado, consulte os seguintes artigos:

- [Conheça os diferentes elementos de uma recomendação](security-center-recommendations.md)
- [Saiba como remediar recomendações](security-center-remediate-recommendations.md)