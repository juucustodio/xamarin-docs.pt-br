---
title: Associação de um .JAR
description: Este passo a passo fornece instruções passo a passo para criar uma biblioteca de associações Java do Xamarin. Android a partir de um Android. Arquivo JAR.
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: 6aa9367495ba00138a38816ffab51c1ab43eec94
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524811"
---
# <a name="binding-a-jar"></a>Associação de um .JAR

_Este passo a passo fornece instruções passo a passo para criar uma biblioteca de associações Java do Xamarin. Android a partir de um Android. Arquivo JAR._

## <a name="overview"></a>Visão geral

A Comunidade do Android oferece muitas bibliotecas Java que você talvez queira usar em seu aplicativo. Essas bibliotecas Java geralmente são empacotadas no. Formato JAR (arquivo Java), mas você pode empacotar um. JAR-o em uma *biblioteca de associações Java* para que sua funcionalidade esteja disponível para aplicativos Xamarin. Android. A finalidade da biblioteca de associações Java é fazer as APIs no. Arquivo JAR disponível para C# codificar por meio de wrappers de código gerados automaticamente.

As ferramentas do Xamarin podem gerar uma biblioteca de associações de uma ou mais entradas. Arquivos JAR. A biblioteca de associações (. Assembly de DLL) contém o seguinte: 

- O conteúdo do original. Arquivo (s) JAR.

- O MCW (wrappers callable) gerenciados, C# que são tipos que encapsulam os tipos Java correspondentes dentro do. Arquivo (s) JAR.

O código da MCW gerado usa JNI (Java Native interface) para encaminhar suas chamadas à API para o subjacente. Arquivo JAR. Você pode criar bibliotecas de associações para qualquer um. Arquivo JAR que foi originalmente direcionado para ser usado com Android (Observe que as ferramentas do Xamarin atualmente não dão suporte à associação de bibliotecas Java não Android). Você também pode optar por criar a biblioteca de associações sem incluir o conteúdo do. Arquivo JAR para que a DLL tenha uma dependência no. JAR em tempo de execução.

Neste guia, vamos percorrer as noções básicas de criação de uma biblioteca de associações para um único. Arquivo JAR. Ilustraremos com um exemplo onde tudo está certo &ndash; , onde não é necessária nenhuma personalização ou depuração de associações. 
A [criação de associações usando metadados](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) oferece um exemplo de um cenário mais avançado em que o processo de associação não é totalmente automático e alguma quantidade de intervenção manual é necessária. Para obter uma visão geral da Associação de biblioteca Java em geral (com um exemplo de código básico), consulte [associando uma biblioteca Java](~/android/platform/binding-java-library/index.md). 

 
## <a name="walkthrough"></a>Passo a passo

