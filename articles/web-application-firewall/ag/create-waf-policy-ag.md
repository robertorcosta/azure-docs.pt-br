---
title: Crie políticas de WaF (Web Application Firewall) para gateway de aplicativos
description: Saiba como criar políticas de firewall de aplicativos da Web para o Application Gateway.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: 3e8cd2f1e594cd6a60296b2df135f275641df313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086972"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Criar políticas de firewall de aplicativos da Web para o Gateway de aplicativos

Associar uma política waf com ouvintes permite que vários sites por trás de um único WAF sejam protegidos por diferentes políticas. Por exemplo, se houver cinco sites atrás do SEU WAF, você pode ter cinco políticas WAF separadas (uma para cada ouvinte) para personalizar as exclusões, regras personalizadas e conjuntos de regras gerenciados para um site sem efetuar os outros quatro. Se você quiser que uma única política se aplique a todos os sites, você pode simplesmente associar a política ao Gateway de aplicativo, em vez dos ouvintes individuais, para fazê-la aplicar globalmente. As políticas também podem ser aplicadas a uma regra de roteamento baseada em caminhos. 

Você pode fazer quantas políticas quiser. Uma vez que você crie uma diretiva, ela deve ser associada a um Gateway de aplicativo para entrar em vigor, mas pode ser associada a qualquer combinação de Gateways de Aplicativo e ouvintes. 

