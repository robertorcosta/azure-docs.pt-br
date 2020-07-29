---
title: Monitoramento e alertas de Azure Key Vault | Microsoft Docs
description: Crie um painel para monitorar a integridade do cofre de chaves e configurar alertas.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81726937"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Monitoramento e alertas para Azure Key Vault

## <a name="overview"></a>Visão geral

Depois de começar a usar o Key Vault para armazenar seus segredos de produção, é importante monitorar a integridade do cofre de chaves para garantir que o serviço funcione conforme o esperado. Conforme você começa a dimensionar seu serviço, o número de solicitações enviadas para o cofre de chaves aumentará. Isso tem um potencial para aumentar a latência de suas solicitações e, em casos extremos, fazer com que suas solicitações sejam limitadas, o que afetará o desempenho do seu serviço. Você também precisará ser alertado se o cofre de chaves estiver enviando um número incomum de códigos de erro, para que você possa ser notificado rapidamente sobre qualquer política de acesso ou problemas de configuração de firewall. Este documento abordará os seguintes tópicos:

+ Métricas de Key Vault básicas para monitorar
+ Como configurar métricas e criar um painel 
+ Como criar alertas em limites especificados 

## <a name="basic-key-vault-metrics-to-monitor"></a>Métricas de Key Vault básicas para monitorar

+ Disponibilidade do cofre  
+ Saturação do cofre 
+ Latência da API de serviço 
+ Total de ocorrências da API de serviço (filtrar por tipo de atividade) 
+ Códigos de erro (filtrar por código de status) 

**Disponibilidade do cofre** -essa métrica deve ser sempre em 100% essa é uma métrica importante a ser monitorada, pois ela poderá mostrar rapidamente se o cofre da chave passou por uma interrupção. 

**Saturação do cofre** – o número de solicitações por segundo que um cofre de chaves pode atender é baseado no tipo de operação que está sendo executada. Algumas operações de cofre têm um limite menor de solicitações por segundo. Essa métrica agrega o uso total de seu cofre de chaves em todos os tipos de operação para surgir com um valor percentual que indica o uso atual do cofre de chaves. Para obter uma lista completa dos limites de serviço do cofre de chaves, consulte o documento a seguir. [Limites de serviços do Azure Key Vault](service-limits.md)

**Latência da API de serviço** – essa métrica mostra a latência média de uma chamada para o cofre de chaves. Embora o cofre de chaves possa estar dentro dos limites de serviço, uma alta utilização do cofre de chaves pode introduzir latência que faz com que os aplicativos falhem. 

**Total de acertos da API** -essa métrica mostra todas as chamadas feitas ao cofre de chaves. Isso ajudará a identificar quais aplicativos estão chamando o cofre de chaves. 

**Códigos de erro** – essa métrica mostrará se o cofre de chaves está apresentando uma quantidade incomum de erros. Para obter uma lista completa de códigos de erro e diretrizes de solução de problemas, consulte o documento a seguir. [Azure Key Vault códigos de erro da API REST](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Como configurar métricas e criar um painel

1. Faça logon no portal do Azure
2. Navegue até o Key Vault
3. Selecionar **métricas** em **monitoramento** 

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-1.png)

4. Atualize o título do gráfico para o que você deseja ver em seu painel. 
5. Selecione o escopo. Neste exemplo, selecionaremos um único cofre de chaves. 
6. Selecione a métrica **disponibilidade geral do cofre** e agregação **média** 
7. Atualize o intervalo de tempo para as últimas 24 horas e atualize a granularidade de tempo para 1 minuto. 

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-2.png)

8. Repita as etapas acima para a saturação do cofre e as métricas de latência da API de serviço. Selecione **fixar no painel** para salvar suas métricas em um painel. 

> [!IMPORTANT]
> Selecione "fixar no painel" e salve todas as métricas que você configurar. Se você sair da página e retornar a ela sem salvar, as alterações de configuração serão perdidas. 

9. Para monitorar todos os tipos de operações no cofre de chaves, use a métrica **total de acertos da API de serviço** e selecione **aplicar divisão por tipo de atividade**

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-3.png)

10. Para monitorar os códigos de erro no cofre de chaves, use a métrica **total de resultados da API de serviço** e selecione **aplicar divisão por tipo de atividade**

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-4.png)

Agora você terá um painel parecido com este. Você pode clicar nos três pontos no canto superior direito de cada bloco e pode reorganizar e redimensionar os blocos conforme necessário. 

Depois de salvar e publicar o painel, ele criará um novo recurso em sua assinatura do Azure. Você poderá vê-lo a qualquer momento pesquisando "painel compartilhado". 

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Como configurar alertas no seu Key Vault 

