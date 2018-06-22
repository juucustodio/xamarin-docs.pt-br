---
title: Associação de um. JAR
description: Este passo a passo fornece instruções passo a passo para criar uma biblioteca de associações de Java do xamarin de um Android. Arquivo JAR.
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/11/2018
ms.openlocfilehash: 2d9f2198dbb88e7614944ac73729a4e6eca42647
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798373"
---
# <a name="binding-a-jar"></a>Associação de um. JAR

_Este passo a passo fornece instruções passo a passo para criar uma biblioteca de associações de Java do xamarin de um Android. Arquivo JAR._

## <a name="overview"></a>Visão geral

Comunidade do Android oferece muitas bibliotecas de Java que você talvez queira usar em seu aplicativo. Essas bibliotecas Java geralmente são empacotadas em. Formato JAR (Java Archive), mas você pode empacotar um. JÁ-lo em uma *biblioteca de associações de Java* para que sua funcionalidade está disponível para aplicativos xamarin. A finalidade da biblioteca de associações de Java é fazer as APIs na. Arquivo JAR disponível para o código c# por meio de wrappers de código gerado automaticamente.

Ferramentas Xamarin podem gerar uma biblioteca de associações de entrada de um ou mais. Arquivos JAR. A biblioteca de associações (. Assembly DLL) contém o seguinte: 

-   O conteúdo original. Arquivos JAR.

-   Gerenciado Callable Wrappers (MCW), que são c# tipos esse contorno de tipos do Java correspondente dentro do. Arquivos JAR.

O código MCW gerado usa JNI (Java nativo Interface) para encaminhar as chamadas de API para subjacente. Arquivo JAR. Você pode criar bibliotecas de associações para qualquer. Arquivo JAR originalmente planejado para ser usado com Android (Observe que as ferramentas de Xamarin atualmente não dá suporte a associação de bibliotecas Java não Android). Você também pode optar por criar a biblioteca de associações sem incluir o conteúdo da. Arquivo JAR para que a DLL tem uma dependência da. JAR em tempo de execução.

Neste guia, forneceremos Noções básicas de criação de uma biblioteca de ligações para um único. Arquivo JAR. Isso será mostrado com um exemplo onde tudo o que vai direito &ndash; ou seja, em que nenhuma personalização ou depuração de associações é necessário. 
[Criando associações usando metadados](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) oferece um exemplo de um cenário mais avançado, onde o processo de associação não é totalmente automático e algum tempo de intervenção manual é necessário. Para obter uma visão geral de Java biblioteca associação em geral (com um exemplo de código básico), consulte [associação a uma biblioteca Java](~/android/platform/binding-java-library/index.md). 

 
## <a name="walkthrough"></a>Passo a passo