Se o Gateway de aplicativo tiver uma política aplicada e, em seguida, você aplicar uma política diferente a um ouvinte no Gateway do aplicativo, a política do ouvinte entrará em vigor, mas apenas para os ouvintes a que eles são designados. A diretiva Application Gateway ainda se aplica a todos os outros ouvintes que não têm uma política específica atribuída a eles. 

   > [!NOTE]
   > As políticas de WAF por site e por URI estão em visualização pública. Isso significa que esse recurso está sujeito aos Termos de Uso Suplementares da Microsoft. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Todas as configurações de WAF do Novo Firewall do Aplicativo web (regras personalizadas, configurações de rulset gerenciadas, exclusões, etc.) vivem dentro de uma política WAF. Se você tiver um WAF existente, essas configurações ainda podem existir na configuração WAF. Para obter etapas sobre como mudar para a nova política waf, consulte [Migrar sua Configuração WAF para uma política WAF](#migrate) mais tarde neste artigo. 

## <a name="create-a-policy"></a>Criar uma política

Primeiro, crie uma diretiva WAF básica com um DRS (Default Rule Set, conjunto de regras padrão) gerenciado usando o portal Azure.

1. No lado superior esquerdo do portal, selecione **Criar um recurso**. Procure **waf,** selecione **O Firewall do Aplicativo da Web**e selecione **Criar**.
2. Ao criar uma página **de diretiva WAF,** a guia **Basics,** inserir ou selecionar as seguintes informações, aceitar os padrões para as configurações restantes e, em seguida, selecionar **'Revisar + criar':**

   |Configuração  |Valor  |
   |---------|---------|
   |Política para     |WAF regional (Gateway de aplicativo)|
   |Subscription     |Selecione seu nome de assinatura|
   |Resource group     |Selecione o grupo de recursos|
   |Nome de política     |Digite um nome exclusivo para sua política waf.|
3. Na guia **Associação,** digite uma das seguintes configurações e selecione **Adicionar**:

   |Configuração  |Valor  |
   |---------|---------|
   |Gateway de aplicativo associado     |Selecione o nome do perfil do Application Gateway.|
   |Ouvintes Associados     |Selecione o nome do ouvinte do gateway de aplicativo e selecione **Adicionar**.|

   > [!NOTE]
   > Se você atribuir uma diretiva ao seu Gateway de Aplicativo (ou ouvinte) que já tenha uma política em vigor, a diretiva original será substituída e substituída pela nova diretiva.
4. Selecione **Examinar + criar**e **Criar**.

   ![Noções básicas da política waf](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>Configure as regras do WAF (opcional)

Quando você cria uma diretiva WAF, por padrão ela está no modo *Detecção.* No modo Detecção, o WAF não bloqueia nenhuma solicitação. Em vez disso, as regras de WAF correspondentes são registradas nos registros waf. Para ver o WAF em ação, você pode alterar as configurações de modo para *Prevenção*. No modo Prevenção, as regras de correspondência definidas no conjunto de regras do CRS que você selecionou são bloqueadas e/ou registradas nos registros waf.

## <a name="managed-rules"></a>Regras gerenciadas

As regras OWASP gerenciadas pelo Azure são habilitadas por padrão. Para desativar uma regra individual dentro de um grupo de regras, expanda as regras dentro desse grupo de regras, selecione a caixa de seleção na frente do número da regra e **selecione Desativar** na guia acima.

[![Regras gerenciadas](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Regras personalizadas

Para criar uma regra personalizada, selecione **Adicionar regra personalizada** na guia Regras **personalizadas.** Isso abre a página de configuração de regras personalizadas. A captura de tela a seguir mostra uma regra personalizada de exemplo configurada para bloquear uma solicitação se a seqüência de consultas contiver o *bloco de texto .*

[![Editar regra](../media/create-waf-policy-ag/edit-custom-rule.png) personalizada](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>Migre seu WAF Config para uma política WAF

Se você tem um WAF existente, você pode ter notado algumas mudanças no portal. Primeiro você precisa identificar que tipo de Política você habilitou em seu WAF. Há três estados potenciais:

- Sem política waf
- Política apenas de regras personalizadas
- Política do WAF

Você pode dizer em que estado seu WAF está olhando para ele no portal. Se as configurações do WAF estiverem visíveis e puderem ser alteradas da exibição do Gateway de aplicativo, o WAF estará no estado 1.

[![Configuração](../media/create-waf-policy-ag/waf-configure.png) waf](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

Se você selecionar **o Web Application Firewall** e ele mostrar uma política associada, o WAF está no estado 2 ou no estado 3. Depois de navegar para a diretiva, se ele mostrar **apenas** regras personalizadas e Gateways de aplicativos associados, então é uma diretiva de regras personalizadas.

![Regras personalizadas de WAF](../media/create-waf-policy-ag/waf-custom-rules.png)

Se ele também mostrar configurações de diretiva e regras gerenciadas, então é uma política completa de Firewall de Aplicativos da Web. 

![Configurações de diretiva WAF](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Migrar para a Política waf

Se você tiver uma diretiva WAF de regras personalizadas, então você pode querer passar para a nova política waf. Daqui para frente, a diretiva de firewall suportará configurações de diretiva WAF, conjuntos de regras gerenciados, exclusões e grupos de regras desativados. Essencialmente, todas as configurações de WAF que foram feitas anteriormente dentro do Gateway de aplicativo são agora feitas através da Diretiva WAF. 

Edições à regra personalizada apenas a política WAF está desativada. Para editar quaisquer configurações do WAF, como desativar regras, adicionar exclusões, etc. você tem que migrar para um novo recurso de política de firewall de nível superior.

Para isso, crie uma *Política de Firewall de Aplicativos da Web* e associe-a ao(s) gateway(s) e ao ouvinte de escolha. Esta nova Diretiva **deve** ser exatamente a mesma da configuração atual do WAF, o que significa que todas as regras personalizadas, exclusão, regra desativada, etc. devem ser copiadas para a nova Política que você está criando. Uma vez que você tenha uma diretiva associada ao gateway de aplicativo, então você pode continuar a fazer alterações nas regras e configurações do WAF. Você também pode fazer isso com o Azure PowerShell. Para obter mais informações, consulte [Associar uma política WAF com um Gateway de aplicativo existente](associate-waf-policy-existing-gateway.md).

Opcionalmente, você pode usar um script de migração para migrar para uma política WAF. Para obter mais informações, consulte [as políticas do Migrate Web Application Firewall usando o Azure PowerShell](migrate-policy.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [grupos e regras de regras do CrS do Firewall da Web Application](application-gateway-crs-rulegroups-rules.md).
