---
title: Monitoramento e alerta do Azure Key Vault | Microsoft Docs
description: Crie um painel para monitorar a saúde do cofre da chave e configure alertas.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: cc0d969ff6eb76732768dfed2826762920ae9e62
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726937"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Monitoramento e alerta para o Azure Key Vault

## <a name="overview"></a>Visão geral

Uma vez que você começou a usar o cofre de chaves para armazenar seus segredos de produção, é importante monitorar a saúde do seu cofre de chaves para garantir que seu serviço funcione como planejado. À medida que você começa a dimensionar seu serviço, o número de solicitações enviadas ao seu cofre de chaves aumentará. Isso tem potencial para aumentar a latência de seus pedidos e, em casos extremos, fazer com que suas solicitações sejam estranguladas, o que afetará o desempenho do seu serviço. Você também precisa ser alertado se o cofre principal está enviando um número incomum de códigos de erro, para que você possa ser rapidamente notificado de quaisquer problemas de política de acesso ou configuração de firewall. Este documento abrangerá os seguintes tópicos:

+ Métricas básicas do Key Vault para monitorar
+ Como configurar métricas e criar um painel 
+ Como criar alertas em limiares especificados 

## <a name="basic-key-vault-metrics-to-monitor"></a>Métricas básicas do Key Vault para monitorar

+ Disponibilidade do cofre  
+ Saturação do Cofre 
+ Latência da API de serviço 
+ Total de api de serviço hits (filtro por tipo de atividade) 
+ Códigos de erro (Filtro por Código de Status) 

**Disponibilidade do cofre** - Esta métrica deve estar sempre em 100% esta é uma métrica importante para monitorar, uma vez que pode mostrar rapidamente se o seu cofre principal sofreu uma paralisação. 

**Saturação do cofre** – O número de solicitações por segundo que um cofre de chaves pode servir é baseado no tipo de operação que está sendo realizada. Algumas operações de cofre têm um limite de pedidos por segundo menor. Essa métrica agrega o uso total do cofre de chaves em todos os tipos de operação para chegar a um valor percentual que indique o uso atual do cofre de chaves. Para obter uma lista completa dos limites de serviço do cofre-chave, consulte o documento a seguir. [Limites de serviços do Azure Key Vault](service-limits.md)

**Latência da API de serviço** - Esta métrica mostra a latência média de uma chamada para o cofre-chave. Embora seu cofre principal possa estar dentro dos limites de serviço, uma alta utilização do cofre de chaves pode introduzir latência que faz com que os aplicativos a jusante falhem. 

**Total de API Hits** - Esta métrica mostra todas as chamadas feitas para o seu cofre principal. Isso ajudará você a identificar quais aplicativos estão chamando seu cofre principal. 

**Códigos de** erro – Esta métrica mostrará se o cofre da chave está experimentando uma quantidade incomum de erros. Para obter uma lista completa de códigos de erro e orientação para solução de problemas, consulte o documento a seguir. [Códigos de erro da API do Cofre da Chave Azure](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Como configurar métricas e criar um painel

1. Faça logon no portal do Azure
2. Navegue até o seu cofre de chaves
3. Selecione **Métricas** em **Monitoramento** 

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-1.png)

4. Atualize o título do gráfico para o que você deseja ver em seu painel. 
5. Selecione o escopo. Neste exemplo, selecionaremos um único cofre de chaves. 
6. Selecione o Metric **Overall Vault Availability** and Aggregation **Avg** 
7. Atualize o intervalo de tempo para as últimas 24 Horas e atualize o tempo granularpara 1 minuto. 

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-2.png)

8. Repita as etapas acima para as métricas de Latência de API de Saturação e Serviço de Saturação do Cofre. Selecione **Pin to Dashboard** para salvar suas métricas em um painel. 

> [!IMPORTANT]
> Selecione "Pin to Dashboard" e salve todas as métricas configuradas. Se você deixar a página e retornar a ela sem salvar, suas alterações de configuração serão perdidas. 

9. Para monitorar todos os tipos de operações no cofre principal, use a Métrica de hits de **aPI de serviço total** e selecione **Aplicar divisão por tipo de atividade**

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-3.png)

10. Para monitorar códigos de erro no cofre de chaves, use a métrica de resultados da **API total e** selecione **Aplicar divisão por tipo de atividade**

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-4.png)

Agora você terá um painel que se parece com isso. Você pode clicar nos 3 terros no canto superior direito de cada ladrilho e pode reorganizar e redimensionar as telhas conforme necessário. 

Depois de salvar e publicar o painel, ele criará um novo recurso na sua assinatura do Azure. Você poderá vê-lo a qualquer momento, procurando por "painel compartilhado". 

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Como configurar alertas no seu Key Vault 

