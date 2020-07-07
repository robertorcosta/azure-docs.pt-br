---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 66d3397fae24ee2546dae4eb5d7c9d188f9ede99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944155"
---
> [!NOTE]
> Você pode usar o DNS do Azure para configurar um nome DNS personalizado para o Serviço de Aplicativo do Azure. Para obter mais informações, consulte [Usar o DNS do Azure para fornecer as configurações de domínio personalizadas para um serviço do Azure](../articles/dns/dns-custom-domain.md#app-service-web-apps).
>
>

Entre no site de seu provedor de domínio.

Localize a página para gerenciamento de registros DNS. Cada provedor de domínio tem sua própria interface de registros DNS; portanto, consulte a documentação do provedor. Procure áreas do site rotuladas como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**. 

Normalmente, você pode encontrar a página de registros DNS exibindo as informações da conta e procurando um link como **Meus domínios**. Vá para essa página e, em seguida, procure um link com um nome semelhante a **Arquivo de zona**, **Registros DNS** ou **Configuração avançada**.

A captura de tela a seguir é um exemplo de uma página de registros DNS:

![Exemplo de página de registros DNS](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

Na captura de tela de exemplo, selecione **Adicionar** para criar um registro. Alguns provedores têm links diferentes para adicionar tipos de registro diferentes. Novamente, consulte a documentação do provedor.

> [!NOTE]
> Para alguns provedores, como GoDaddy, as alterações nos registros DNS só entram em vigor quando você seleciona um link separado **Salvar Alterações**. 
