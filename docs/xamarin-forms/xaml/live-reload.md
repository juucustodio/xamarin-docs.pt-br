---
title: Recarregamento dinâmico do Xamarin (visualização)
description: Veja as alterações em seu XAML refletidas ao vivo, sem a necessidade de compilação de outro e implantar.
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
robots: noindex
ms.date: 10/26/2018
ms.openlocfilehash: 21ff09f2af93ee46578b959111bf744ba05a74d7
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617651"
---
# <a name="xamarin-live-reload-preview"></a>Recarregamento dinâmico do Xamarin (visualização)

> [!NOTE]
> A visualização do Xamarin Live Reload terminou, e queremos agradecer a todos por seus comentários e comentários. Leia nossa [roteiro](https://docs.microsoft.com/visualstudio/productinfo/vs-roadmap) para Visual Studio de 2019 saber mais sobre os novos recursos de produtividade que estamos trabalhando para xamarin. Forms. Essa extensão permanecerão disponível para o Visual Studio 2017, mas não receberá atualizações futuras.

Xamarin Live Reload permite que você **fazer alterações em seu XAML e vê-los refletidas ao vivo, sem a necessidade de compilação de outro e implantar**. As alterações feitas no seu XAML serão reimplantadas em Salvar e refletidas no seu destino da implantação.

## <a name="requirements"></a>Requisitos

* [Visual Studio 2017 versão 15.7 ou posterior](https://visualstudio.microsoft.com/vs/) com o **desenvolvimento móvel com .NET** carga de trabalho.
* [Xamarin. Forms 3.0.0 ou acima](https://www.nuget.org/packages/Xamarin.Forms/).

## <a name="getting-started"></a>Guia de Introdução
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1. Instalar o recarregamento dinâmico do Xamarin no Visual Studio Marketplace

Xamarin Live Reload é distribuído por meio do Visual Studio Marketplace. Para instalar a extensão, visite o [página do Xamarin Live Reload no Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload) site e clique em **baixar**.

Abra o. VSIX que é baixado e, em seguida, clique em **instalar**.

![Confirmação do Xamarin Live Reload instalador do Visual Studio](images/LiveReloadVSIXInstall.png)

Como alternativa, você pode pesquisar por ele na **Online** guia o **extensões e atualizações** diálogo dentro do Visual Studio.

### <a name="2-configure-your-app-to-use-live-reload"></a>2. Configurar seu aplicativo para usar o Live Reload

Adicionar o Live Reload para aplicativos móveis existentes pode ser feito em três etapas:

1. Verifique se todos os projetos são atualizados para usar [xamarin. Forms 3.0.0 ou acima](https://www.nuget.org/packages/Xamarin.Forms/) ou superior.

2. Adicione a **Xamarin.LiveReload** pacote do NuGet:

    a. **.NET standard** – instale o **Xamarin.LiveReload** NuGet para a biblioteca do .NET Standard 2.0. Isso não precisa ser instalado em seus projetos de plataforma. Certifique-se de que o **origem do pacote** é definido como **todos os**.
    
    b. **Projetos compartilhados** – instale o **Xamarin.LiveReload** NuGet em todos os projetos de plataforma (como Android, iOS, UWP, etc.). Certifique-se de que o **origem do pacote** é definido como **todos os**.

    [![Adicionar NuGet do Xamarin recarregamento dinâmico com o Gerenciador de pacotes do NuGet](images/addlivereloadnuget.w157-sml.png)](images/addlivereloadnuget.w157.png#lightbox)

3. Adicione `LiveReload.Init();` para o construtor no `Application` de classe, conforme mostrado no trecho de código a seguir:

```csharp
public partial class App : Application
{
    public App ()
    {
        // Initialize Live Reload.
        #if DEBUG
        LiveReload.Init();
        #endif
        
        InitializeComponent();
        MainPage = new MainPage();
    }
}
```

### <a name="3-start-live-reloading"></a>3. Iniciar recarregamento dinâmico

Compile e implante seu aplicativo. Depois que o aplicativo for implantado, abra um arquivo XAML, fazer algumas alterações e salve o arquivo. As alterações são implantados novamente para o destino da implantação.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

Ao vivo works recarregar com alterações a qualquer arquivo XAML. Alterações em c# ou adição/remoção de pacotes do NuGet requer uma nova compilação e implante entrem em vigor.

## <a name="frequently-asked-questions"></a>Perguntas frequentes 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>Xamarin Live Reload está disponível no Visual Studio para Mac? 

Não, a versão de visualização do Xamarin Live Reload só está disponível para Visual Studio 2017.

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>Isso funciona com todas as bibliotecas, como Prism? 

Como seu aplicativo é compilado, o Live Reload funciona com todas as bibliotecas, como Prism e bibliotecas de controle de terceiros, como Telerik, Infragistics, Syncfusion, ArcGIS, GrapeCity e outros fornecedores de controle.

### <a name="what-changes-does-live-reload-redeploy"></a>As alterações que o Live Reload reimplantar? 

Recarregamento dinâmico só se aplica as alterações feitas em XAML ou CSS. Se você fizer alterações em um arquivo c#, uma recompilação será necessária. 

### <a name="what-platforms-are-supported"></a>Quais plataformas têm suporte? 

Recarregamento dinâmico funciona em qualquer plataforma com suporte pelo xamarin. Forms, incluindo iOS, Android e UWP.

### <a name="does-this-work-on-emulators-simulators-and-physical-devices"></a>Isso funciona em emuladores, simuladores e dispositivos físicos? 

Sim, o Live Reload funciona com todos os destinos de implantação válido, incluindo dispositivos físicos, simuladores de iOS e Android emulators. Implantação em um dispositivo exige que o dispositivo e o computador estar na mesma rede Wi-Fi.

### <a name="does-this-work-with-corporate-networks"></a>Isso funciona com redes corporativas?

Se você está depurando um simulador de iOS ou Android emulator, o Live Reload usa localhost para se comunicar. Se você deseja implantar em um dispositivo, o dispositivo e o computador deve estar na mesma rede Wi-Fi. Em cenários em que isso não for possível, você pode [configurar seu próprio servidor do Live Reload](#live-reload-server), que lhe permitirá ao recarregar Live, independentemente das configurações de conectividade de rede.

### <a name="does-it-require-debugging-the-app"></a>Ele requer depuração do aplicativo? 

Nº Na verdade, você pode até mesmo iniciar todos os seus destinos com suporte do aplicativo (Android, iOS e UWP) em qualquer número de dispositivos ou simuladores/emuladores e ver todos eles atualização ao mesmo tempo. 

## <a name="limitations"></a>Limitações

* Há suporte para recarregar somente de XAML.
* Estado da interface do usuário não pode ser mantido entre reimplanta, a menos que o uso do MVVM.

## <a name="known-issues"></a>Problemas Conhecidos

* Só tem suporte no Visual Studio.
* Vinculação deve ser definida como **não vincular** ou **vincular somente SDKs de estruturas** 
* Recarregar os recursos de todo o aplicativo (ou seja, **App. XAML** ou compartilhar dicionários de recursos), navegação de aplicativo é redefinida. 
* Recarregamento do ContentView atualmente requer recarregar a página que contém.
* Elementos que contêm AutomationId podem causar uma falha de recarregar.
* Editando o XAML enquanto depuração UWP pode causar uma falha de tempo de execução. Solução alternativa: Use **iniciar sem depuração (Ctrl + F5)** em vez de **iniciar depuração (F5)**.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="error-codes"></a>Códigos de erro

* **XLR001**: *o projeto atual faz referência a versão do pacote NuGet 'Xamarin.LiveReload' '[VERSION]', mas a extensão do Xamarin Live Reload requer a versão '[VERSION]'.*

  Para permitir que a iteração rápida e a evolução do recurso de Live Reload, o pacote do nuget e a extensão do Visual Studio devem corresponder exatamente. Atualize o pacote do nuget para a mesma versão da extensão que você instalou.

* **XLR002**: *Live Reload requer pelo menos a propriedade 'MqttHostname' ao compilar na linha de comando. Como alternativa, definir 'EnableLiveReload' como 'false' para desabilitar o recurso.*

  As propriedades exigidas pelo Live Reload não estão disponíveis quando compilar da linha de comando (ou em integração contínua) e, portanto, deve ser fornecido explicitamente. 

* **XLR003**: *pacote do nuget Live Reload requer instalação da extensão do Xamarin Live Reload para Visual Studio.*

  Tentativa de compilar um projeto que referencia o pacote do nuget Live Reload, mas a extensão do Visual não está instalada.  

* *Exceção ao carregar assemblies: System.IO.FileNotFoundException: não foi possível carregar o assembly ' Xamarin.Live.Reload, versão = 0.3.27.0, Culture = neutral, PublicKeyToken ='.*

  O projeto de host deve usar `PackageReference` em vez de `packages.config`

### <a name="app-doesnt-connect"></a>Aplicativo não se conectar

Quando o aplicativo é compilado, as informações a partir **Ferramentas > Opções > Xamarin > Live Reload** (chaves de criptografia, porta e nome de host) são inseridas no aplicativo, portanto, que, quando `LiveReload.Init()` é executado, nenhuma configuração ou emparelhamento é necessário para a conexão seja bem-sucedida.

Diferente de normais problemas de rede (firewall, dispositivo em uma rede diferente), o principal motivo que o aplicativo não pode conectar com êxito a IDE é porque sua configuração for diferente no Visual Studio. Isso pode acontecer se:

* Aplicativo foi compilado em um computador diferente.
* Aplicativo foi compilado e implantado em uma sessão diferente do Visual Studio, e **gerar chaves de criptografia** está marcada (padrão) **Ferramentas > Opções > Xamarin > Live Reload**.
* Configurações do Visual Studio foram alteradas (ou seja, nome do host, porta ou criptografia de chaves), mas o aplicativo não foi criado e implantado novamente.

Esses casos são resolvidos por criar e implantar o aplicativo novamente.

### <a name="uninstalling-preview-1"></a>Desinstalando a versão prévia 1

Se você tiver uma visualização mais antiga e você tiver problemas para desinstalá-lo, siga estas etapas:

1. Exclua a pasta **C:\Program Files (x86) \Microsoft Visual Studio\Preview\Enterprise\Common7\IDE\Extensions\Xamarin\LiveReload** (Observação: substitua "Enterprise" com sua edição instalada e "Preview" com "2017" Se você instalado para um VS estável)
2. Abra uma **prompt de comando do desenvolvedor** para que o Visual Studio e execute `devenv /updateconfiguration`. 

## <a name="tips--tricks"></a>Dicas e truques

* Desde que as configurações de Live Reload não são alterados (incluindo as chaves de criptografia, por exemplo, se você desativar **gerar chaves de criptografia**) e criar na mesma máquina, não é necessário compilar e implantar o aplicativo após inicial Implante, a menos que você altere o código ou dependências. Você pode simplesmente iniciar novamente um aplicativo implantado anteriormente e ele se conectará ao último host usado.

* Não há nenhuma limitação em quantos dispositivos você pode se conectar à mesma sessão do Visual Studio. Você pode implantar e iniciar o aplicativo em dispositivos/simuladores tantos conforme necessário para ver o trabalho recarregando ao vivo em todos eles ao mesmo tempo.

* Recarregamento dinâmico recarregará apenas parte da interface do usuário do seu aplicativo, mas faz *não* recriar suas páginas, nem ela substitua seu modelo de exibição (ou o contexto de associação). Isso significa que o *inteira* estado do aplicativo sempre é preservado recarregamentos, incluindo suas dependências injetadas.

## <a name="live-reload-server"></a>Servidor de recarregamento dinâmico

Em cenários em que uma conexão de aplicativo em execução em seu computador (conforme indicado pelo uso `localhost` ou `127.0.0.1` na **Ferramentas > Opções > Xamarin > Live Reload**) não é possível (ou seja, firewalls, redes diferentes), Você pode configurar um servidor remoto em vez disso, que o IDE e o aplicativo irão conectar a.

Recarregamento dinâmico usa o padrão [protocolo MQTT](http://mqtt.org/) para troca de mensagens e, portanto, podem se comunicar com [servidores de terceiros](https://github.com/mqtt/mqtt.github.io/wiki/servers). Há até mesmo [servidores públicos](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers) (também conhecido como *agentes*) disponíveis que você pode usar. Recarregamento dinâmico foi testado com `broker.hivemq.com` e `iot.eclipse.org` nomes de host, bem como os serviços fornecidos pelo [www.cloudmqtt.com](https://www.cloudmqtt.com) e [www.cloudamqp.com](https://www.cloudamqp.com). Você também pode implantar seu próprio servidor MQTT na nuvem, como [HiveMQ no Azure](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud).

Você pode configurar qualquer porta, mas é comum usar a porta 1883 padrão para servidores remotos. Live Reload mensagens usam forte ponta a ponta AES criptografia simétrica, portanto, é seguro para se conectar a servidores remotos. Por padrão, a chave de criptografia e o vetor de inicialização (IV) são recriados em cada sessão do Visual Studio.

Provavelmente, a maneira mais fácil é instalar o [mosquitto](https://mosquitto.org) server em uma VM do Ubuntu em branco no Azure:

1. Criar uma nova VM do Ubuntu Server no Portal do Azure
2. Adicionar uma nova regra de porta de entrada para 1883 (porta MQTT padrão) na guia rede
3. Abra o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) (modo de bash)
4. Digite `ssh [USERNAME]@[PUBLIC_IP]` usando o nome de usuário escolhido em 1) e o IP público mostrado em sua página de visão geral VM
5. Executar `sudo apt-get install mosquitto`, inserindo a senha que você escolheu em 1)

Agora você pode usar esse IP para se conectar ao seu próprio servidor MQTT.
