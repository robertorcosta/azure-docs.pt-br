---
title: Configurar leads de cliente | Mercado Azure
description: Configure os leads do cliente no mercado comercial.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: a1ec89dfd2dda91a10f2cc00b6ca4d9d7abbf032
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731150"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Clientes potenciais de sua oferta do marketplace

Os leads são clientes interessados ou implantando suas ofertas no [Azure Marketplace](https://azuremarketplace.microsoft.com) ou no [AppSource](https://appsource.microsoft.com). Você receberá leads de clientes assim que sua oferta for publicada no mercado. Este artigo explicará:

* A oferta do seu marketplace gera leads de clientes, garantindo que você não perca oportunidades de negócios. 
* Conecte seu CRM à sua oferta, para que você possa gerenciar seus leads em um único local central.
* Entenda os dados de liderança que enviamos, para que você possa acompanhar os clientes que entraram em contato com você.

## <a name="generate-customer-leads"></a>Gerar leads de clientes

Aqui estão os lugares onde um lead é gerado:

1. Quando um cliente concorda em compartilhar suas informações depois de selecionar "Entre em contato comigo" do mercado. Este lead é um **lead de interesse inicial,** onde compartilhamos informações sobre o cliente que manifestou interesse em obter seu produto. O cliente potencial está no topo do funil de aquisição.

      ![Dinâmica 365 Entre em contato comigo](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. Quando um cliente seleciona "Get it Now" ou "Create" (no [portal Azure)](https://portal.azure.com/)para obter sua oferta, este lead é um **lead ativo,** onde compartilhamos informações sobre um cliente que começou a implantar seu produto.

    ![SQL Obtê-lo agora](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Criação do Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. Um cliente faz um "Test Drive" ou inicia um "Teste Gratuito" da sua oferta. Test Drives ou testes gratuitos são oportunidades aceleradas para você compartilhar seus negócios instantaneamente com potenciais clientes sem quaisquer barreiras de entrada.

    ![Unidade de teste Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Unidade de teste Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Conecte-se ao seu sistema de CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Entenda os dados de chumbo

Cada cliente potencial que você recebe durante o processo de aquisição de cliente tem dados em campos específicos. O primeiro campo a ficar `LeadSource` de olho é o campo, que segue este formato: **Source-Action** | **Offer**.

**Fontes**: O valor para este campo é preenchido com base no marketplace que gerou o lead. Os valores possíveis são `"AzureMarketplace"`, `"AzurePortal"` e `"AppSource (SPZA)"`.

**Ações**: O valor para este campo é preenchido com base na ação que o cliente tomou no mercado, o que gerou o lead. 

Os valores possíveis são:

- "INS" – instalação. Essa ação ocorre no Azure Marketplace ou no AppSource quando um cliente compra seu produto.
- "PLT" – significa Partner Led Trial (parceiro lidera a avaliação). Essa ação ocorre no AppSource quando um cliente usa a opção Entrar em contato comigo.
- "DNC" – Do Not Contact (Não entrar em contato). Essa ação ocorre no AppSource quando um parceiro que foi listado de forma cruzada na página do aplicativo solicitado ser contatado. Estamos compartilhando a informação de que esse cliente foi listado de forma cruzada em seu aplicativo, mas ele não precisa ser contatado.
- "Criar" - Esta ação só está dentro do portal Azure e é gerada quando um cliente compra sua oferta para sua conta.
- "StartTestDrive" – essa ação ocorre apenas para test drives e é gerada quando um cliente inicia o test drive.

**Ofertas**: Você pode ter várias ofertas no mercado. O valor para este campo é preenchido com base na oferta que gerou o lead. O ID do Publisher e o ID de Oferta são enviados neste campo e são valores fornecidos quando publicou a oferta para o mercado.

Os exemplos a seguir mostram valores de exemplo no formato `publisherid.offerid`esperado: 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Informações do cliente

As informações do cliente são enviadas através de vários campos. O exemplo a seguir mostra as informações do cliente contidas em um lead.

- Nome: Carlos
- Sobrenome: Lima
- Email: crlima\@microsoft.com
- Telefone: 1234567890
- País: EUA
- Empresa: Microsoft
- Cargo: CTO

>[!Note]
>Nem todos os dados no exemplo anterior estão sempre disponíveis para cada cliente potencial. Como você obterá leads de várias etapas como mencionado na seção Leads do cliente, a melhor maneira de lidar com os leads é desduplicar os registros e personalizar os follow-ups. Dessa forma, cada cliente recebe uma mensagem apropriada, e você cria uma relação exclusiva.

## <a name="best-practices-for-lead-management"></a>Melhores práticas para gestão de lead

1. *Processo* - Defina um processo claro de vendas, com marcos, KPIs e posse clara da equipe.
2. *Qualificação* - Definir pré-requisitos, que indiquem se um lead foi totalmente qualificado. Certifique-se de que os representantes de vendas ou marketing qualifiquem os leads cuidadosamente antes de levá-los através do processo de vendas completo.
3. *Acompanhamento* - Não se esqueça de acompanhar, espere que a transação típica exija de 5 a 12 chamadas de acompanhamento
4. *Nutrição* - Nutrir seus leads, a fim de levá-lo no caminho para uma margem de lucro maior.

## <a name="leads-frequently-asked-questions"></a>Lidera perguntas frequentes

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Onde posso obter ajuda na configuração de meu destino de cliente potencial?

Você pode [here](#connect-to-your-crm-system) encontrar a documentação https://partner.microsoft.com/support/v2/?stage=1 aqui ou enviar um bilhete de suporte através de, em seguida, selecionar **'criação de oferta'** → **seu tipo de oferta** → **'configuração de gerenciamento de chumbo.'**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Sou obrigado a configurar um destino líder para publicar uma oferta no mercado?

A resposta depende do tipo de oferta que você está publicando. A SaaS e a Dynamics 365 para engajamento do cliente oferecem a lista como 'Contact Me', todas as ofertas da Dynamics 365 for Operations, todas as ofertas da Dynamics 365 Business Central e todas as ofertas do Serviço de Consultoria requerem uma conexão com um destino líder. Se o seu tipo de oferta não foi listado, então não é necessário. No entanto, é recomendável configurar um destino líder para que você não perca oportunidades de negócios.

### <a name="how-can-i-find-the-test-lead"></a>Como encontrar o cliente potencial de teste?

Procure `"MSFT_TEST"` no seu destino principal, aqui está um teste de exemplo da Microsoft:

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

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Tenho uma oferta ao vivo, mas não vejo pistas?

Certifique-se de que sua conexão com o destino principal é válida. Enviaremos uma pista de teste depois que você publicar sua oferta no Partner Center. Se você ver o chumbo de teste, então a conexão é válida. Você também pode testar sua conexão de chumbo tentando adquirir a visualização da oferta durante a etapa de pré-visualização clicando em "obtê-lo agora", "entra em contato comigo" ou "avaliação gratuita" na listagem no mercado.

Além disso, certifique-se de que você está procurando os dados certos. O conteúdo na seção [Entender dados de chumbo](#understand-lead-data) deste documento descreve os dados de chumbo que enviamos para o seu destino principal.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Configurei o BLOB do Azure como meu cliente potencial de destino, por que não vejo o cliente potencial?

O destino líder do Azure Blob não é mais suportado, então você está perdendo quaisquer leads de clientegerados pela sua oferta. Mude para qualquer uma das outras [opções de destino principal](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Recebi um email do Marketplace, por que não é possível localizar o cliente potencial no meu CRM?

É possível que o domínio de email do usuário final é de. edu. Por razões de privacidade, não passamos informações identificáveis privadas do domínio .edu. Envie um bilhete https://partner.microsoft.com/support/v2/?stage=1de suporte através de .

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Configurei a Tabela Azure como meu destino principal, como posso ver os leads?

Você pode acessar os dados de chumbo armazenados na Tabela Azure a partir do portal Azure, ou você pode baixar e instalar o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gratuitamente para visualizar os dados das tabelas da sua conta de armazenamento Azure.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Configurei a tabela do Azure como destino de cliente potencial, posso ser notificado sempre que um novo cliente potencial é enviado pelo Marketplace?

Sim, siga as instruções para configurar um fluxo microsoft que envia um e-mail se um lead for adicionado à Tabela Azure na documentação [aqui](./commercial-marketplace-lead-management-instructions-azure-table.md).

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Configurei o Salesforce como destino como meu cliente potencial, por que não é possível localizar os clientes potenciais?

Verifique se o formulário "web to lead" é um campo obrigatório com base em uma lista de seleção. Em caso afirmativo, alterne o campo para um campo de texto não obrigatório.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Houve um problema com meu destino de cliente potencial e eu me perdi de alguns clientes potenciais. É possível tê-los enviados para mim por email?

Devido a políticas de informações identificáveis privadas, não podemos compartilhar informações de chumbo por e-mail não seguro.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Configurei o Azure Table como meu destino principal, quanto custará?

Dados de geração de cliente potencial (<1 GB para quase todos os publicadores). O custo dependerá do número de clientes potenciais recebido, se 1.000 potenciais são recebidos em um mês, isso custará cerca de 50 centavos. Para obter mais informações sobre preços de armazenamento, veja [preço de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

Se sua pergunta ainda não for respondida, entre em contato com o suporte através de aka.ms/marketplacepublishersupport e selecione **'criação de oferta'** → **seu tipo de oferta** → **'configuração de gerenciamento de chumbo'.** 

### <a name="i-am-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-who-to-receive-these-emails"></a>Estou recebendo notificações de e-mail quando novos leads de clientes são recebidos. Como posso configurar quem receber esses e-mails?

Acesse sua oferta no Partner Center e navegue até a página **de configuração de oferta** -> Lead **Management** -> **Edit**. Atualize os endereços de e-mail no campo **de e-mail contato.**

## <a name="next-steps"></a>Próximas etapas

Uma vez que a configuração técnica esteja em vigor, você deve incorporar esses leads em sua estratégia de marketing e processos operacionais de vendas atuais &. Estamos interessados em entender melhor seu processo geral de vendas e queremos trabalhar estreitamente com você para fornecer leads de alta qualidade e dados suficientes para o seu sucesso. Apreciamos seus comentários sobre como otimizar e melhorar os leads que enviamos a você, com dados adicionais para ajudar a tornar esses clientes bem-sucedidos. Deixe-nos saber se você está interessado em [fornecer feedback](mailto:AzureMarketOnboard@microsoft.com) e sugestões para permitir que sua equipe de vendas seja mais bem sucedida com o Marketplace Leads.