Esta seção mostrará como configurar alertas em seu cofre de chaves para que você possa alertar sua equipe para tomar uma ação imediatamente se o cofre de chaves estiver em um estado não íntegro. Você pode configurar alertas que enviam um email, preferencialmente para uma DL de equipe, acionam uma notificação de grade de eventos ou chamam ou texto de um número de telefone. Você também pode escolher alertas estáticos com base em um valor fixo ou um alerta dinâmico que irá alertá-lo se uma métrica monitorada exceder o limite médio de seu cofre de chaves um determinado número de vezes dentro de um intervalo de tempo definido. 

> [!IMPORTANT]
> Observe que pode levar até 10 minutos para que alertas recentemente configurados comecem a enviar notificações. 

### <a name="configure-an-action-group"></a>Configurar um grupo de ação 

Um grupo de ações é uma lista configurável de notificações e propriedades.

1. Faça logon no portal do Azure
2. Pesquisar **alertas** na caixa de pesquisa
3. Selecionar **Gerenciar ações**

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-6.png)

4. Selecione **+ Adicionar grupo de ação**

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-7.png)

5. Escolha o **tipo de ação** para o grupo de ações. Neste exemplo, criaremos um alerta de email.

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-9.png)

6. Clique em **OK** na parte inferior da página. Você criou um grupo de ações com êxito. 

Agora que você configurou um grupo de ações, configuraremos os limites de alerta do cofre de chaves. 

### <a name="configure-alert-thresholds"></a>Configurar limites de alerta 

1. Selecione o recurso do cofre de chaves na portal do Azure e selecione **alertas** em **monitoramento**

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-10.png)

2. Selecionar **nova regra de alerta**

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-11.png)

3. Selecione o escopo da sua regra de alerta. Você pode selecionar um único cofre ou vários. 

> [!IMPORTANT]
> Observe que quando você estiver selecionando vários cofres para o escopo de seus alertas, todos os cofres selecionados deverão estar na mesma região. Você precisará configurar regras de alerta separadas para cofres em regiões diferentes. 

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-12.png)

4. Selecione as condições para seus alertas. Você pode escolher qualquer um dos sinais a seguir e definir sua lógica para alertas. A equipe de Key Vault recomenda configurar os seguintes limites de alerta. 

    + Key Vault disponibilidade cai abaixo de 100% (limite estático)
    + A latência de Key Vault é maior que 500 MS (limite estático) 
    + A saturação geral do cofre é maior que 75% (limite estático) 
    + A saturação geral do cofre excede a média (limite dinâmico)
    + Total de códigos de erro maiores que a média (limite dinâmico) 

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Exemplo 1: Configurando um limite de alerta estático para latência

Selecione a **latência geral da API de serviço** como o nome do sinal
> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-14.png)

Consulte os parâmetros de configuração a seguir.

+ Definir o limite como **estático** 
+ Definir o operador como **maior que**
+ Definir o tipo de agregação como **média**
+ Defina o valor do limite como **500**
+ Definir o período de agregação para **5 minutos**
+ Definir a frequência de avaliação como **1 minuto**
+ Selecione **Concluído**  

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Exemplo 2: Configurando um limite de alerta dinâmico para saturação do cofre 

Ao usar um alerta dinâmico, você poderá ver os dados históricos do cofre de chaves que você selecionou. A área azul representa o uso médio do cofre de chaves. A área vermelha mostra os picos que dispararam um alerta, contanto que outros critérios na configuração do alerta sejam atendidos. Os pontos vermelhos mostram instâncias de violações em que os critérios para o alerta foram atendidos durante a janela de tempo agregada. Você pode definir um alerta para ser acionado após um determinado número de violações dentro de um tempo definido. Se você não quiser incluir dados passados, há uma opção para excluir dados antigos abaixo nas configurações avançadas. 

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-16.png)

Consulte os parâmetros de configuração a seguir.

+ Definir o limite como **dinâmico** 
+ Definir o operador como **maior que**
+ Definir o tipo de agregação como **média**
+ Definir a sensibilidade do limite como **médio**
+ Definir o período de agregação para **5 minutos**
+ Definir a frequência de avaliação como **1 minuto**
+ **Opcional** Definir configurações avançadas 
+ Selecione **Concluído**

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-17.png)

5. Adicionar o grupo de ações que você configurou

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-18.png)

6. Habilitar o alerta e atribuir uma severidade

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-19.png)

7. Criar o alerta 


## <a name="next-steps"></a>Próximas etapas

Parabéns, você criou com êxito um painel de monitoramento e configurou alertas para o cofre de chaves! Depois de ter seguido todas as etapas acima, você deverá receber alertas por email quando o cofre de chaves atender aos critérios de alerta configurados. Um exemplo é mostrado abaixo. Use as ferramentas que você configurou neste artigo para monitorar ativamente a integridade do cofre de chaves. 

### <a name="example-email-alert"></a>Alerta de email de exemplo 

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure](../media/alert-20.png)
