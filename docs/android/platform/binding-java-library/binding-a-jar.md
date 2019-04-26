---
title: Associação de um .JAR
description: Este passo a passo fornece instruções passo a passo para criar uma biblioteca de associações de Java do xamarin. Android do Android. Arquivo JAR.
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: 3c84b29807fd4a181ed867095645005bf9ba4df0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60957222"
---
# <a name="binding-a-jar"></a>Associação de um .JAR

_Este passo a passo fornece instruções passo a passo para criar uma biblioteca de associações de Java do xamarin. Android do Android. Arquivo JAR._

## <a name="overview"></a>Visão geral

A comunidade de Android oferece muitas bibliotecas de Java que você talvez queira usar em seu aplicativo. Essas bibliotecas Java geralmente são empacotadas em. Formato JAR (Java Archive), mas você pode empacotar um. JÁ-lo em um *biblioteca de associações de Java* para que sua funcionalidade está disponível para aplicativos xamarin. Android. A finalidade da biblioteca de associações de Java é tornar as APIs na. Arquivo JAR disponíveis para C# código por meio de wrappers de código gerado automaticamente.

As ferramentas do Xamarin podem gerar uma biblioteca de associações de entrada de um ou mais. Arquivos JAR. A biblioteca de associações (. Assembly DLL) contém o seguinte: 

-   O conteúdo do original. Arquivos JAR.

-   Gerenciado Callable Wrappers (MCW), que são C# tipos que quebra automática de linha dentro de tipos do Java correspondente a. Arquivos JAR.

O código gerado do MCW usa JNI (Interface nativa do Java) para encaminhar as chamadas à API subjacente. Arquivo JAR. Você pode criar bibliotecas de associações para qualquer. Arquivo JAR que foi originalmente direcionado para ser usado com o Android (Observe que as ferramentas do Xamarin não oferece suporte a associação de bibliotecas de Java não Android). Você também pode optar por criar a biblioteca de associações sem incluir o conteúdo da. Arquivo JAR para que a DLL tem uma dependência na. JAR em tempo de execução.

Neste guia, vamos percorrer as Noções básicas de criação de uma biblioteca de associações para um único. Arquivo JAR. Isso será mostrado com um exemplo onde tudo der certo &ndash; ou seja, onde nenhuma personalização ou depuração de associações é necessária. 
[Criando associações usando metadados](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) oferece um exemplo de um cenário mais avançado, onde o processo de associação não é totalmente automático e alguma quantidade de intervenção manual é necessária. Para uma visão geral de Java biblioteca de associação em geral (com um exemplo de código básico), consulte [associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md). 

 
## <a name="walkthrough"></a>Passo a passo