O seguinte explicação passo a passo, vamos criar uma biblioteca de associações para [Picasso](http://square.github.io/picasso/), um Android popular. JAR que fornece a imagem de carregamento e a funcionalidade de cache. Usaremos as etapas a seguir para associar **2.x.x.jar picasso** para criar um novo assembly .NET que podemos usar em um projeto de xamarin: 

1. Crie um novo projeto de biblioteca de associações de Java.

2. Adicionar o. Arquivo JAR para o projeto.

3. Definir a ação de compilação apropriado para o. Arquivo JAR.

4. Escolha uma estrutura de destino que o. Dá suporte a JAR.

5. Crie a biblioteca de associações.

Depois de criar a biblioteca de associações, podemos desenvolverá um pequeno aplicativo do Android que demonstra a nossa capacidade de chamar APIs na biblioteca de associações. Neste exemplo, queremos acessar métodos de **2.x.x.jar picasso**:

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

```csharp
After we generate a Bindings Library for **picasso-2.x.x.jar**,
we can call these methods from C#. For example:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```


### <a name="creating-the-bindings-library"></a>Criando a biblioteca de associações

Antes de começar as etapas abaixo, faça o download [2.x.x.jar picasso](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

Primeiro, crie um novo projeto de biblioteca de associações. No Visual Studio para Mac ou o Visual Studio, crie uma nova solução e selecione o *biblioteca associações Android* modelo. (As capturas de tela neste passo a passo usam o Visual Studio, mas o Visual Studio para Mac é muito semelhante). Nome da solução **JarBinding**: 

[![Criar projeto de biblioteca de JarBinding](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

O modelo inclui uma **Jars** pasta em que você adicionar seu. JAR(s) para o projeto de biblioteca de associações. Clique com botão direito do **Jars** pasta e selecione **Adicionar > Existing Item**: 

[![Adicionar item existente](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Navegue até o **picasso 2.x.x.jar** arquivos baixados anteriormente, selecione-o e clique em **adicionar**: 

[![Selecione o arquivo jar e clique em Adicionar](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Verifique o **2.x.x.jar picasso** arquivo foi adicionado com êxito ao projeto: 

[![JAR adicionado ao projeto](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

Quando você cria um projeto de biblioteca de associações de Java, você deve especificar se o. JAR será inserido na biblioteca de associações ou empacotado separadamente. Para fazer isso, você especificar um dos seguintes *ações de construção*: 

-   **EmbeddedJar** &ndash; o. JAR será inserido na biblioteca de associações.

-   **InputJar** &ndash; o. JAR será mantido separado da biblioteca de associações.

Normalmente, você usa o **EmbeddedJar** ação de compilação para que o. JAR é empacotado automaticamente para a biblioteca de associações. Essa é a opção mais simples &ndash; código de bytes Java em de. JAR é convertido em código de bytes Dex e incorporada (junto com o gerenciado Callable Wrappers) seu APK. Se você quiser manter o. JAR separado da biblioteca de associações, você pode usar o **InputJar** opção; no entanto, você deve garantir que o. Arquivo JAR está disponível no dispositivo que executa o aplicativo. 

Defina a ação de compilação para **EmbeddedJar**: 

[![Selecione a ação de compilação EmbeddedJar](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

Em seguida, abra o projeto de propriedades para configurar o *Framework de destino*. Se o. JAR usa APIs Android, defina a estrutura de destino para o nível de API que o. JAR espera. Normalmente, o desenvolvedor das. Arquivo JAR indicará qual nível de API (ou níveis) que o. JAR é compatível com. (Para obter mais informações sobre a configuração de estrutura de destino e os níveis de API do Android em geral, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).)

Definir o destino da API de nível para a biblioteca de associações (neste exemplo, estamos usando o nível de API 19): 

[![Nível de API de destino definido como 19 da API](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)


Finalmente, crie a biblioteca de associações. Embora algumas mensagens de aviso podem ser exibidas, o projeto de biblioteca de associações deve compilar com êxito e produzir uma saída. DLL no seguinte local: **JarBinding/bin/Debug/JarBinding.dll**
    


### <a name="using-the-bindings-library"></a>Usando a biblioteca de associações

Para consumir isso. DLL em seu aplicativo xamarin, faça o seguinte:

1.  Adicione uma referência à biblioteca de associações.

2.  Fazer chamadas para o. JAR por meio de encapsuladores que pode ser chamadas. 

Nas etapas a seguir, vamos criar um aplicativo mínimo que usa a biblioteca de associações para baixar e exibir uma imagem em um `ImageView`; o "trabalho pesado" é feito pelo código que reside na. Arquivo JAR. 

Primeiro, crie um novo aplicativo xamarin que consome a biblioteca de associações. A solução e selecione **adicionar novo projeto**; nome do novo projeto **BindingTest**. Estamos criando este aplicativo na mesma solução que a biblioteca de associações para simplificar este passo a passo; No entanto, o aplicativo que consome a biblioteca de associações em vez disso, pode residir em uma solução diferente: 

[![Adicionar novo projeto BindingTest](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

Com o botão direito do **referências** nó do **BindingTest** do projeto e selecione **adicionar referência...** :

[![Adicionar a referência à direita](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Verifique o **JarBinding** projeto criado anteriormente e clique em **Okey**:

[![Selecione o projeto JarBinding](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Abra o **referências** nó do **BindingTest** do projeto e verifique o **JarBinding** referência está presente: 

[![JarBinding aparece em referências](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

Modificar o **BindingTest** layout (**Main.axml**) para que ele tem um único `ImageView`:

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

Adicione o seguinte `using` instrução **MainActivity.cs** &ndash; isso torna possível acessar facilmente os métodos de Java baseado em `Picasso` classe reside na biblioteca de associações:

```csharp
using Com.Squareup.Picasso;
```

Modificar o `OnCreate` método assim que ele usa o `Picasso` classe para carregar uma imagem de uma URL e exibi-lo no `ImageView`: 

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

Compilar e executar o **BindingTest** projeto. O aplicativo será iniciado e, depois de um pequeno atraso (dependendo de condições de rede), ele deve baixar e exibir uma imagem semelhante à captura de tela a seguir:

[![Execução de captura de tela de BindingTest](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

Parabéns! Você já associada com êxito uma biblioteca de Java. JAR e usado em seu aplicativo xamarin.
 
 
## <a name="summary"></a>Resumo

Neste passo a passo, criamos uma biblioteca de associações de um terceiro. JAR do arquivo, adicionar a biblioteca de associações para um aplicativo de teste mínimo e, em seguida, executar o aplicativo para verificar que nosso código c# pode chamar código Java que residem em de. Arquivo JAR. 



## <a name="related-links"></a>Links relacionados

- [Criar uma biblioteca de associações de Java (vídeo)](https://university.xamarin.com/classes#10090)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
