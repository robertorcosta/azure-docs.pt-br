---
title: Proteger a comunicação do serviço baseado no WCF
description: Saiba como proteger as comunicações baseadas em WCF quanto a serviço confiáveis que estão sendo executados no cluster do Azure Service Fabric.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ca5eafa4612503a13f80b7f238e4827979c0358b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75614155"
---
# <a name="secure-wcf-based-communications-for-a-service"></a>Proteger a comunicação baseada em WCF para um serviço
A segurança é um dos aspectos mais importantes da comunicação. A estrutura de aplicativo dos Reliable Services fornece algumas pilhas e ferramentas de comunicação predefinidas que você pode usar para aprimorar a segurança. Este artigo fala sobre como melhorar a segurança quando você estiver usando a comunicação remota do serviço.

Usamos um [exemplo](service-fabric-reliable-services-communication-wcf.md) existente que explica como configurar uma pilha de comunicação baseada em WCF para os Reliable Services. Para ajudar a proteger um serviço quando você estiver usando uma pilha de comunicação baseada em WCF, siga estas etapas:

1. Para o serviço, você precisa ajudar a proteger o ouvinte da comunicação WCF (`WcfCommunicationListener`) que você criar. Para fazer isso, modifique o método `CreateServiceReplicaListeners` .

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
2. No cliente, a classe `WcfCommunicationClient` que foi criada no [exemplo](service-fabric-reliable-services-communication-wcf.md) anterior permanece inalterada. Mas você precisa substituir o método `CreateClientAsync` de `WcfCommunicationClientFactory`:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Use `SecureWcfCommunicationClientFactory` para criar um cliente de comunicação WCF (`WcfCommunicationClient`). Use o cliente para invocar métodos de serviço.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

Como próxima etapa, leia [API Web com OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md).
