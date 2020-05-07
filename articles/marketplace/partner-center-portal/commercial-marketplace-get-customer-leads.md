---
title: Gerenciamento de leads do Marketplace comercial da Microsoft
description: Saiba mais sobre como gerar e receber leads de clientes do seu Microsoft AppSource e ofertas do Azure Marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 5d1bf859968c7b7a889abe635c917d0da11bf6e1
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837321"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>Clientes potenciais de sua oferta de Marketplace comercial

Os clientes potenciais estão interessados ou implantando suas ofertas do [Microsoft AppSource](https://appsource.microsoft.com) e [do Azure Marketplace](https://azuremarketplace.microsoft.com). Você pode receber leads do cliente depois que sua oferta for publicada no Marketplace comercial. Este artigo explica os seguintes conceitos de gerenciamento de leads:

* Como sua oferta do Marketplace comercial gera clientes potenciais para garantir que você não perca as oportunidades de negócios. 
* Como conectar seu sistema CRM (gerenciamento de relacionamento com o cliente) à sua oferta para que você possa gerenciar seus leads em um local central.
* Os dados de Lead que enviamos para você para que você possa acompanhar os clientes que chegaram a você.

## <a name="generate-customer-leads"></a>Gerar leads do cliente

Aqui estão os locais onde um cliente potencial é gerado:

- Um cliente consente em compartilhar suas informações depois de selecionar **entre em contato comigo** no Marketplace comercial. Esse Lead é um Lead de *interesse inicial* . Compartilhamos informações com você sobre o cliente que expressou interesse em obter seu produto. O cliente potencial está no topo do funil de aquisição.

    ![Dynamics 365 contate-me](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Um cliente seleciona **obtê-lo agora** (ou seleciona **criar** no [portal do Azure](https://portal.azure.com/)) para obter sua oferta. Esse Lead é um Lead *ativo* . Compartilhamos informações com você sobre o cliente que começou a implantar seu produto.

    ![Botão obter agora do SQL](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Botão criar do Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Um cliente seleciona a **unidade de teste** ou a **avaliação gratuita** para experimentar sua oferta. As unidades de teste ou as avaliações gratuitas são oportunidades aceleradas para você compartilhar seus negócios instantaneamente com clientes potenciais, sem qualquer barreiras de entrada.

    ![Botão testar unidade do Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Botão de avaliação gratuita do Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Conectar-se ao sistema CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Entender os dados do cliente potencial

Cada cliente potencial que você recebe durante o processo de aquisição de cliente tem dados em campos específicos. O primeiro campo a ser examinado é o `LeadSource` campo, que segue este formato: **a oferta de ação** | **Offer**de origem.

**Fontes**: o valor desse campo é preenchido com base no Marketplace que gerou o cliente potencial. Os valores possíveis são `"AzureMarketplace"`, `"AzurePortal"` e `"AppSource (SPZA)"`.

**Ações**: o valor desse campo é preenchido com base na ação que o cliente realizou no Marketplace que gerou o cliente potencial.

Os valores possíveis são:

- **"Ins"**: significa *instalação*. Esta ação está no Azure Marketplace ou AppSource quando um cliente adquire seu produto.
- **"PLT"**: significa avaliação orientada por *parceiro*. Essa ação está em AppSource quando um cliente seleciona a opção **entre em contato comigo** .
- **"DNC"**: significa *não contatar*. Essa ação está em AppSource quando um parceiro que foi listado em sua página de aplicativo é solicitado a ser contatado. Compartilhamos uma notificação informando que esse cliente foi listado em seu aplicativo, mas eles não precisam ser contatados.
- **"Criar"**: essa ação está apenas dentro do portal do Azure e é gerada quando um cliente compra sua oferta para sua conta.
- **"StartTestDrive"**: essa ação é apenas para a opção **Test Drive** e é gerada quando um cliente inicia sua Test Drive.

**Ofertas**: você pode ter várias ofertas no Marketplace comercial. O valor desse campo é preenchido com base na oferta que gerou o cliente potencial. A ID do Publicador e a ID da oferta são enviadas neste campo e são os valores que você forneceu quando publicou a oferta no Marketplace.

Os exemplos a seguir mostram valores no formato `publisherid.offerid`esperado: 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Informações do cliente

As informações do cliente são enviadas por vários campos. O exemplo a seguir mostra as informações do cliente contidas em um cliente potencial:

- Nome: Carlos
- Sobrenome: Lima
- Email: crlima\@microsoft.com
- Telefone: 1234567890
- País: EUA
- Empresa: Microsoft
- Cargo: CTO

>[!NOTE]
>Nem todos os dados no exemplo anterior estão sempre disponíveis para cada cliente potencial. Como você obterá clientes potenciais de várias etapas, conforme mencionado na seção "gerar leads do cliente", a melhor maneira de lidar com os Leads é eliminar a duplicação dos registros e personalizar os acompanhamentos. Dessa forma, cada cliente recebe uma mensagem apropriada e você cria uma relação exclusiva.

## <a name="best-practices-for-lead-management"></a>Práticas recomendadas para o gerenciamento de leads

- **Processo**: defina um processo de vendas claros, com etapas, análise e Propriedade clara da equipe.
- **Qualificação**: defina os pré-requisitos, que indicam se um cliente potencial foi totalmente qualificado. Certifique-se de que os representantes de vendas ou marketing qualifiquem os leads cuidadosamente antes de conduzi-los pelo processo de vendas completo.
- **Acompanhamento**: não se esqueça de acompanhar. Espere que a transação típica exija 5 a 12 chamadas de acompanhamento.
- **Incentivá**: incentivá Your leads para ajudá-lo a obter uma margem de lucro mais alta.

## <a name="common-questions-about-lead-management"></a>Perguntas comuns sobre o gerenciamento de leads

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Onde posso obter ajuda na configuração de meu destino de cliente potencial?

Siga as etapas na seção [conectar-se ao sistema CRM](#connect-to-your-crm-system)ou enviar um tíquete de suporte por meio da [ajuda e suporte do Partner Center](https://partner.microsoft.com/support/v2/?stage=1). Em seguida, selecione **criação** > **de oferta seu tipo de oferta** > **Gerenciamento de Lead configuração**.

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Eu precisei configurar um destino de Lead para publicar uma oferta no Marketplace comercial?

A resposta depende do tipo de oferta que você está publicando. Software como serviço (SaaS) e o compromisso com o cliente do Dynamics 365 use **entre em contato comigo** para listar todas as ofertas do Dynamics 365 para finanças e operações, todas as ofertas do Dynamics 365 Business central e todas as ofertas de serviços de consultoria. Como resultado, eles exigem uma conexão com um destino de cliente potencial. Se o tipo de oferta não estiver listado, uma conexão com um destino de cliente potencial não será necessária. Recomendamos que você configure um destino de cliente potencial para não perder as oportunidades de negócios.

### <a name="how-can-i-find-the-test-lead"></a>Como encontrar o cliente potencial de teste?

Procure `"MSFT_TEST"` em seu destino de Lead. Aqui está um exemplo de líder de teste da Microsoft:

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Tenho uma oferta ao vivo, mas por que não vejo clientes potenciais?

Verifique se a conexão com o destino do cliente potencial é válida. Enviaremos a você um líder de teste depois de selecionar **publicar** em sua oferta no Partner Center. Se você vir o Lead de teste, a conexão será válida. Você também pode testar sua conexão de cliente potencial tentando adquirir a versão prévia da oferta durante a etapa de visualização. Selecione **obter agora**, **entre em contato comigo**ou **avaliação gratuita** na lista no Marketplace comercial.

Além disso, verifique se você está procurando os dados corretos. O conteúdo na seção [entender dados de leads](#understand-lead-data) deste artigo descreve os dados de Lead que enviamos para o destino do cliente potencial.

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Configurei o armazenamento de BLOBs do Azure como meu destino de Lead, mas por que não vejo o cliente potencial?

O armazenamento de BLOBs do Azure não tem mais suporte como um destino de Lead, portanto, você não tem nenhum cliente potencial gerado por sua oferta. Alterne para qualquer uma das outras [Opções de destino de Lead](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Recebi um email do Marketplace comercial, mas por que não posso encontrar o cliente potencial no meu CRM?

É possível que o domínio de email do usuário final é de. edu. Por motivos de privacidade, não passamos informações pessoais do domínio. edu. Envie um tíquete de suporte por meio da [ajuda e suporte do Partner Center](https://partner.microsoft.com/support/v2/?stage=1).

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Configurei uma tabela do Azure como meu destino de Lead. Como posso exibir os leads?

Você pode acessar os dados do cliente potencial armazenados na tabela do Azure por meio do portal do Azure. Você também pode baixar e instalar o [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) gratuitamente para exibir os dados da tabela da sua conta de armazenamento do Azure.

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Configurei uma tabela do Azure como meu destino de Lead. Posso ser notificado sempre que um novo cliente potencial do Marketplace comercial for enviado?

Sim. Siga as instruções em [Configurar gerenciamento de leads usando uma tabela do Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) para configurar um Microsoft Flow que envia um email se um cliente potencial for adicionado à tabela do Azure.

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Configurei o Salesforce como meu destino de Lead, mas por que não consigo encontrar os leads?

Verifique se o formulário da Web para o lead é um campo obrigatório com base em uma lista de seleção. Se for, mude o campo para um campo de texto não obrigatório.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Houve um problema com meu destino de cliente potencial e eu me perdi de alguns clientes potenciais. É possível tê-los enviados para mim por email?

Devido a políticas de informações pessoais, não podemos compartilhar informações de Lead por meio de emails não seguros.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Configurei uma tabela do Azure como meu destino de Lead. Quanto custará?

Os dados de geração de cliente potencial estão baixos. É menor que 1 GB para quase todos os Publicadores. O custo depende do número de clientes potenciais recebidos. Por exemplo, se 1.000 clientes potenciais forem recebidos em um mês, o custo será de cerca de 50 centavos. Para obter mais informações sobre preços de armazenamento, consulte [visão geral do armazenamento do Azure preço](https://azure.microsoft.com/pricing/details/storage/).

Se sua pergunta não for respondida, entre em contato Suporte da Microsoft por meio da [ajuda e suporte do Partner Center](https://aka.ms/marketplacepublishersupport). Em seguida, selecione **criação** > **de oferta seu tipo de oferta** > **Gerenciamento de Lead configuração**.

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Estou recebendo notificações por email quando novos leads do cliente são recebidos. Como posso configurar outra pessoa para receber esses emails?

Acesse sua oferta no Partner Center e vá para a página de **instalação da oferta** > **Gerenciamento** > de Lead**Edit**. Atualize os endereços de email no campo **email de contato** .

## <a name="next-steps"></a>Próximas etapas

Depois que a instalação técnica estiver em vigor, incorpore esses leads em sua estratégia de marketing e vendas atual e nos processos operacionais. Estamos interessados em entender melhor seu processo de vendas geral e quero trabalhar em conjunto com você para fornecer leads de alta qualidade e dados suficientes para que você tenha êxito. Apreciamos seus comentários sobre como otimizar e melhorar os leads que enviamos a você, com dados adicionais para ajudar a tornar esses clientes bem-sucedidos. Informe-nos se você estiver interessado em [fornecer comentários](mailto:AzureMarketOnboard@microsoft.com) e sugestões para permitir que sua equipe de vendas seja mais bem-sucedida com clientes potenciais do Marketplace comercial.
