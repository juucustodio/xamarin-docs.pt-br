---
title: Recarregar ao vivo
description: Consulte as alterações para o XAML ao vivo, refletidas sem a necessidade de compilação de outra e implantar.
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
ms.date: 04/23/2018
ms.openlocfilehash: 11e876207285689b230bb2fada3a4c836443e360
ms.sourcegitcommit: a69439ad4c9fd0abe759143687d3b23582573d90
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="xamarin-live-reload"></a>Recarregar ao vivo do Xamarin

![Visualizar](~/media/shared/preview.png)

Xamarin Live recarregar permite que você **fazer alterações para o XAML e vê-los ao vivo, refletidas sem a necessidade de compilação de outra e implantar**. Todas as alterações feitas ao XAML serão reimplantadas salvar e refletidas no seu destino da implantação.

Como o seu aplicativo é compilado ao usar o Live recarregar, ele funciona com todas as bibliotecas e controles de terceiros. Dinâmica funciona de recarregamento em todas as plataformas xamarin. Forms oferece suporte, incluindo UWP, iOS e Android e funciona em todos os destinos de implantação válido, incluindo simuladores e emuladores de dispositivos físicos.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

Recarregar ao vivo está disponível atualmente apenas no Visual Studio de 2017.

## <a name="requirements"></a>Requisitos

