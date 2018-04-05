---
title: Instruções passo a passo - Trabalhando com o WCF
description: Este passo a passo aborda como um aplicativo móvel criado com o Xamarin pode consumir um serviço web do WCF usando a classe BasicHttpBinding.
ms.prod: xamarin
ms.assetid: D0E83342-2E79-4D25-BD57-43718F5628C4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/17/2018
ms.openlocfilehash: 1b317c4c82ec736c7f4c8306036e43cf04086a82
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="walkthrough---working-with-wcf"></a>Instruções passo a passo - Trabalhando com o WCF

_Este passo a passo aborda como um aplicativo móvel criado com o Xamarin pode consumir um serviço web do WCF usando a classe BasicHttpBinding._


É um requisito comum para aplicativos móveis ser capaz de se comunicar com sistemas de back-end. Há muitas opções de estruturas de back-end, um dos quais é [Windows Communication Foundation](http://msdn.microsoft.com/en-us/library/ms731082.aspx) (WCF). Este passo a passo fornece um exemplo de como um aplicativo móvel do Xamarin pode consumir um serviço WCF usando o `BasicHttpBinding` classe. Passo a passo inclui os seguintes tópicos:

1.  **Criar um serviço WCF** -nesta seção, criaremos um serviço WCF muito básico com dois métodos. O primeiro método levará um parâmetro de cadeia de caracteres, enquanto o outro método levará um objeto c#. Esta seção também aborda como configurar a estação de trabalho do desenvolvedor para permitir o acesso remoto para o serviço WCF.
1.  **Criar um aplicativo xamarin** -quando o serviço WCF tiver sido criado, vamos criar um aplicativo xamarin simples que usará o serviço WCF. Esta seção aborda como criar uma classe de proxy de serviço do WCF para facilitar a comunicação com o serviço WCF.
1.  **Criar um aplicativo xamarin** -a parte final deste tutorial envolve a criação de um aplicativo xamarin simples que usará o serviço WCF.

<a name="Requirements" />

## <a name="requirements"></a>Requisitos

Este passo a passo pressupõe que você tenha alguma familiaridade com a criação e uso de serviços WCF.

<a name="Creating_a_WCF_Service" />

## <a name="creating-a-wcf-service"></a>Criando um serviço WCF

A primeira tarefa antes de nós é criar um serviço WCF para um aplicativo móvel para se comunicar com.

1. Inicie o Visual Studio de 2017 e criar um novo projeto.
1. No **novo projeto** caixa de diálogo, selecione o **WCF > biblioteca de serviços WCF** modelo e o nome da solução `HelloWorldService`:

    ![](walkthrough-working-with-wcf-images/new-wcf-service.png "Criar uma nova biblioteca de serviço do WCF")

1. Em **Solution Explorer**, adicione uma nova classe chamada `HelloWorldData` ao projeto:

    ```csharp
        using System.Runtime.Serialization;

        namespace HelloWorldService
        {
            [DataContract]
            public class HelloWorldData
            {
                [DataMember]
                public bool SayHello { get; set; }

                [DataMember]
                public string Name { get; set; }

                public HelloWorldData()
                {
                    Name = "Hello ";
                    SayHello = false;
                }
            }
        }
    ```


1. Em **Solution Explorer**, renomeie `IService1.cs` para `IHelloWorldService.cs`e renomeie `Service1.cs` para `HelloWorldService.cs`.
1. Em **Solution Explorer**, abra `IHelloWorldService.cs` e substitua o código com o código a seguir:

    ```csharp
        using System.ServiceModel;

        namespace HelloWorldService
        {
            [ServiceContract]
            public interface IHelloWorldService
            {
                [OperationContract]
                string SayHelloTo(string name);

                [OperationContract]
                HelloWorldData GetHelloData(HelloWorldData helloWorldData);
            }
        }
    ```
  
    Esse serviço fornece dois métodos — uma que usa uma cadeia de caracteres para um parâmetro e outro que usa um objeto .NET.

1. Em **Solution Explorer**, abra `HelloWorldService.cs` e substitua o código com o código a seguir:

    ```csharp
        using System;

        namespace HelloWorldService
        {
            public class HelloWorldService : IHelloWorldService
            {
                public HelloWorldData GetHelloData(HelloWorldData helloWorldData)
                {
                    if (helloWorldData == null)
                        throw new ArgumentException("helloWorldData");

                    if (helloWorldData.SayHello)
                        helloWorldData.Name = "Hello World to {helloWorldData.Name}";

                    return helloWorldData;
                }

                public string SayHelloTo(string name)
                {
                    return "Hello World to you, {name}";
                }
            }
        }
    ```

1. Em **Solution Explorer**, abra `App.config`, atualizar o `name` atributo do `<service>` nó, o `contract` atributo do `<endpoint>` nó e o `baseAddress` atributo do `<add>` nó:

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            ...
            <services>
              <service name="HelloWorldService.HelloWorldService">
                <endpoint address="" binding="basicHttpBinding" contract="HelloWorldService.IHelloWorldService">
                  <identity>
                    <dns value="localhost" />
                  </identity>
                </endpoint>
                <endpoint address="mex" binding="mexHttpBinding" contract="IMetadataExchange" />
                <host>
                  <baseAddresses>
                    <add baseAddress="http://localhost:8733/Design_Time_Addresses/HelloWorldService/" />
                  </baseAddresses>
                </host>
              </service>
            </services>
            ...
        </configuration>
    ```

1. Compilar e executar o serviço do WCF. O serviço será hospedado pelo cliente de teste do WCF:

    ![](walkthrough-working-with-wcf-images/hosted-wcf-service.png "Serviço do WCF em execução no cliente de teste")

1. Com o cliente de teste do WCF em execução, inicie um navegador e navegue até o ponto de extremidade para o serviço WCF:

    ![](walkthrough-working-with-wcf-images/wcf-service-browser.png "Página de informações do navegador de serviço WCF")

> [!IMPORTANT]
> A seção a seguir só será necessária se você precisa aceitar conexões remotas em uma estação de trabalho do Windows 10. A seção pode ser ignorada se você tiver uma plataforma alternativa no qual deseja implantar o serviço WCF.

<a name="Allow_Remote_Access_to_IIS_Express" />

## <a name="configuring-remote-access-to-iis-express"></a>Configurando o acesso remoto para o IIS Express

Hospedar um WCF localmente é adequado quando conexões vêm apenas do computador local. No entanto, dispositivos remotos (como um dispositivo Android ou um iPhone) não terão qualquer acesso a um serviço WCF local. Portanto, esta seção explica como configurar o Windows 10 e o IIS Express para aceitar conexões remotas:

1.  **Configurar o IIS Express para conexões remotas aceitar** -esta etapa envolve a editar o arquivo de configuração para o IIS Express aceitar conexões remotas em uma porta específica e, em seguida, configurando uma regra para o IIS Express aceitar o tráfego de entrada.
1.  **Adicionar uma exceção ao Firewall do Windows** -você deve abrir uma porta no Firewall do Windows que aplicativos remotos podem usar para se comunicar com o serviço WCF.

    Você precisará saber o endereço IP de sua estação de trabalho. Para os fins deste exemplo vamos supor que nossa estação de trabalho tem o endereço IP 192.168.1.143.

1. Vamos começar configurando o IIS Express para escutar as solicitações externas. Podemos fazer isso editando o arquivo de configuração para o IIS Express no `[solutiondirectory]\.vs\config\applicationhost.config`, conforme mostrado na seguinte captura de tela:

    [![](walkthrough-working-with-wcf-images/image05.png "Podemos fazer isso editando o arquivo de configuração para o IIS Express no solutiondirectory.vsconfigapplicationhost.config, conforme mostrado nesta captura de tela")](walkthrough-working-with-wcf-images/image05.png#lightbox)

    Localize o `site` elemento com o nome `HelloWorldWcfHost`. Ele deve ser algo como o seguinte trecho XML:

    ```xml
        <site name="HelloWorldWcfHost" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="\\vmware-host\Shared Folders\tom\work\xamarin\code\private-samples\webservices\HelloWorld\HelloWorldWcfHost" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:8733:localhost" />
            </bindings>
        </site>
    ```
 
    Precisamos adicionar outro `binding` para abrir a porta 8734 para o tráfego externo. Adicione o seguinte XML para o `bindings` elemento, substituindo o endereço IP com seu próprio endereço IP:

    ```xml
    <binding protocol="http" bindingInformation="*:8734:192.168.1.143" />
    ```
    
    Isso irá configurar o IIS Express para aceitar o tráfego HTTP de qualquer endereço IP remoto na porta 8734 no endereço IP externo do computador. Acima de trecho de código pressupõe que o endereço IP do computador que executa o IIS Express é 192.168.1.143. Após as alterações, o `bindings` elemento deve ser semelhante ao seguinte:

    ```xml
        <site name="HelloWorldWcfHost" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="\\vmware-host\Shared Folders\tom\work\xamarin\code\private-samples\webservices\HelloWorld\HelloWorldWcfHost" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:8733:localhost" />
                <binding protocol="http" bindingInformation="*:8734:192.168.1.143" />
            </bindings>
        </site>
    ```

1. Em seguida, é preciso configurar o IIS Express aceitar conexões de entrada na porta 8734. Inicialização do backup de um prompt de comando administrativo e execute este comando:

    `> netsh http add urlacl url=http://192.168.1.143:9608/ user=everyone`

1. A etapa final é configurar o Firewall do Windows para permitir o tráfego externo na porta 8734. Em um prompt de comando administrativo, execute o seguinte comando:

    `> netsh advfirewall firewall add rule name="IISExpressXamarin" dir=in protocol=tcp localport=8734 profile=private remoteip=localsubnet action=allow`

    Esse comando permitirá que o tráfego de entrada na porta 8734 de todos os dispositivos na mesma sub-rede da estação de trabalho do Windows 10.

Você criou um serviço WCF muito básico hospedado no IIS Express que aceitará conexões de entrada de outros dispositivos ou computadores em nosso sub-rede. Você pode testar essa saída executando seu aplicativo e visitando `http://localhost:8733/Design_Time_Addresses/HelloWorldService/` na estação de trabalho e `http://192.168.1.143:8734/Design_Time_Addresses/HelloWorldService/` de outro computador na sua sub-rede.

Para permitir que o IIS Express manter em execução e que atende o serviço, desative o **editar e continuar** opção *propriedades do projeto > Web > depuradores*.

## <a name="creating-a-proxy-for-the-web-service"></a>Criando um Proxy para o serviço Web

Um proxy de serviço da web deve ser criado para o serviço WCF, antes de um aplicativo pode consumir o serviço. Isso pode ser feito da seguinte maneira:

1. Adicionar uma biblioteca de classe padrão do .NET chamado `HelloWorldServiceProxy`e excluir todas as classes no projeto.
1. Execute o `HelloWorldService` projeto.
1. Com o `HelloWorldService` projeto está em execução, adicione uma nova **serviço conectado** para o projeto, usando o **provedor de referência de serviço do Microsoft WCF Web**.
1. No **ponto de extremidade de serviço** guia do **configurar referência de serviço da Web do WCF** caixa de diálogo, clique o **Discover** botão, exclua `mex` do final do ponto de extremidade no **URI** lista suspensa, digite `HelloWorldServiceProxy` como o **Namespace**e clique no **próximo** botão.
1. No **opções de tipo de dados** guia do **configurar referência de serviço da Web do WCF** caixa de diálogo, aceite os padrões clicando o **próximo** botão.
1. No **opções de cliente** guia do **configurar referência de serviço da Web do WCF** caixa de diálogo, certifique-se de que o **pública** caixa de seleção está marcada e clique no **concluir**  botão.
1. Criar o `HelloWorldServiceProxy` projeto.

> [!NOTE]
> Uma alternativa para criar o proxy usando o provedor de referência de serviço Web do Microsoft WCF no Visual Studio de 2017 é usar a ferramenta de utilitário de metadados do ServiceModel (svcutil.exe). Para obter mais informações, consulte [Ferramenta Utilitária de metadados ServiceModel (Svcutil.exe)](https://docs.microsoft.com/en-us/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Creating_a_Xamarin_Android_Application" />

## <a name="creating-a-xamarinandroid-application"></a>Criando um aplicativo xamarin

O proxy do serviço WCF pode ser consumido por um aplicativo xamarin, da seguinte maneira:

1. No Visual Studio, adicione um novo projeto em branco do Android para a solução e denomine- `HelloWorld.Android`.
1. No `HelloWorld.Android` do projeto, adicione uma referência para o `HelloWorldServiceProxy` projeto e uma referência para o `System.ServiceModel` namespace.
1. Em **Solution Explorer**, abra `Resources/layout/main.axml` e substitua o XML existente com o seguinte XML:

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                  android:orientation="vertical"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent">
            <LinearLayout
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="0px"
                    android:layout_weight="1">
                <Button
                        android:id="@+id/sayHelloWorldButton"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:text="@string/say_hello_world" />
                <TextView
                        android:text="Large Text"
                        android:textAppearance="?android:attr/textAppearanceLarge"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:id="@+id/sayHelloWorldTextView" />
            </LinearLayout>
            <LinearLayout
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="0px"
                    android:layout_weight="1">
                <Button
                        android:id="@+id/getHelloWorldDataButton"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:text="@string/get_hello_world_data" />
                <TextView
                        android:text="Large Text"
                        android:textAppearance="?android:attr/textAppearanceLarge"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:id="@+id/getHelloWorldDataTextView" />
            </LinearLayout>
        </LinearLayout>
    ```
    
    As capturas de tela a seguir mostra a interface do usuário no designer:

    [![](walkthrough-working-with-wcf-images/image09.png "Isso é uma captura de tela de aparência nessa interface do usuário no designer")](walkthrough-working-with-wcf-images/image09.png#lightbox)
    
1. Em **Solution Explorer**, abra `Resources/values/Strings.xml` e adicione o seguinte XML:

    ```xml
    <string name="say_hello_world">Say Hello World</string>
    <string name="get_hello_world_data">Get Hello World data</string>
    ```
    
1. Em **Solution Explorer**, abra `MainActivity.cs` e substitua o código existente pelo seguinte código:

    ```csharp
        [Activity(Label = "HelloWorld.Android", MainLauncher = true)]
        public class MainActivity : Activity
        {
            static readonly EndpointAddress Endpoint = new EndpointAddress("<insert_WCF_service_endpoint_here>");

            HelloWorldServiceClient _client;
            Button _getHelloWorldDataButton;
            TextView _getHelloWorldDataTextView;
            Button _sayHelloWorldButton;
            TextView _sayHelloWorldTextView;
            ...
        }
    ```

    Substituir `<insert_WCF_service_endpoint_here>` com o endereço do seu ponto de extremidade do WCF.

1. Em `MainActivity.cs`, modifique o `OnCreate` método para que ele contém o código a seguir:

    ```csharp
        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            InitializeHelloWorldServiceClient();

            // This button will invoke the GetHelloWorldData - the method that takes a C# object as a parameter.
            _getHelloWorldDataButton = FindViewById<Button>(Resource.Id.getHelloWorldDataButton);
            _getHelloWorldDataButton.Click += GetHelloWorldDataButtonOnClick;
            _getHelloWorldDataTextView = FindViewById<TextView>(Resource.Id.getHelloWorldDataTextView);

            // This button will invoke SayHelloWorld - this method takes a simple string as a parameter.
            _sayHelloWorldButton = FindViewById<Button>(Resource.Id.sayHelloWorldButton);
            _sayHelloWorldButton.Click += SayHelloWorldButtonOnClick;
            _sayHelloWorldTextView = FindViewById<TextView>(Resource.Id.sayHelloWorldTextView);
        }
    ```
    
    O código acima inicializa as variáveis de instância para a classe e conecta alguns manipuladores de eventos.

1. Em `MainActivity.cs`, instanciar a classe de proxy de cliente, adicionando os dois métodos a seguir:

    ```csharp
        void InitializeHelloWorldServiceClient()
        {
            BasicHttpBinding binding = CreateBasicHttpBinding();
            _client = new HelloWorldServiceClient(binding, Endpoint);
        }

        static BasicHttpBinding CreateBasicHttpBinding()
        {
            BasicHttpBinding binding = new BasicHttpBinding
            {
                Name = "basicHttpBinding",
                MaxBufferSize = 2147483647,
                MaxReceivedMessageSize = 2147483647
            };

            TimeSpan timeout = new TimeSpan(0, 0, 30);
            binding.SendTimeout = timeout;
            binding.OpenTimeout = timeout;
            binding.ReceiveTimeout = timeout;
            return binding;
        }
    ```
    
    O código acima cria e inicializa um `HelloWorldServiceClient` objeto.

1. Em `MainActivity.cs`, adicionar manipuladores até mesmo para os dois botões de `Activity`:

    ```csharp
        async void GetHelloWorldDataButtonOnClick(object sender, EventArgs e)
        {
            var data = new HelloWorldData
            {
                Name = "Mr. Chad",
                SayHello = true
            };

            _getHelloWorldDataTextView.Text = "Waiting for WCF...";
            HelloWorldData result;
            try
            {
                result = await _client.GetHelloDataAsync(data);
                _getHelloWorldDataTextView.Text = result.Name;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }

        async void SayHelloWorldButtonOnClick(object sender, EventArgs e)
        {
            _sayHelloWorldTextView.Text = "Waiting for WCF...";
            try
            {
                var result = await _client.SayHelloToAsync("Kilroy");
                _sayHelloWorldTextView.Text = result;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
    ```
  
1. Executar o aplicativo, certifique-se de que o serviço WCF está em execução e clique em dois botões. O aplicativo chama o WCF de forma assíncrona, contanto que o `Endpoint` campo está definido corretamente:

    [![](walkthrough-working-with-wcf-images/image08.png "Dentro de 30 segundos, uma resposta deve ser recebida de cada método WCF e o aplicativo deve ser semelhante esta captura de tela")](walkthrough-working-with-wcf-images/image08.png#lightbox)

<a name="Creating_a_Xamarin_iOS_Application" />

## <a name="creating-a-xamarinios-application"></a>Criando um aplicativo xamarin

O proxy do serviço WCF pode ser consumido por um aplicativo xamarin, da seguinte maneira:

1. No Visual Studio, adicione um novo iPhone **único aplicativo de exibição** projeto à solução e nomeie-o `HelloWorld.iOS`.
1. No `HelloWorld.iOS` do projeto, adicione uma referência para o `HelloWorldServiceProxy` projeto e uma referência para o `System.ServiceModel` namespace.
1. Em **Solution Explorer**, clique duas vezes em `Main.storyboard` para abrir o arquivo no designer de iOS. Em seguida, adicione o seguinte `UIButton` e `UITextView` controles:

    ||Nome|Título|
    |--- |--- |--- |
    |`UIButton`|`sayHelloWorldButton`|Digamos que "Olá, mundo"|
    |`UITextView`|`sayHelloWorldText`||
    |`UIButton`|`getHelloWorldDataButton`|Obter "Olá, mundo" dados|
    |`UITextView`|`getHelloWorldDataText`||

    Depois de adicionar os controles, a interface do usuário deve ser semelhante a captura de tela a seguir:

    [![](walkthrough-working-with-wcf-images/image12.png "Depois de adicionar os controles, a interface do usuário deve ser semelhante a esta captura de tela")](walkthrough-working-with-wcf-images/image12.png#lightbox)

1. Em **Solution Explorer**, abra `ViewController.cs` e adicione o seguinte código:

    ```xml
        public partial class ViewController : UIViewController
        {
            static readonly EndpointAddress Endpoint = new EndpointAddress("<insert_WCF_service_endpoint_here>");
            HelloWorldServiceClient _client;
            ...
        }
    ```
  
    Substituir `<insert_WCF_service_endpoint_here>` com o endereço do seu ponto de extremidade do WCF.

1. Em `ViewController.cs`, atualize o `ViewDidLoad` método para que ele se parece com o seguinte:

    ```csharp
        public override void ViewDidLoad()
        {
            base.ViewDidLoad();
            InitializeHelloWorldServiceClient();

            getHelloWorldDataButton.TouchUpInside += GetHelloWorldDataButton_TouchUpInside;
            sayHelloWorldButton.TouchUpInside += SayHelloWorldButton_TouchUpInside;
        }
    ```
  
1. Em `ViewController.cs`, adicione o `InitializeHelloWorldServiceClient` e `CreateBasicHttpBinding` métodos:

    ```csharp
        void InitializeHelloWorldServiceClient()
        {
            BasicHttpBinding binding = CreateBasicHttpBinding();
            _client = new HelloWorldServiceClient(binding, Endpoint);
        }

        static BasicHttpBinding CreateBasicHttpBinding()
        {
            BasicHttpBinding binding = new BasicHttpBinding
            {
                Name = "basicHttpBinding",
                MaxBufferSize = 2147483647,
                MaxReceivedMessageSize = 2147483647
            };

            TimeSpan timeout = new TimeSpan(0, 0, 30);
            binding.SendTimeout = timeout;
            binding.OpenTimeout = timeout;
            binding.ReceiveTimeout = timeout;
            return binding;
        }
    ```
  
1. Em `ViewController.cs`, adicionar manipuladores de eventos para o `TouchUpInside` eventos nos dois `UIButton` instâncias:

    ```csharp
        async void GetHelloWorldDataButton_TouchUpInside(object sender, EventArgs e)
        {
            getHelloWorldDataText.Text = "Waiting for WCF...";
            var data = new HelloWorldData
            {
                Name = "Mr. Chad",
                SayHello = true
            };

            HelloWorldData result;
            try
            {
                result = await _client.GetHelloDataAsync(data);
                getHelloWorldDataText.Text = result.Name;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }

        async void SayHelloWorldButton_TouchUpInside(object sender, EventArgs e)
        {
            sayHelloWorldText.Text = "Waiting for WCF...";
            try
            {
                var result = await _client.SayHelloToAsync("Kilroy");
                sayHelloWorldText.Text = result;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
    ```

1. Executar o aplicativo, certifique-se de que o serviço WCF está em execução e clique em dois botões. O aplicativo chama o WCF de forma assíncrona, contanto que o `Endpoint` campo está definido corretamente:

    [![](walkthrough-working-with-wcf-images/image10.png "Dentro de 30 segundos, uma resposta deve ser recebida de cada método WCF e o aplicativo deve se parecer com esta captura de tela")](walkthrough-working-with-wcf-images/image10.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Resumo

Este tutorial abordados como trabalhar com um serviço WCF em um aplicativo móvel usando o xamarin e xamarin. Ele mostrou como criar um serviço WCF e explicou como configurar o Windows 10 e o IIS Express para aceitar conexões de dispositivos remotos. Ele explicou como gerar um cliente de proxy do WCF e demonstrou como usar o cliente de proxy em aplicativos xamarin e xamarin.


## <a name="related-links"></a>Links relacionados

- [HelloWorld (exemplo)](https://developer.xamarin.com/samples/mobile/WCF-Walkthrough/)
- [Desenvolvendo aplicativos orientados a serviço com WCF](https://docs.microsoft.com/en-us/dotnet/framework/wcf/index)
- [Como: criar um cliente do Windows Communication Foundation](https://docs.microsoft.com/en-us/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [Ferramenta de utilitário de metadados ServiceModel (svcutil.exe)](https://docs.microsoft.com/en-us/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
