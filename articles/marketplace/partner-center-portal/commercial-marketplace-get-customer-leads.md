---
title: Gerenciamento de vendas potenciais do marketplace comercial da Microsoft
description: Saiba mais sobre como gerar e receber vendas potenciais de clientes do seu Microsoft AppSource e ofertas do Azure Marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 89b73fd98ca773668d2eb53892d0c21397e9abf3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559578"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>Vendas potenciais do cliente da sua oferta do marketplace comercial

AS vendas potenciais são clientes interessados ou que estão implantando suas ofertas do [Microsoft AppSource](https://appsource.microsoft.com) e do [Azure Marketplace](https://azuremarketplace.microsoft.com). Você poderá receber vendas potenciais do cliente depois que sua oferta for publicada no marketplace comercial. Este artigo explica os seguintes conceitos de gerenciamento de vendas potenciais:

* Como sua oferta do marketplace comercial gera vendas potenciais do cliente para garantir que você não perca as oportunidades de negócios. 
* Como conectar seu sistema de CRM (gerenciamento de relacionamento com o cliente) à sua oferta para que você possa gerenciar suas vendas potenciais em um local central.
* Os dados de vendas potenciais que enviamos para você para que você possa acompanhar os clientes que entraram em contato com você.

## <a name="generate-customer-leads"></a>Gerar vendas potenciais do cliente

Aqui estão os locais em que uma venda potencial é gerada:

- Um cliente consente em compartilhar informações depois de selecionar **Entrar em contato comigo** no marketplace comercial. Essa é uma venda potencial de *interesse inicial*. Compartilhamos informações com você sobre o cliente que expressou interesse em obter seu produto. O cliente potencial está no topo do funil de aquisição.

    ![Entre em contato comigo do Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Um cliente seleciona **Obtê-lo agora** (ou seleciona **Criar** no [portal do Azure](https://portal.azure.com/)) para obter sua oferta. Essa venda potencial é um *ativa*. Compartilhamos informações com você sobre o cliente que começou a implantar seu produto.

    ![Botão Obter agora do SQL](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Botão Criar do Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Um cliente seleciona **Test Drive** ou **Avaliação Gratuita** para experimentar sua oferta. As unidades de teste ou as avaliações gratuitas são oportunidades aceleradas para você compartilhar seus negócios instantaneamente com clientes potenciais sem nenhuma barreira de entrada.

    ![Botão Test Drive do Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Botão Avaliação Gratuita do Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Conectar-se ao seu sistema CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Entender os dados da venda potencial

Cada cliente potencial que você recebe durante o processo de aquisição de cliente tem dados em campos específicos. O primeiro campo a ser examinado é `LeadSource`, que segue este formato: **Ação-de-Origem** | **Oferta**.

**Origens**: O valor desse campo é preenchido com base no marketplace que gerou a venda potencial. Os valores possíveis são `"AzureMarketplace"`, `"AzurePortal"` e `"AppSource (SPZA)"`.

**Ações**: O valor desse campo é preenchido com base na ação que o cliente realizou no marketplace que gerou a venda potencial.

Os valores possíveis são:

- **"INS"** : significa *instalação*. Essa ação ocorre no Azure Marketplace ou no AppSource quando um cliente adquire seu produto.
- **"PLT"** : significa *avaliação conduzida pelo parceiro*. Essa ação ocorre no AppSource quando um cliente usa a opção **Entrar em contato comigo**.
- **"DNC"** : significa *não entrar em contato*. Essa ação ocorre no AppSource quando um parceiro que foi listado de modo cruzado na página do aplicativo solicitado ser contatado. Compartilhamos uma notificação de que esse cliente foi listado de modo cruzado em seu aplicativo, mas ele não precisa ser contatado.
- **"Create"** : essa ação ocorre apenas dentro do portal do Azure e é gerada quando um cliente compra sua oferta na conta dele.
- **"StartTestDrive"** : essa ação é apenas para a opção **Test Drive** e é gerada quando um cliente inicia o test drive.

**ofertas**: Você pode ter várias ofertas no marketplace comercial. O valor desse campo é preenchido com base na oferta que gerou a venda potencial. A ID do editor e a ID da oferta são enviadas neste campo e são os valores que você forneceu quando publicou a oferta no marketplace.

Os exemplos a seguir mostram valores no formato esperado `publisherid.offerid`: 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Informações do cliente

As informações do cliente são enviadas usando vários campos. O exemplo a seguir mostram as informações do cliente que estão contidas em um cliente potencial:

- Nome: John
- Sobrenome: Smith
- Email: crlima\@microsoft.com
- Telefone: 1234567890
- País: EUA
- Empresa: Microsoft
- Título: CTO

>[!NOTE]
>Nem todos os dados no exemplo anterior estão sempre disponíveis para cada cliente potencial. Como você obterá clientes potenciais de várias etapas, conforme mencionado na seção "Gerar vendas potenciais do cliente", a melhor maneira de lidar com vendas potenciais é eliminar a duplicação dos registros e personalizar os acompanhamentos. Dessa forma, cada cliente recebe uma mensagem apropriada e você cria uma relação exclusiva.

## <a name="best-practices-for-lead-management"></a>Melhores práticas para o gerenciamento de vendas potenciais

Aqui estão algumas recomendações para orientar os leads pelo seu ciclo de vendas:

- **Processo**: Defina um processo de vendas claro, com marcos, análise e responsabilidade clara da equipe.
- **Qualificação**: defina os pré-requisitos, que indicam se um cliente potencial foi totalmente qualificado. Os representantes de vendas ou marketing devem qualificar as vendas potenciais cuidadosamente antes de conduzi-las pelo processo de vendas completo.
- **Acompanhamento**: não se esqueça de acompanhar em 24 horas. Você obterá o cliente potencial em seu CRM de escolha imediatamente depois que o cliente implantar um test drive; Envie-os por email enquanto eles ainda estiverem em aquecimento. Solicite o agendamento de uma chamada telefônica para entender melhor se o seu produto é uma boa solução para seu problema. Espere que a transação típica exija várias chamadas de acompanhamento.
- **Incentivo**: incentivá suas vendas potenciais para ajudar você a obter uma margem de lucro mais alta. Fazer check-in, mas não Bombard-los. Recomendamos que você envie um email aos clientes potenciais pelo menos algumas vezes antes de fechá-los; Não desistir após a primeira tentativa. Lembre-se, esses clientes estão diretamente envolvidos em seu produto e gastaram tempo em uma avaliação gratuita; Eles são ótimos clientes potenciais.

## <a name="common-questions-about-lead-management"></a>Perguntas comuns sobre o gerenciamento de vendas potenciais

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Como posso obter ajuda na configuração de meu destino de cliente potencial?

Siga as etapas na seção [Conectar-se ao seu sistema de CRM](#connect-to-your-crm-system) ou envie um tíquete de suporte por meio de [Ajuda e suporte do Partner Center](https://aka.ms/marketplacepublishersupport). Em seguida, selecione **Criação de oferta** > **Seu tipo de oferta** > **Configuração de gerenciamento de vendas potenciais**.

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>É necessário configurar um destino de venda potencial para publicar uma oferta no marketplace comercial?

A resposta depende do tipo de oferta que você está publicando. SaaS (software como serviço) e o Dynamics 365 Customer Engagement usam **Entre em contato comigo** para listar todas as ofertas do Dynamics 365 para finanças e operações, todas as ofertas do Dynamics 365 Business Central e todas as ofertas de Serviços de Consultoria. Assim, eles exigem uma conexão com um destino de venda potencial. Se o tipo de oferta não estiver listado, uma conexão com um destino de venda potencial não será necessária. Recomendamos configurar um destino de venda potencial para não perder as oportunidades de negócios.

### <a name="how-can-i-find-the-test-lead"></a>Como encontrar a venda potencial de teste?

Pesquise `"MSFT_TEST"` em seu destino de venda potencial. Aqui está um exemplo de venda potencial de teste da Microsoft:

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

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Eu tenho uma oferta online, por que não estou vendo as vendas potenciais?

Verifique se a conexão com o destino da venda potencial é válida. Enviaremos a você uma venda potencial de teste depois de selecionar **Publicar** na oferta no Partner Center. Se você vir a venda potencial de teste, a conexão será válida. Você também pode testar sua conexão de venda potencial tentando adquirir a versão prévia da oferta durante a etapa de versão prévia. Selecione **Obter agora**, **Entre em contato comigo** ou **Avaliação gratuita** na listagem no marketplace comercial.

Além disso, verifique se você está procurando os dados corretos. O conteúdo na seção [Entender os dados da venda potencial](#understand-lead-data) deste artigo descreve os dados de venda potencial que enviamos para o destino da venda potencial.

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Configurei o armazenamento de Blobs do Azure como meu destino de venda potencial. Por que não vejo a venda potencial?

O armazenamento de Blobs do Azure não tem mais suporte como um destino de venda potencial, portanto, você não tem nenhuma venda potencial do cliente gerada pela sua oferta. Alterne para qualquer uma das outras [opções de destino de venda potencial](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Recebi um email do marketplace comercial. Por que não consigo encontrar a venda potencial no meu CRM?

É possível que o domínio de email do usuário final é de. edu. Por motivos de privacidade, não enviamos informações pessoais do domínio .edu. Envie um tíquete de suporte por meio da [Ajuda e suporte do Partner Center](https://aka.ms/marketplacepublishersupport).

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Configurei uma tabela do Azure como meu destino de venda potencial. Como posso exibir as vendas potenciais?

Você pode acessar os dados da venda potencial armazenados na tabela do Azure por meio do portal do Azure. Você também pode baixar e instalar o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) gratuitamente para exibir os dados da tabela da sua conta de armazenamento do Azure.

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Configurei uma tabela do Azure como meu destino de venda potencial. Posso ser notificado sempre que uma nova venda potencial do marketplace comercial for enviada?

Sim. Siga as instruções em [Configurar o gerenciamento de venda potencial usando uma tabela do Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) para configurar um Microsoft Flow que envia um email se uma venda potencial é adicionada à tabela do Azure.

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Configurei o Salesforce como destino como meu cliente potencial. Por que não consigo localizar as vendas potenciais?

Verifique se o formulário Web-to-lead é um campo obrigatório com base em uma lista de seleção. Se for, mude o campo para um campo de texto não obrigatório.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Houve um problema com meu destino de venda potencial e eu perdi algumas vendas potenciais. Elas podem ser enviadas por email para mim?

Devido a políticas de informações pessoais, não podemos compartilhar informações de venda potencial por meio de emails não seguros.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Configurei uma tabela do Azure como meu destino de venda potencial. Quanto isso custará?

Os dados de geração de venda potencial estão baixos. São inferiores a 1 GB para quase todos os editores. O custo depende do número de vendas potenciais recebidas. Por exemplo, se 1.000 vendas potenciais forem recebidas em um mês, o custo será de cerca de 50 centavos. Para obter mais informações sobre preços de armazenamento, confira [Visão geral de preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

Se sua pergunta não for respondida, entre em contato com o Suporte da Microsoft por meio da [Ajuda e suporte do Partner Center](https://aka.ms/marketplacepublishersupport). Em seguida, selecione **Criação de oferta** > **Seu tipo de oferta** > **Configuração de gerenciamento de vendas potenciais**.

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Estou recebendo notificações por email quando novas vendas potenciais do cliente são recebidas. Como posso configurar outra pessoa para receber esses emails?

Acesse sua oferta no Partner Center e vá para a página de **Configuração da oferta** > **Gerenciamento de venda potencial** > **Editar**. Atualize os endereços de email no campo **Email de contato**.

## <a name="next-steps"></a>Próximas etapas

Depois que a instalação técnica estiver em vigor, incorpore essas vendas potenciais à estratégia atual de vendas e marketing e aos processos operacionais. Estamos interessados em entender melhor seu processo geral de vendas e queremos trabalhar estreitamente com você para fornecer vendas potenciais de alta qualidade e dados suficientes para o seu sucesso. Apreciamos seus comentários sobre como otimizar e melhorar os leads que enviamos a você, com dados adicionais para ajudar a tornar esses clientes bem-sucedidos. Informe-nos se estiver interessado em [enviar comentários](mailto:AzureMarketOnboard@microsoft.com) e sugestões para que sua equipe de vendas tenha mais sucesso com vendas potenciais do marketplace comercial.