Nas instruções a seguir, criaremos uma biblioteca de associações para [Picasso](http://square.github.io/picasso/), um Android popular. JAR que fornece o carregamento de imagens e a funcionalidade de cache. Usaremos as etapas a seguir para associar **Picasso-2. x. x. jar** para criar um novo assembly .NET que possamos usar em um projeto Xamarin. Android: 

1. Crie um novo projeto de biblioteca de associações Java.

2. Adicione o. Arquivo JAR para o projeto.

3. Defina a ação de compilação apropriada para o. Arquivo JAR.

4. Escolha uma estrutura de destino que o. Suporte a JAR.

5. Crie a biblioteca de associações.

Depois de criarmos a biblioteca de associações, desenvolveremos um pequeno aplicativo Android que demonstra nossa capacidade de chamar APIs na biblioteca de associações. Neste exemplo, queremos acessar os métodos de **Picasso-2. x. x. jar**:

```java
package com.squareup.picasso

public class Picasso
{ 
    ...
    public static Picasso with (Context context) { ... };
    ...
    public RequestCreator load (String path) { ... };
    ...
}
```

Depois de gerarmos uma biblioteca de associações para **Picasso-2. x. x. jar**, podemos chamar esses métodos de C#. Por exemplo:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```


### <a name="creating-the-bindings-library"></a>Criando a biblioteca de associações

Antes de começar com as etapas abaixo, baixe [Picasso-2. x. x. jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

Primeiro, crie um novo projeto de biblioteca de associações. No Visual Studio para Mac ou no Visual Studio, crie uma nova solução e selecione o modelo de *biblioteca de associações do Android* . (As capturas de tela neste passo a passos usam o Visual Studio, mas Visual Studio para Mac é muito semelhante.) Nomeie a solução **JarBinding**: 

[![Criar projeto de biblioteca JarBinding](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

O modelo inclui uma pasta **jars** na qual você adiciona o. JAR (s) para o projeto de biblioteca de associações. Clique com o botão direito do mouse na pasta **jars** e selecione **Adicionar > item existente**: 

[![Adicionar item existente](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Navegue até o arquivo **Picasso-2. x. x. jar** baixado anteriormente, selecione-o e clique em **Adicionar**: 

[![Selecione arquivo JAR e clique em Adicionar](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Verifique se o arquivo **Picasso-2. x. x. jar** foi adicionado com êxito ao projeto: 

[![Jar adicionado ao projeto](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

Ao criar um projeto de biblioteca de associações Java, você deve especificar se o. O JAR deve ser inserido na biblioteca de associações ou empacotado separadamente. Para fazer isso, especifique uma das seguintes ações de *compilação*: 

- **EmbeddedJar** &ndash; o. O JAR será inserido na biblioteca de associações.

- **InputJar** &ndash; o. O JAR será mantido separado da biblioteca de associações.

Normalmente, você usa a ação de Build **EmbeddedJar** para que o. O JAR é empacotado automaticamente na biblioteca de associações. Essa é a opção &ndash; mais simples de código de bytes Java no. O JAR é convertido em código de bytes Dex e é inserido (juntamente com os wrappers callable gerenciados) em seu APK. Se você quiser manter o. JAR separado da biblioteca de associações, você pode usar a opção **InputJar** ; no entanto, você deve garantir que o. O arquivo JAR está disponível no dispositivo que executa o aplicativo. 

Defina a ação de compilação como **EmbeddedJar**: 

[![Selecionar ação de Build do EmbeddedJar](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

Em seguida, abra as propriedades do projeto para configurar a *estrutura de destino*. Se o. O JAR usa qualquer API do Android, define a estrutura de destino para o nível de APIs que o. O JAR espera. Normalmente, o desenvolvedor do. O arquivo JAR indicará o nível de API (ou níveis) que o. JAR é compatível com. (Para obter mais informações sobre a configuração da estrutura de destino e os níveis de API do Android em geral, consulte [noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).)

Defina o nível da API de destino para sua biblioteca de associações (neste exemplo, estamos usando o nível de API 19): 

[![Nível de API de destino definido como API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)


Por fim, crie a biblioteca de associações. Embora algumas mensagens de aviso possam ser exibidas, o projeto de biblioteca de associações deve ser compilado com êxito e produzir uma saída. DLL no seguinte local: **JarBinding/bin/Debug/JarBinding.dll**
    


### <a name="using-the-bindings-library"></a>Usando a biblioteca de associações

Para consumir isso. Na DLL do aplicativo Xamarin. Android, faça o seguinte:

1. Adicione uma referência à biblioteca de associações.

2. Faça chamadas no. JAR por meio dos wrappers callable gerenciados. 

Nas etapas a seguir, criaremos um aplicativo mínimo que usa a biblioteca de associações para baixar e exibir uma imagem em um `ImageView`; o "trabalho pesado" é feito pelo código que reside no. Arquivo JAR. 

Primeiro, crie um novo aplicativo Xamarin. Android que consome a biblioteca de associações. Clique com o botão direito do mouse na solução e selecione **Adicionar novo projeto**; Nomeie o novo projeto **BindingTest**. Estamos criando esse aplicativo na mesma solução que a biblioteca de associações para simplificar este passo a passos; no entanto, o aplicativo que consome a biblioteca de associações poderia, em vez disso, residir em uma solução diferente: 

[![Adicionar novo projeto BindingTest](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

Clique com o botão direito do mouse no nó **referências** do projeto **BindingTest** e selecione **Adicionar referência...** :

[![Adicionar referência à direita](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Verifique o projeto **JarBinding** criado anteriormente e clique em **OK**:

[![Selecione o projeto JarBinding](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Abra o nó **referências** do projeto **BindingTest** e verifique se a referência **JarBinding** está presente: 

[![JarBinding aparece em referências](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

Modifique o layout **BindingTest** (**Main. axml**) para que ele tenha um único `ImageView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <ImageView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/imageView" />
</LinearLayout>
```

Adicione a instrução `using` a seguir para **MainActivity.cs** &ndash; isso torna possível acessar facilmente os métodos da classe baseada `Picasso` em Java que reside na biblioteca de associações:

```csharp
using Com.Squareup.Picasso;
```

Modifique o `OnCreate` método para que ele use a `Picasso` classe para carregar uma imagem de uma URL `ImageView`e exibi-la no: 

```csharp
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);
        ImageView imageView = FindViewById<ImageView>(Resource.Id.imageView);

        // Use the Picasso jar library to load and display this image:
        Picasso.With (this)
            .Load ("http://i.imgur.com/DvpvklR.jpg")
            .Into (imageView);
    }
}
```

Compile e execute o projeto **BindingTest** . O aplicativo será inicializado e, após um pequeno atraso (dependendo das condições de rede), ele deverá baixar e exibir uma imagem semelhante à captura de tela a seguir:

[![Captura de tela de BindingTest em execução](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

Parabéns! Você uma biblioteca Java com êxito. JAR e o utilizou em seu aplicativo Xamarin. Android.
 
 
## <a name="summary"></a>Resumo

Neste tutorial, criamos uma biblioteca de associações para terceiros. Arquivo JAR, adicionado a biblioteca de associações a um aplicativo de teste mínimo e, em seguida, executou o aplicativo C# para verificar se nosso código pode chamar o código Java que reside no. Arquivo JAR. 



## <a name="related-links"></a>Links relacionados

- [Criando uma biblioteca de associações Java (vídeo)](https://university.xamarin.com/classes#10090)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