Esta seção mostrará como configurar alertas no cofre principal para que você possa alertar sua equipe a agir imediatamente se o seu cofre principal estiver em um estado insalubre. Você pode configurar alertas que enviam um e-mail, de preferência para uma equipe DL, disparar uma notificação de grade de evento ou ligar ou enviar um número de telefone. Você também pode escolher alertas estáticos com base em um valor fixo ou um alerta dinâmico que irá alertá-lo se uma métrica monitorada exceder o limite médio do cofre da chave um determinado número de vezes dentro de um intervalo de tempo definido. 

> [!IMPORTANT]
> Observe que pode levar até 10 minutos para que os alertas recém-configurados comecem a enviar notificações. 

### <a name="configure-an-action-group"></a>Configure um grupo de ação 

Um grupo de ação é uma lista configurável de notificações e propriedades.

1. Faça logon no portal do Azure
2. Procure **alertas** na caixa de pesquisa
3. Selecione **Gerenciar ações**

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-6.png)

4. Selecione **+ Adicione grupo de ação**

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-7.png)

5. Escolha o **tipo de ação** para o seu grupo de ação. Neste exemplo, criaremos um alerta de e-mail.

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-9.png)

6. Clique em **OK** na parte inferior da página. Você criou com sucesso um grupo de ação. 

Agora que você configurou um grupo de ação, configuraremos os limiares de alerta do cofre de chave. 

### <a name="configure-alert-thresholds"></a>Configure limiares de alerta 

1. Selecione seu recurso de cofre chave no portal Azure e selecione **Alertas** em **Monitoramento**

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-10.png)

2. Selecione **nova regra de alerta**

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-11.png)

3. Selecione o escopo da regra de alerta. Você pode selecionar um único cofre ou múltiplo. 

> [!IMPORTANT]
> Observe que quando você estiver selecionando vários cofres para o escopo de seus alertas, todos os cofres selecionados devem estar na mesma região. Você terá que configurar regras de alerta separadas para cofres em diferentes regiões. 

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-12.png)

4. Selecione as condições para seus alertas. Você pode escolher qualquer um dos seguintes sinais e definir sua lógica para alertar. A equipe do Key Vault recomenda configurar os seguintes limiares de alerta. 

    + Disponibilidade do cofre de chaves cai abaixo de 100% (Limite Estático)
    + A latência do cofre-chave é superior a 500ms (Limiar Estático) 
    + A saturação geral do cofre é superior a 75% (Limiar Estático) 
    + A saturação geral do cofre excede a média (Limite Dinâmico)
    + Códigos de erro totais acima da média (Limite Dinâmico) 

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Exemplo 1: Configuração de um limiar de alerta estático para latência

Selecione **Latência geral da API de serviço** como o nome do sinal
> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-14.png)

Consulte os seguintes parâmetros de configuração.

+ Defina o limiar para **estático** 
+ Defina o Operador como **maior do que**
+ Defina o tipo de agregação como **médio**
+ Defina o valor limite para **500**
+ Definir período de agregação para **5 minutos**
+ Defina a freqüência de avaliação para **1 minuto**
+ Selecione **Concluído**  

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Exemplo 2: Configuração de um limiar de alerta dinâmico para saturação do cofre 

Quando você usa um alerta dinâmico, você poderá ver dados históricos do cofre-chave selecionado. A área azul representa o uso médio do seu cofre principal. A área vermelha mostra picos que teriam acionado um alerta desde que outros critérios na configuração do alerta sejam atendidos. Os pontos vermelhos mostram casos de violações onde os critérios para o alerta foram atendidos durante a janela de tempo agregada. Você pode definir um alerta para disparar após um certo número de violações dentro de um tempo definido. Se você não quiser incluir dados passados, há uma opção para excluir dados antigos abaixo em configurações avançadas. 

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-16.png)

Consulte os seguintes parâmetros de configuração.

+ Defina o limiar para **dinâmico** 
+ Defina o Operador como **maior do que**
+ Defina o tipo de agregação como **médio**
+ Defina a sensibilidade do limiar como **média**
+ Definir período de agregação para **5 minutos**
+ Defina a freqüência de avaliação para **1 minuto**
+ **Opcional** Configurar configurações avançadas 
+ Selecione **Concluído**

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-17.png)

5. Adicione o grupo de ação que você configurou

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-18.png)

6. Habilite o alerta e atribua uma gravidade

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-19.png)

7. Crie o alerta 


## <a name="next-steps"></a>Próximas etapas

Parabéns, você criou com sucesso um painel de monitoramento e configurou alertas para o seu cofre de chaves! Depois de seguir todas as etapas acima, você deve receber alertas de e-mail quando seu cofre principal atender aos critérios de alerta que você configurou. Um exemplo é mostrado abaixo. Use as ferramentas que você criou neste artigo para monitorar ativamente a saúde do seu cofre principal. 

### <a name="example-email-alert"></a>Exemplo de alerta de e-mail 

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure](../media/alert-20.png)