* [Visual Studio 2017 15,7 Preview 4](https://www.visualstudio.com/vs/preview/) ou superior com o **desenvolvimento móvel com o .NET** carga de trabalho.
* [Xamarin. Forms 3.0.354232-pre3](https://www.nuget.org/packages/Xamarin.Forms/3.0.0.354232-pre3) ou superior.

## <a name="getting-started"></a>Guia de Introdução
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1. Instale Xamarin recarregamento de ao vivo do Visual Studio Marketplace.

Xamarin Live recarregar é distribuído por meio do Visual Studio Marketplace. Para instalar a extensão, visite o [página recarregar Live do Xamarin no Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload) site e clique em **baixar**.

Abra o .vsix que é baixado e, em seguida, clique em **instalar**.

![Confirmação de Xamarin Live recarregar instalador do Visual Studio](images/LiveReloadVSIXInstall.png)

> Como alternativa, você pode pesquisar por ele no **Online** guia o **extensões e atualizações** diálogo dentro do Visual Studio.

### <a name="2-configure-your-app-to-use-live-reload"></a>2. Configure seu aplicativo para usar ao vivo recarregar.

Adicionando ao vivo recarregar a aplicativos móveis existentes pode ser feito em três etapas:

1. Certifique-se de que todos os projetos são atualizados para usar [3.0.354232-pre3 xamarin. Forms](https://www.nuget.org/packages/Xamarin.Forms/3.0.0.354232-pre3) ou superior.
2. Instalar o **Xamarin.LiveReload** NuGet na sua biblioteca do .NET 2.0 padrão. Isso não precisa ser instalado em seus projetos de plataforma. Certifique-se de que o **origem do pacote** é definido como **todos os**.

![Adicionar Xamarin ao vivo recarregar NuGet com o NuGet Package Manager](images/addlivereloadnuget.png)

3. Adicionar `LiveReload.Init();` para o construtor de `Application` classe, conforme mostrado no trecho de código a seguir:

```csharp
public partial class App : Application
{
    public App ()
    {
        // Initialize Live Reload.
        LiveReload.Init();
    
        InitializeComponent();
        MainPage = new MainPage();
    }
}
```

### <a name="3-start-live-reloading"></a>3. Inicie recarregamento ao vivo.

Compilar e implantar seu aplicativo. Depois que o aplicativo é implantado, abra um arquivo XAML, fazer algumas alterações e salve o arquivo. As alterações são realocadas para o destino de implantação.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

Dinâmica funciona recarregar com as alterações nos arquivos XAML. Alterações em c# ou adição/remoção de pacotes do NuGet requer uma nova compilação e implantar entrem em vigor.

## <a name="frequently-asked-questions"></a>Perguntas frequentes 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>Xamarin Live recarregar está disponível no Visual Studio para Mac? 

A versão de visualização inicial do Xamarin Live recarregar só está disponível para o Visual Studio de 2017. Suporte para o Visual Studio para Mac está planejado para uma versão futura.

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>Isso funciona com todas as bibliotecas, como prisma? 

Como o seu aplicativo é compilado, Live recarregar funciona com todas as bibliotecas, como prisma e bibliotecas de controle de terceiros, como Telerik Infragistics, Syncfusion, ArcGIS, GrapeCity e outros fornecedores de controle.

### <a name="what-changes-does-live-reload-redeploy"></a>As alterações que o Live recarregar reimplantar? 

Recarregar dinâmica só se aplica a alterações feitas em XAML. Se você fizer alterações em um arquivo c#, a recompilação será necessária. Suporte para recarregar c# está planejado para uma versão futura.

### <a name="what-platforms-are-supported"></a>Quais plataformas têm suporte? 

Recarregar dinâmica funciona em qualquer plataforma com suporte de xamarin. Forms, incluindo UWP, iOS e Android.

### <a name="does-this-work-on-emulators-simulators-and-physical-devices"></a>Isso funciona em dispositivos físicos, simuladores e emuladores? 

Sim, Live recarregar funciona com todos os destinos de implantação válido, incluindo emuladores Android, iOS simuladores e dispositivos físicos. Implantação em um dispositivo exige que o dispositivo e o computador estar na mesma rede Wi-Fi.

### <a name="does-this-work-with-corporate-networks"></a>Isso funciona com redes corporativas?

Se você estiver depurando um emulador Android ou iOS simulador, Live recarregar usa localhost para se comunicar. Se você deseja implantar em um dispositivo, o dispositivo e o computador devem estar na mesma rede Wi-Fi. Em cenários em que isso não for possível, você pode [configurar seu próprio servidor recarregar Live](#live-reload-server), que permite que você para recarregar Live, independentemente das configurações de conectividade de rede.

### <a name="does-it-require-debugging-the-app"></a>Ele requer o aplicativo de depuração? 

Nº Na verdade, você pode até mesmo iniciar todos os destinos seu aplicativo com suporte (Android, iOS e UWP) em qualquer número de dispositivos ou simuladores/emuladores e ver todos eles atualizar de uma vez. 

## <a name="limitations"></a>Limitações

* Há suporte para recarregar somente de XAML.
* Só tem suporte no Visual Studio.
* Funciona apenas com bibliotecas .NET padrão.
* Não há suporte para folhas de estilo CSS.
* Estado da interface do usuário não pode ser mantido entre reimplanta, a menos que usando MVVM.

## <a name="live-reload-server"></a>Servidor de recarregar ao vivo

Em cenários em que uma conexão de aplicativo em execução em seu computador (conforme indicado por meio `localhost` ou `127.0.0.1` na **Ferramentas > Opções > Xamarin > Live recarregar**) não é possível (por exemplo, firewalls, redes diferentes), Você pode configurar um servidor remoto em vez disso, que o aplicativo e o IDE conect para.

Recarregar ao vivo usa o padrão de [protocolo MQTT](http://mqtt.org/) para trocar mensagens e, portanto, podem se comunicar com [servidores de terceiros](https://github.com/mqtt/mqtt.github.io/wiki/servers). Existem até mesmo [servidores públicos](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers) (também conhecido como *agentes*) disponíveis que podem ser usados. Recarregar ao vivo foi testado com `broker.hivemq.com` e `iot.eclipse.org` nomes de host, bem como os serviços fornecidos pelo [www.cloudmqtt.com](https://www.cloudmqtt.com) e [www.cloudamqp.com](https://www.cloudamqp.com). Você também pode implantar seu próprio servidor MQTT na nuvem, como [HiveMQ no Azure](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud) ou [MQ coelho em AWS](http://www.rabbitmq.com/ec2.html). 

Você pode configurar qualquer porta, mas é comum para usar a porta de 1883 padrão para servidores remotos. Ao vivo recarregar mensagens usam forte ponta a ponta AES criptografia simétrica, portanto, é seguro se conectar a servidores remotos. Por padrão, a chave de criptografia e o vetor de inicialização (IV) são recriados em cada sessão do Visual Studio.

## <a name="troubleshooting"></a>Solução de problemas

Quando o aplicativo é compilado, as informações de **Ferramentas > Opções > Xamarin > Live recarregar** (chaves de criptografia, porta e nome de host) são inseridas no aplicativo, assim que, quando `LiveReload.Init();` for executado, nenhuma configuração ou emparelhamento é necessário para a conexão seja bem-sucedida.

Diferente de normais problemas de rede (firewall, o dispositivo em uma rede diferente), o principal motivo que o aplicativo não pode conectar com êxito a IDE é porque sua configuração é diferente no Visual Studio. Isso pode ocorrer se:

* Aplicativo foi compilado em um computador diferente.
* Aplicativo foi compilado e implantado em uma sessão diferente do Visual Studio, e **de geração automática de chaves de criptografia** está marcada (padrão) **Ferramentas > Opções > Xamarin > Live recarregar**.
* Configurações do Visual Studio foram alteradas (ou seja, chaves de criptografia, porta ou nome de host), mas o aplicativo não foi compilado e implantado novamente.

Nesses casos são resolvidos por compilar e implantar o aplicativo novamente.

## <a name="tips--tricks"></a>Dicas e truques

* Como não altere as configurações de recarregamento em tempo real (incluindo as chaves de criptografia, como se você desativar **de geração automática de chaves de criptografia**) e você criar no mesmo computador, você não precisa compilar e implantar o aplicativo após o inicial Implante, a menos que você altere o código ou dependências. Você pode simplesmente iniciar novamente um aplicativo implantado anteriormente e ele se conectará ao último host usado.

* Não há nenhuma limitação em quantos dispositivos que você pode se conectar à mesma sessão do Visual Studio. Você pode implantar e iniciar o aplicativo em dispositivos/simuladores tantos conforme necessário para ver o trabalho recarregando ao vivo em todos eles ao mesmo tempo.

* Recarregar dinâmica será recarregado somente a parte de interface do usuário do seu aplicativo, mas *não* crie novamente suas páginas, nem ele substitui o modelo de exibição (ou contexto de associação). Isso significa que o *todo* estado do aplicativo sempre é preservado em recargas, incluindo suas dependências injetadas.