A seguinte explicação passo a passo, vamos criar uma biblioteca de associações para [Picasso](http://square.github.io/picasso/), um Android popular. JAR que fornece o carregamento e a funcionalidade de cache de imagem. Usaremos as etapas a seguir para associar **2.x.x.jar picasso** para criar um novo assembly .NET que pode ser usada em um projeto xamarin. Android: 

1. Crie um novo projeto de biblioteca de associações de Java.

2. Adicionar o. Arquivo JAR para o projeto.

3. Defina a ação de compilação apropriado para o. Arquivo JAR.

4. Escolha uma estrutura de destino que o. Dá suporte a JAR.

5. Crie a biblioteca de associações.

Depois de criarmos a biblioteca de associações, vamos desenvolver um pequeno aplicativo do Android que demonstra a nossa capacidade de chamar APIs na biblioteca de associações. Neste exemplo, queremos que acessa os métodos de **picasso 2.x.x.jar**:

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

Depois que podemos gerar uma biblioteca de associações para **picasso 2.x.x.jar**, podemos chamar esses métodos de C#. Por exemplo:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```


### <a name="creating-the-bindings-library"></a>Criando a biblioteca de associações

Antes de começar as etapas abaixo, baixe [picasso 2.x.x.jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

Primeiro, crie um novo projeto de biblioteca de associações. No Visual Studio para Mac ou Visual Studio, crie uma nova solução e selecione o *biblioteca de associações do Android* modelo. (As capturas de tela neste passo a passo usam o Visual Studio, mas o Visual Studio para Mac é muito semelhante). Nomeie a solução **JarBinding**: 

[![Criar projeto de biblioteca JarBinding](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

O modelo inclui um **Jars** pasta em que você adiciona seu. JAR(s) ao projeto de biblioteca de associações. Clique com botão direito do **Jars** pasta e selecione **Adicionar > Item existente**: 

[![Adicionar item existente](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Navegue até a **picasso 2.x.x.jar** arquivo baixado anteriormente, selecione-o e clique em **Add**: 

[![Selecione o arquivo jar e clique em Adicionar](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Verifique se que o **picasso 2.x.x.jar** arquivo foi adicionado com êxito ao projeto: 

[![JAR adicionado ao projeto](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

Quando você cria um projeto de biblioteca de associações de Java, você deve especificar se o. JAR será inserido na biblioteca de associações ou empacotado separadamente. Para fazer isso, especifique um dos seguintes *ações de build*: 

-   **EmbeddedJar** &ndash; o. JAR será inserido na biblioteca de associações.

-   **InputJar** &ndash; o. JAR será mantida separada da biblioteca de associações.

Normalmente, você usa o **EmbeddedJar** ação de build para que o. JAR é empacotada automaticamente na biblioteca de associações. Essa é a opção mais simples &ndash; código de bytes Java na. JAR é convertido em código de bytes do Dex e é inserido (juntamente com o Managed Callable Wrappers) em seu APK. Se você quiser manter o. JAR separado da biblioteca de associações, você pode usar o **InputJar** opção; no entanto, você deve garantir que o. Arquivo JAR está disponível no dispositivo que executa seu aplicativo. 

Defina a ação de compilação para **EmbeddedJar**: 

[![Selecione a ação de compilação EmbeddedJar](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

Em seguida, abra o projeto de propriedades para configurar o *estrutura de destino*. Se o. JAR usa quaisquer APIs do Android, defina a estrutura de destino para o nível de API que o. JAR espera. Normalmente, o desenvolvedor da. Arquivo JAR indicará qual nível de API (ou níveis) que o. É compatível com o JAR. (Para obter mais informações sobre a configuração de estrutura de destino e os níveis de API do Android em geral, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).)

Definir o destino API nível para sua biblioteca de associações (neste exemplo, estamos usando API nível 19): 

[![Nível de API de destino definida como API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)


Por fim, compile a biblioteca de associações. Embora algumas mensagens de aviso podem ser exibidas, o projeto de biblioteca de associações deve compilar com êxito e produzir uma saída. DLL no seguinte local: **JarBinding/bin/Debug/JarBinding.dll**
    


### <a name="using-the-bindings-library"></a>Usando a biblioteca de associações

Para consumir isso. DLL em seu aplicativo xamarin. Android, faça o seguinte:

1.  Adicione uma referência à biblioteca de associações.

2.  Fazer chamadas para o. JAR por meio de Callable Wrappers gerenciados. 

As etapas a seguir, vamos criar um aplicativo mínimo que usa a biblioteca de associações para baixar e exibir uma imagem em um `ImageView`; o "trabalho pesado" é feito pelo código que reside na. Arquivo JAR. 

Primeiro, crie um novo aplicativo xamarin. Android que consome a biblioteca de associações. A solução com o botão direito e selecione **adicionar novo projeto**; Nomeie o novo projeto **BindingTest**. Estamos criando este aplicativo na mesma solução que a biblioteca de associações para simplificar este passo a passo; No entanto, o aplicativo que consome a biblioteca de associações poderia, em vez disso, residir em uma solução diferente: 

[![Adicionar novo projeto BindingTest](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

Com o botão direito do **referências** nó do **BindingTest** do projeto e selecione **adicionar referência...** :

[![Adicionar à direita de referência](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Verifique as **JarBinding** projeto criado anteriormente e clique em **Okey**:

[![Selecione o projeto JarBinding](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Abra o **referências** nó do **BindingTest** do projeto e verifique se que o **JarBinding** referência está presente: 

[![JarBinding aparece sob referências](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

Modificar a **BindingTest** layout (**Main. axml**) para que ele tenha um único `ImageView`:

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

Adicione o seguinte `using` instrução **MainActivity.cs** &ndash; isso torna possível acessar facilmente os métodos dos baseado em Java `Picasso` classe reside na biblioteca de associações:

```csharp
using Com.Squareup.Picasso;
```

Modificar a `OnCreate` , de modo que ele usa o `Picasso` classe para carregar uma imagem de uma URL e exibi-lo no `ImageView`: 

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

Compilar e executar o **BindingTest** projeto. O aplicativo será iniciado e, após um pequeno atraso (dependendo de condições de rede), ele deve baixar e exibir uma imagem semelhante à seguinte captura de tela:

[![Execução de captura de tela de BindingTest](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

Parabéns! Uma biblioteca Java foi associada já com êxito. JAR e usado em seu aplicativo xamarin. Android.
 
 
## <a name="summary"></a>Resumo

Neste passo a passo, criamos uma biblioteca de associações para um terceiro. JAR do arquivo, adicionou a biblioteca de associações para um aplicativo de teste mínima e executou o aplicativo para verificar que nosso C# código pode chamar o código Java que residem na. Arquivo JAR. 



## <a name="related-links"></a>Links relacionados

- [Compilando uma biblioteca de associações de Java (vídeo)](https://university.xamarin.com/classes#10090)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
