---
title: Recarregamento do Xamarin Live (versão prévia)
description: Veja as alterações em seu XAML refletidas em tempo real, sem a necessidade de outra compilação e implantação.
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
robots: noindex
ms.date: 10/26/2018
ms.openlocfilehash: a5a5a9acf47603601461660df689a7a5fa6aee00
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728337"
---
# <a name="xamarin-live-reload-preview"></a>Recarregamento do Xamarin Live (versão prévia)

> [!NOTE]
> A versão prévia da recarga do Xamarin Live terminou e queremos agradecer a todos os seus comentários e comentários. Leia nosso [roteiro](https://docs.microsoft.com/visualstudio/productinfo/vs-roadmap) para o Visual Studio 2019 para saber mais sobre os novos recursos de produtividade nos quais estamos trabalhando para o Xamarin. Forms. Essa extensão permanecerá disponível para o Visual Studio 2017, mas não receberá atualizações futuras.

O Xamarin Live reload permite que você **faça alterações no XAML e os veja refletido em tempo real, sem a necessidade de outra compilação e implantação**. Todas as alterações feitas em seu XAML serão reimplantadas no salvamento e refletidas em seu destino de implantação.

## <a name="requirements"></a>Requisitos do

* [Visual Studio 2017 versão 15,7 ou superior](https://visualstudio.microsoft.com/vs/) com o **desenvolvimento móvel com** carga de trabalho do .net.
* [Xamarin. Forms 3.0.0 ou superior](https://www.nuget.org/packages/Xamarin.Forms/).

## <a name="getting-started"></a>Guia de Introdução
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1. Instale o Xamarin Live recarregar do Visual Studio Marketplace

A recarga ao vivo do Xamarin é distribuída por meio do Visual Studio Marketplace. Para instalar a extensão, visite a [página de recarregamento do Xamarin Live no site Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload) e clique em **baixar**.

Abra o. vsix baixado e clique em **instalar**.

![Confirmação de recarga ao vivo do instalador do Visual Studio Installer](images/LiveReloadVSIXInstall.png)

Como alternativa, você pode procurá-lo na guia **online** da caixa de diálogo **extensões e atualizações** dentro do Visual Studio.

### <a name="2-configure-your-app-to-use-live-reload"></a>2. configurar seu aplicativo para usar o recarregamento ao vivo

Adicionar a recarga ao vivo a aplicativos móveis existentes pode ser feito em três etapas:

1. Verifique se todos os projetos estão atualizados para usar [Xamarin. Forms 3.0.0 ou superior](https://www.nuget.org/packages/Xamarin.Forms/) ou superior.

2. Adicione o pacote NuGet do **Xamarin. LiveReload** :

    a. **.Net Standard** – instale o NuGet do **Xamarin. LiveReload** em sua biblioteca do .net Standard 2,0. Isso não precisa ser instalado em seus projetos de plataforma. Verifique se a **origem do pacote** está definida como **todos**.
    
    b. **Projetos compartilhados** – instale o NuGet do **Xamarin. LiveReload** em todos os projetos de plataforma (como Android, Ios, UWP, etc.). Verifique se a **origem do pacote** está definida como **todos**.

    [![adicionar o Xamarin Live recarregar o NuGet com o Gerenciador de pacotes NuGet](images/addlivereloadnuget.w157-sml.png)](images/addlivereloadnuget.w157.png#lightbox)

3. Adicione `LiveReload.Init();` ao Construtor na classe `Application`, conforme mostrado no seguinte trecho de código:

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

### <a name="3-start-live-reloading"></a>3. iniciar o recarregamento ao vivo

Compile e implante seu aplicativo. Depois que o aplicativo for implantado, abra um arquivo XAML, faça algumas alterações e salve o arquivo. Suas alterações são reimplantadas no destino de implantação.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

O recarregamento ao vivo funciona com alterações em qualquer arquivo XAML. Alterações C# ou adição/remoção de pacotes NuGet requer uma nova compilação e implantação para entrar em vigor.

## <a name="frequently-asked-questions"></a>Perguntas frequentes 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>A recarga do Xamarin Live está disponível no Visual Studio para Mac? 

Não, a versão de visualização do Xamarin Live reload só está disponível para o Visual Studio 2017.

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>Isso funciona com todas as bibliotecas, como o Prism? 

Como seu aplicativo é compilado, o recarregamento ao vivo funciona com todas as bibliotecas, como Prism e bibliotecas de controle de terceiros, como Telerik, Infragistics, Syncfusion, ArcGIS, GrapeCity e outros fornecedores de controle.

### <a name="what-changes-does-live-reload-redeploy"></a>Quais alterações o reimplanta ao vivo? 

O recarregamento ao vivo aplica apenas as alterações feitas em XAML ou CSS. Se você fizer alterações em um C# arquivo, uma recompilação será necessária. 

### <a name="what-platforms-are-supported"></a>Quais plataformas têm suporte? 

O recarregamento ao vivo funciona em qualquer plataforma com suporte do Xamarin. Forms, incluindo Android, iOS e UWP.

### <a name="does-this-work-on-emulators-simulators-and-physical-devices"></a>Isso funciona em emuladores, simuladores e dispositivos físicos? 

Sim, a recarga ao vivo funciona com todos os destinos de implantação válidos, incluindo emuladores do Android, simuladores do iOS e dispositivos físicos. A implantação em um dispositivo requer que o dispositivo e o computador estejam na mesma rede Wi-Fi.

### <a name="does-this-work-with-corporate-networks"></a>Isso funciona com redes corporativas?

Se você estiver Depurando em um emulador do Android ou no simulador do iOS, a recarga ao vivo usará localhost para se comunicar. Se você deseja implantar em um dispositivo, o dispositivo e o computador precisam estar na mesma rede Wi-Fi. Em cenários em que isso não é possível, você pode [configurar seu próprio servidor de recarregamento dinâmico](#live-reload-server), o que permitirá que você recarregue ao vivo, independentemente das configurações de conectividade de rede.

### <a name="does-it-require-debugging-the-app"></a>Ele requer a depuração do aplicativo? 

No. Na verdade, você pode até mesmo iniciar todos os seus destinos de aplicativo com suporte (Android, iOS e UWP) em qualquer número de dispositivos ou simuladores/emuladores e ver todas as atualizações ao mesmo tempo. 

## <a name="limitations"></a>Limitações

* Há suporte apenas para o recarregamento de XAML.
* O estado da interface do usuário não pode ser mantido entre reimplantações, a menos que o MVVM seja usado.

## <a name="known-issues"></a>Problemas Conhecidos

* Somente com suporte no Visual Studio.
* A vinculação deve ser definida para **não vincular** ou **vincular somente SDKs de estrutura** 
* Recarregar recursos de todo o aplicativo (ou seja, **app. XAML** ou dicionários de recursos compartilhados), a navegação do aplicativo é redefinida. 
* O recarregamento de ContentView atualmente requer o recarregamento da página recipiente.
* Os elementos que contêm AutomationId podem causar uma falha de recarregamento.
* Editar XAML durante a depuração UWP pode causar uma falha de tempo de execução. Solução alternativa: use **Iniciar sem depuração (Ctrl + F5)** em vez de **Iniciar Depuração (F5)** .

## <a name="troubleshooting"></a>Solução de problemas

### <a name="error-codes"></a>Códigos de erro

* **XLR001**: *o projeto atual referencia ' Xamarin. LiveReload ' versão do pacote NUGET ' [Version] ', mas a extensão de recarregamento do Xamarin Live requer a versão ' [Version] '.*

  Para permitir a iteração rápida e a evolução do recurso de recarregamento ao vivo, o pacote NuGet e a extensão do Visual Studio devem corresponder exatamente. Atualize seu pacote NuGet para a mesma versão da extensão que você instalou.

* **XLR002**: *o recarregamento ao vivo requer pelo menos a propriedade ' MqttHostname ' ao criar a partir da linha de comando. Como alternativa, defina ' EnableLiveReload ' como ' false ' para desabilitar o recurso.*

  As propriedades exigidas pela recarga ao vivo não estão disponíveis durante a criação a partir da linha de comando (ou na integração contínua) e, portanto, devem ser fornecidas explicitamente. 

* **XLR003**: *o pacote NuGet de recarregamento ao vivo requer a instalação da extensão do Visual Studio para recarregar o Xamarin Live.*

  Tentativa de criar um projeto que referencia o pacote NuGet de recarregamento ao vivo, mas a extensão Visual não está instalada.  

* *Exceção ao carregar assemblies: System. IO. FileNotFoundException: não foi possível carregar o assembly ' Xamarin. Live. recarregar, Version = 0.3.27.0, Culture = neutral, PublicKeyToken = '.*

  O projeto host deve estar usando `PackageReference` em vez de `packages.config`

### <a name="app-doesnt-connect"></a>O aplicativo não se conecta

Quando o aplicativo é criado, as informações de **ferramentas > opções > Xamarin > recarregamento ao vivo** (nome do host, chaves de criptografia e porta) são incorporadas ao aplicativo, de modo que quando `LiveReload.Init()` é executado, nenhum emparelhamento ou configuração é necessário para que a conexão seja realizada com sucesso.

Além de problemas normais de rede (firewall, dispositivo em uma rede diferente), o principal motivo pelo qual o aplicativo pode não conectar com êxito o IDE é porque sua configuração difere daquela do Visual Studio. Isso pode acontecer se:

* O aplicativo foi compilado em um computador diferente.
* O aplicativo foi compilado e implantado em uma sessão diferente do Visual Studio, e a **geração automática de chaves de criptografia** está marcada (o padrão) em **ferramentas > opções > Xamarin > recarregar ao vivo**.
* As configurações do Visual Studio foram alteradas (ou seja, nome do host, porta ou chaves de criptografia), mas o aplicativo não foi compilado e implantado novamente.

Esses casos são resolvidos criando e implantando o aplicativo novamente.

### <a name="uninstalling-preview-1"></a>Desinstalando a visualização 1

Se você tiver uma versão prévia mais antiga e tiver problemas para desinstalá-lo, siga estas etapas:

1. Exclua a pasta **C:\Program Files (x86) \Microsoft Visual Studio\Preview\Enterprise\Common7\IDE\Extensions\Xamarin\LiveReload** (Observação: substitua "Enterprise" pela edição instalada e "Preview" por "2017" se você instalou em um vs estável)
2. Abra um **prompt de comando do desenvolvedor** para o Visual Studio e execute `devenv /updateconfiguration`. 

## <a name="tips--tricks"></a>Dicas e truques

* Desde que as configurações de recarga ao vivo não sejam alteradas (incluindo as chaves de criptografia, como se você desativar a **geração automática de chaves de criptografia**) e criar a partir do mesmo computador, não será necessário compilar e implantar o aplicativo após a implantação inicial, a menos que você altere o código ou as dependências. Você pode simplesmente iniciar um aplicativo implantado anteriormente e ele se conectará ao último host usado.

* Não há nenhuma limitação de quantos dispositivos você pode conectar à mesma sessão do Visual Studio. Você pode implantar e iniciar o aplicativo em quantos dispositivos/simuladores forem necessários para ver o recarregamento ao vivo trabalhando em todos eles ao mesmo tempo.

* O recarregamento ao vivo recarregará apenas a parte da interface do usuário do seu aplicativo, mas *não* recriará suas páginas, nem substituirá seu modelo de exibição (ou contexto de associação). Isso significa que *todo* o estado do aplicativo é sempre preservado entre recargas, incluindo suas dependências injetadas.

## <a name="live-reload-server"></a>Servidor de recarregamento dinâmico

Em cenários em que uma conexão do aplicativo em execução para seu computador (como indicado usando `localhost` ou `127.0.0.1` em **ferramentas > opções > Xamarin > Live recarregar**) não é possível (ou seja, firewalls, redes diferentes), você pode configurar um servidor remoto em vez disso, o IDE e o aplicativo serão conectardos.

O recarregamento ao vivo usa o [protocolo MQTT](https://mqtt.org/) padrão para trocar mensagens e, portanto, pode se comunicar com [servidores](https://github.com/mqtt/mqtt.github.io/wiki/servers)de terceiros. Há até mesmo [servidores públicos](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers) (também conhecidos como *agentes*) disponíveis que você pode usar. O recarregamento ao vivo foi testado com `broker.hivemq.com` e `iot.eclipse.org` nomes de host, bem como os serviços fornecidos por [www.cloudmqtt.com](https://www.cloudmqtt.com) e [www.cloudamqp.com](https://www.cloudamqp.com). Você também pode implantar seu próprio servidor MQTT na nuvem, como [HiveMQ no Azure](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud).

Você pode configurar qualquer porta, mas é comum usar a porta 1883 padrão para servidores remotos. As mensagens de recarga ao vivo usam uma forte criptografia simétrica AES de ponta a ponta, portanto, é seguro conectar-se a servidores remotos. Por padrão, a chave de criptografia e o vetor de inicialização (IV) são regenerados em todas as sessões do Visual Studio.

Provavelmente, a maneira mais fácil é instalar o servidor [mosquitto](https://mosquitto.org) em uma VM Ubuntu em branco no Azure:

1. Criar uma nova VM do servidor Ubuntu no portal do Azure
2. Adicione uma nova regra de porta de entrada para 1883 (porta MQTT padrão) na guia rede
3. Abrir o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) (modo bash)
4. Digite `ssh [USERNAME]@[PUBLIC_IP]` usando o nome de usuário que você escolheu em 1) e o IP público mostrado na página de visão geral da VM
5. Execute `sudo apt-get install mosquitto`, inserindo a senha que você escolheu em 1)

Agora você pode usar esse IP para se conectar ao seu próprio servidor MQTT.
