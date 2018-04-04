---
title: Guia de Introdução ao macOS
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: c129079aad14ac9e8aad6f73670ce9a43a36f222
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started-with-macos"></a>Guia de Introdução ao macOS


## <a name="what-you-will-need"></a>Você precisará

* Siga as instruções em nosso [guia de Introdução ao Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) guia.

* Um assembly do .NET para usar com **Embeddinator 4000**.

* Um aplicativo Cocoa macOS

Continuar depois que você tiver seguido as instruções em nosso [guia de Introdução ao Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) guia. Se você já tiver um assembly .NET que você pode ignorar diretamente em **Embeddinator-4000 usando** seção.

## <a name="creating-a-net-assembly"></a>Criação de um assembly .NET

Para criar um assembly .NET que você precisará abrir [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac/) e criar um novo **projeto de biblioteca do .NET** fazendo *arquivo > nova solução > outros > .NET > biblioteca*. Clique em Avançar e dê *clima* como o *nome do projeto*e clique em *criar*.

Siga as próximas etapas:

1. Excluir o **MyClass.cs** arquivo e o **propriedades** pasta.

2. Clique com o botão direito em *projeto clima > Adicionar > novo arquivo.*

3. Selecione *classe vazia* e usar **XAMWeatherFetcher** como o nome, em seguida, clique em novo.

4. Substitua o conteúdo do *XAMWeatherFetcher.cs* com o código a seguir:

```csharp
using System;
using System.Json;
using System.Net;

public class XAMWeatherFetcher {

    static string urlTemplate = @"https://query.yahooapis.com/v1/public/yql?q=select%20item.condition%20from%20weather.forecast%20where%20woeid%20in%20(select%20woeid%20from%20geo.places(1)%20where%20text%3D%22{0}%2C%20{1}%22)&format=json&env=store%3A%2F%2Fdatatables.org%2Falltableswithkeys";
    public string City { get; private set; }
    public string State { get; private set; }

    public XAMWeatherFetcher (string city, string state)
    {
        City = city;
        State = state;
    }

    public XAMWeatherResult GetWeather ()
    {
        try {
            using (var wc = new WebClient ()) {
                var url = string.Format (urlTemplate, City, State);
                var str = wc.DownloadString (url);
                var json = JsonValue.Parse (str)["query"]["results"]["channel"]["item"]["condition"];
                var result = new XAMWeatherResult (json["temp"], json["text"]);
                return result;
            }
        }
        catch (Exception ex) {
            // Log some of the exception messages
            Console.WriteLine (ex.Message);
            Console.WriteLine (ex.InnerException?.Message);
            Console.WriteLine (ex.InnerException?.InnerException?.Message);

            return null;
        }

    }
}

public class XAMWeatherResult {
    public string Temp { get; private set; }
    public string Text { get; private set; }

    public XAMWeatherResult (string temp, string text)
    {
        Temp = temp;
        Text = text;
    }
}
```

Você observará que `Using System.Json;` apresentará um erro; para corrigir isso, precisamos fazer o seguinte:

1. Clique duas vezes no **referências** pasta.

2. Clique no **pacotes** guia.

3. Verificar **System.Json**.

4. Click **Ok**.

Feito acima tudo que precisamos fazer é compilação nosso assembly .NET clicando em *menu Build > compilar tudo* ou ⌘ + b. Compilação bem-sucedida mensagem deverá aparecer na barra de status superior.

Agora clique com botão direito em *clima* nó do projeto e selecione *Revelar no localizador*. No localizador, vá para o *bin/Debug* dentro de pasta; ele encontrará **Weather.dll.**

## <a name="using-embeddinator-4000"></a>Usando Embeddinator 4000

Se você instalou usando nosso instalador pkg Embeddinator-4000 e iniciar uma nova sessão de terminal após a instalação, você deve ser capaz de usar o **objcgen** comando (caso contrário, você pode usar o caminho absoluto: `/Library/Frameworks/Xamarin.Embeddinator-4000.framework/Commands/objcgen`); **objcgen** é a ferramenta que precisamos para gerar uma biblioteca nativa de um assembly .NET.

Abra terminal, `cd` para a pasta que contém Weather.dll e execute **objcgen** com os argumentos mostrados abaixo:

```shell
cd /Users/Alex/Projects/Weather/Weather/bin/Debug

objcgen --debug --outdir=output -c Weather.dll
```

Tudo o que você precisa serão colocados no **saída** diretório lado *Weather.dll*. Se você tiver seu próprio assembly .NET, substitua *Weather.dll* com ele e siga as mesmas etapas acima.

## <a name="using-the-generated-output-in-an-xcode-project"></a>Usando a saída gerada em um projeto no Xcode

Abra o Xcode e criar um novo **macOS aplicativo Cocoa** e nomeie-o **MyWeather**. Clique com o botão direito no *nó do projeto MyWeather*, selecione *adicionar arquivos para "MyWeather"*, navegue até o **saída** diretório criado por *Embeddinator 4000* e adicione os seguintes arquivos:

* bindings.h
* embeddinator.h
* glib.h
* mono-support.h
* mono_embeddinator.h
* objc-support.h
* libWeather.dylib
* Weather.dll

Certifique-se de **copiar itens se necessário** é verificada no painel de opções da caixa de diálogo de arquivo.

Agora, precisamos garantir que **libWeather.dylib** e **Weather.dll** invadir o pacote de aplicativos:

* Clique em *MyWeather o nó do projeto*.
* Selecione *fases de compilação* guia.
* Adicionar um novo *fase de cópia de arquivos*.
* Em *destino* selecione **estruturas** e adicione **libWeather.dylib**.
* Adicionar um novo *fase de cópia de arquivos*.
* Em *destino* selecione **executáveis**, adicionar **Weather.dll** e certifique-se de *código logon cópia* é verificada.

Agora, abra **ViewController.m** e substitua o seu conteúdo com:

```objective-c
#import "ViewController.h"
#import "bindings.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    XAMWeatherFetcher * fetcher = [[XAMWeatherFetcher alloc] initWithCity:@"Boston" state:@"MA"];
    XAMWeatherResult * weather = [fetcher getWeather];

    NSString * result;
    if (weather)
        result = [NSString stringWithFormat:@"%@ °F - %@", weather.temp, weather.text];
    else
        result = @"An error occured";

    NSTextField * textField = [NSTextField labelWithString:result];
    [self.view addSubview:textField];
}

@end
```

Por fim, execute o projeto Xcode e semelhante a esta será exibida:

![Execução de exemplo MyWeather](macos-images/weather-from-csharp-macos.png)

Está disponível um exemplo mais completo e visualmente melhores [aqui](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather).
