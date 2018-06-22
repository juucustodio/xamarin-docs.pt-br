---
title: Recursos de KitKat
description: Android 4.4 (KitKat) é carregado com uma infinidade de recursos para desenvolvedores e usuários. Este guia destaca vários desses recursos e fornece exemplos de código e os detalhes de implementação para ajudá-lo a tirar o máximo proveito KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 3c3eafc8dc18113080dd6c906025556292c43e1c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773732"
---
# <a name="kitkat-features"></a>Recursos de KitKat

_Android 4.4 (KitKat) é carregado com uma infinidade de recursos para desenvolvedores e usuários. Este guia destaca vários desses recursos e fornece exemplos de código e os detalhes de implementação para ajudá-lo a tirar o máximo proveito KitKat._

## <a name="overview"></a>Visão geral

Android 4.4 (API nível 19), também conhecido como "KitKat", foi liberado no final de 2013. KitKat oferece uma variedade de novos recursos e melhorias, incluindo:

-  [Experiência do usuário](#user_experience) &ndash; animações fácil com o framework de transição, transparente barras de status e de navegação e modo de tela inteira envolvente ajuda a criar uma melhor experiência do usuário.

-  [Conteúdo do usuário](#user_content) &ndash; gerenciamento de arquivos do usuário simplificada com o framework de acesso de armazenamento; imprimir imagens, sites da web e outros tipos de conteúdo é mais fácil com APIs de impressão aprimorado.

-  [Hardware](#hardware) &ndash; transformar qualquer aplicativo em um cartão de NFC com NFC baseado em Host cartão emulação; executar sensores de baixa potência com o `SensorManager` .

-  [Ferramentas de desenvolvedor](#developer_tools) &ndash; aplicativos Screencast em ação com o cliente de ponte de depuração do Android, disponível como parte do SDK do Android.


Este guia fornece instruções sobre como migrar um aplicativo xamarin existente KitKat, bem como uma visão geral de KitKat para desenvolvedores do xamarin.

## <a name="requirements"></a>Requisitos

Para desenvolver aplicativos xamarin usando KitKat, você precisa *xamarin 4.11.0* ou superior e Android 4.4 (API nível 19) instalado pelo Gerenciador de SDK do Android, conforme ilustrado na captura de tela a seguir:

[![Selecionando o Android 4.4 no Gerenciador de SDK do Android](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Migrar seu aplicativo para KitKat

Esta seção fornece alguns itens da primeira resposta para ajudar a transição de aplicativos existentes para o Android 4.4.

### <a name="check-system-version"></a>Verifique a versão do sistema

Se um aplicativo deve ser compatível com versões anteriores do Android, certifique-se de incluir qualquer código específico do KitKat em uma verificação de versão do sistema, conforme ilustrado pelo exemplo de código abaixo:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>Envio em lote do alarme

Android usa serviços de alarme para ativar um aplicativo em segundo plano em um horário especificado. KitKat isso leva um passo adiante processando em lotes alarmes para preservar a energia. Isso significa que, em vez de ativação de cada aplicativo em um horário específico, KitKat prefere agrupar vários aplicativos que são registrados para ativar durante o mesmo intervalo de tempo e ativá-los ao mesmo tempo.
Para informar ao Android para ativar um aplicativo durante um intervalo de tempo especificado, chame `SetWindow` no [ `AlarmManager` ](https://developer.xamarin.com/api/type/Android.App.AlarmManager/), passando o mínimo e o tempo máximo, em milissegundos, que pode decorrer antes do aplicativo é ativado e a operação a ser executada na ativação.
O código a seguir fornece um exemplo de um aplicativo que precisa ser ativado entre meia hora e uma hora do momento em que a janela é definida:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Para continuar a ativação de um aplicativo em um horário específico, use `SetExact`, passando a hora exata em que o aplicativo deve ser ativado e a operação a ser executada:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat não permite que você defina um alarme exato de repetição. Aplicativos que usam [ `SetRepeating` ](https://developer.xamarin.com/api/member/Android.App.AlarmManager.SetRepeating/p/Android.App.AlarmType/System.Int64/System.Int64/Android.App.PendingIntent/) e exigem alarmes exatos para trabalhar agora será precisa disparar cada alarme manualmente.

### <a name="external-storage"></a>Armazenamento externo

Armazenamento externo agora é dividido em dois tipos - armazenamento exclusivo para seu aplicativo e dados compartilhados por vários aplicativos. Lendo e gravando em local específico do aplicativo no armazenamento externo não exige nenhuma permissão especial. Interagir com dados no armazenamento compartilhado agora requer a `READ_EXTERNAL_STORAGE` ou `WRITE_EXTERNAL_STORAGE` permissão. Os dois tipos podem ser classificados como tal:

-  Se você estiver recebendo um caminho de arquivo ou diretório, chamando um método na `Context` - por exemplo, [ `GetExternalFilesDir` ](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalFilesDir/p/System.String/) ou [`GetExternalCacheDirs`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalCacheDirs%28%29/)
   - seu aplicativo não requer nenhuma permissão adicional.

-  Se você estiver recebendo um caminho de arquivo ou diretório ao acessar uma propriedade ou chamar um método `Environment` , como [ `GetExternalStorageDirectory` ](https://developer.xamarin.com/api/property/Android.OS.Environment.ExternalStorageDirectory/) ou [ `GetExternalStoragePublicDirectory` ](https://developer.xamarin.com/api/member/Android.OS.Environment.GetExternalStoragePublicDirectory/p/System.String/) , seu aplicativo requer o `READ_EXTERNAL_STORAGE` ou `WRITE_EXTERNAL_STORAGE` permissão.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE` indica o `READ_EXTERNAL_STORAGE` permissão, portanto você só precisa definir uma permissão.

### <a name="sms-consolidation"></a>Consolidação de SMS

KitKat simplifica mensagens para o usuário agregando todo o conteúdo SMS no aplicativo de um padrão selecionado pelo usuário. O desenvolvedor é responsável por fazer o aplicativo selecionável como o aplicativo de mensagens padrão e está se comportando corretamente no código e na vida se o aplicativo não estiver selecionado. Para obter mais informações sobre como fazer a transição de seu aplicativo SMS KitKat, consulte o [obtendo o SMS aplicativos prontos para KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) guia do Google.

### <a name="webview-apps"></a>Aplicativos do WebView

[WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) tem uma transformação em KitKat. A maior alteração é adicionada a segurança para carregar o conteúdo em um `WebView`. Embora a maioria dos aplicativos destinados a versões mais antigas de API devem funcionar conforme o esperado, teste os aplicativos que usam o `WebView` classe é altamente recomendável. Para obter mais informações sobre as APIs do WebView afetados, consulte o Android [migrando para WebView no Android 4.4](http://developer.android.com/guide/webapps/migrating.html) documentação.

<a name="user_experience" />

## <a name="user-experience"></a>Experiência do Usuário

KitKat vem com várias novas APIs para aprimorar a experiência do usuário, incluindo a nova estrutura de transição para tratamento de animações de propriedade e uma opção de interface do usuário transparente para temas. Essas alterações são abordadas a seguir.

### <a name="transition-framework"></a>Estrutura de transição

A estrutura de transição torna animações mais fácil de implementar. KitKat permite que você execute uma animação de propriedade simples com apenas uma linha de código ou personalizar transições usando *cenas*.

#### <a name="simple-property-animation"></a>Animação de propriedade simples

A nova biblioteca Android transições simplifica o code-behind animações de propriedade. A estrutura permite que você executar animações simples com mínimas de código. Por exemplo, o código a seguir exemplos de uso [ `TransitionManager.BeginDelayedTransition` ](https://developer.xamarin.com/api/member/Android.Transitions.TransitionManager.BeginDelayedTransition/p/Android.Views.ViewGroup/Android.Transitions.Transition/) para animar mostrando e ocultando um `TextView`:

```csharp
using Android.Transitions;

public class MainActivity : Activity
{
    LinearLayout linear;
    Button button;
    TextView text;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        linear = FindViewById<LinearLayout> (Resource.Id.linearLayout);
        button = FindViewById<Button> (Resource.Id.button);
        text = FindViewById<TextView> (Resource.Id.textView);

        button.Click += (o, e) => {

            TransitionManager.BeginDelayedTransition (linear);

            if(text.Visibility != ViewStates.Visible)
            {
                text.Visibility = ViewStates.Visible;
            }
            else
            {
                text.Visibility = ViewStates.Invisible;
            }
        };
    }
}
```

O exemplo acima usa a estrutura de transição para criar um automático, transição padrão entre a alteração dos valores de propriedade. Como a animação é tratada por uma única linha de código, você pode facilmente fazer isso compatível com versões anteriores do Android encapsulando a `BeginDelayedTransition` chamada em uma verificação de versão do sistema. Consulte o [migrar seu aplicativo para KitKat](#Migrating_Your_App_to_KitKat) seção para obter mais informações.

Captura de tela abaixo mostra o aplicativo antes da animação:

[![Captura de tela do aplicativo antes do início da animação](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

Captura de tela abaixo mostra o aplicativo depois que a animação:

[![Captura de tela do aplicativo após a conclusão de animação](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Você pode obter mais controle sobre a transição nos bastidores, que são abordadas na próxima seção.

#### <a name="android-scenes"></a>Nos bastidores Android

[Nos bastidores](https://developer.xamarin.com/api/type/Android.Transitions.Scene/) foram introduzidas como parte da estrutura de transição para dar ao desenvolvedor mais controle sobre animações. Nos bastidores criar uma área dinâmica na interface do usuário: especificar um contêiner e várias versões ou "nos bastidores", para o conteúdo XML dentro do contêiner, e Android faz o resto do trabalho para animar as transições entre nos bastidores. Nos bastidores Android permitem que você compile animações complexas com um mínimo de trabalho no lado do desenvolvimento.

O elemento de interface do usuário estático hospeda o conteúdo dinâmico é uma chamada um *contêiner* ou *cena base*. O exemplo a seguir usa o Android Designer para criar um `RelativeLayout` chamado `container`:

[![Usando o Designer de Android para criar um contêiner de RelativeLayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

O layout de exemplo também define um botão chamado `sceneButton` abaixo de `container`. Esse botão disparará a transição.

O conteúdo dinâmico dentro do contêiner requer dois novos layouts Android. Os layouts especificar apenas o código *dentro de* o contêiner.
O código de exemplo a seguir define um layout chamado *Scene1* que contém dois campos de texto de leitura "Kit" e "Kat" respectivamente, e um segundo layout chamado *Scene2* que contém os mesmos campos de texto revertidos. O XML é o seguinte:

 **Scene1.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kit"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textA"
        android:text="Kat"
        android:textSize="35sp" />
</merge>
```

 **Scene2.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kat"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textB"
        android:text="Kit"
        android:textSize="35sp" />
</merge>
```

O exemplo acima usa `merge` para tornar o código de exibição mais curto e simplificar a hierarquia de exibição. Você pode ler mais sobre `merge` layouts [aqui](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html).

Uma cena é criada chamando [ `Scene.GetSceneForLayout` ](https://developer.xamarin.com/api/member/Android.Transitions.Scene.GetSceneForLayout/p/Android.Views.ViewGroup/System.Int32/Android.Content.Context/), passando o objeto de contêiner, a ID de recurso do arquivo de layout da cena e atual `Context`, conforme ilustrado pelo exemplo de código abaixo:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

Clicando no botão inverte entre dois bastidores, Android anima com os valores de transição padrão:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

Captura de tela abaixo ilustra a cena antes da animação:

[![Captura de tela do aplicativo antes de inicia a animação](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Captura de tela abaixo ilustra a cena depois que a animação:

[![Captura de tela do aplicativo depois que a animação é concluída](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)


> [!NOTE]
> Há um [bug conhecido](https://code.google.com/p/android/issues/detail?id=62450) as transições Android biblioteca que faz com que o segundo plano criados usando `GetSceneForLayout` interrompido quando um usuário navega por meio de uma atividade na segunda vez. Uma solução alternativa de java é descrita [aqui](http://www.doubleencore.com/2013/11/new-transitions-framework/).


##### <a name="custom-transitions-in-scenes"></a>Transições personalizadas em segundo plano

Uma transição personalizada pode ser definida em um arquivo de recurso xml no `transition` diretório em `Resources`, conforme ilustrado na captura de tela abaixo:

[![Local do arquivo transition.xml no diretório de recursos/transição](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

O exemplo de código a seguir define uma transição que anima por 5 segundos e usa o [exceder interpolador](http://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

A transição é criada na atividade usando o [TransitionInflater](https://developer.xamarin.com/api/type/Android.Transitions.TransitionInflater/), conforme ilustrado pelo código a seguir:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

A nova transição é adicionada para o `Go` chamada que inicia a animação:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Interface de usuário transparente

KitKat oferece mais controle sobre temas seu aplicativo com barras de status e navegação transclucent opcional. Você pode alterar o translucency de elementos de interface do usuário do sistema no mesmo arquivo XML que você usa para definir o tema do Android. KitKat apresenta as seguintes propriedades:

-  `windowTranslucentStatus` -Quando definido como true, faz a barra de status superior transparente.

-  `windowTranslucentNavigation` -Quando definido como true, faz a barra de navegação inferior transparente.

-  `fitsSystemWindows` -Definir a barra superior ou inferior a transcluent desloca o conteúdo com os elementos de interface do usuário transparentes por padrão. Definir essa propriedade como `true` é uma maneira simple de impedir a sobreposição com os elementos de interface do usuário do sistema transparente de conteúdo.


O código a seguir define um tema com barras de status e navegação transparente:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <style name="KitKatTheme" parent="android:Theme.Holo.Light">
        <item name="android:windowBackground">@color/xamgray</item>
        <item name="android:windowTranslucentStatus">true</item>
        <item name="android:windowTranslucentNavigation">true</item>
        <item name="android:fitsSystemWindows">true</item>
        <item name="android:actionBarStyle">@style/ActionBar.Solid.KitKat</item>
    </style>

    <style name="ActionBar.Solid.KitKat" parent="@android:style/Widget.Holo.Light.ActionBar.Solid">
        <item name="android:background">@color/xampurple</item>
    </style>
</resources>
```

Captura de tela abaixo mostra o tema acima com status transparente e barras de navegação:

[![Captura de tela de exemplo de aplicativo com barras de status e navegação transparente](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Conteúdo do usuário

### <a name="storage-access-framework"></a>Estrutura de acesso de armazenamento

A estrutura de acesso de armazenamento (SAF) é uma nova maneira para os usuários interajam com o conteúdo armazenado como imagens, vídeos e documentos. Em vez de apresentar os usuários com uma caixa de diálogo para escolher um aplicativo para controlar o conteúdo, KitKat abre uma nova interface do usuário que permite aos usuários acessar seus dados em um local de agregação. Depois que o conteúdo foi escolhido, o usuário voltará para o aplicativo que solicitou o conteúdo e a experiência de aplicativo continuará normalmente.

Esta alteração requer duas ações no lado do desenvolvedor: primeiro, os aplicativos que exigem o conteúdo de provedores precisam ser atualizados para uma nova maneira de reqesting conteúdo. Segundo, aplicativos que gravam dados em um `ContentProvider` precisa ser modificado para usar a nova estrutura. Ambos os cenários dependem do novo [ `DocumentsProvider` ](https://developer.xamarin.com/api/type/Android.Provider.DocumentsProvider/) API.

#### <a name="documentsprovider"></a>DocumentsProvider

Em KitKat, interações com `ContentProviders` são abstraídos com o `DocumentsProvider` classe. Isso significa que SAF não importa onde os dados são fisicamente, desde que ele está acessível por meio de `DocumentsProvider` API. Provedores de locais, serviços de nuvem e dispositivos de armazenamento externo todo o uso a mesma interface e é tratada da mesma forma, fornecendo o usuário e o desenvolvedor de um lugar para interagir com conteúdo do usuário.

Esta seção aborda como carregar e salvar o conteúdo com o Framework de acesso de armazenamento.

#### <a name="request-content-from-a-provider"></a>Conteúdo da solicitação de um provedor

Podemos dizer KitKat que desejamos escolher o conteúdo usando a UI SAF com o `ActionOpenDocument` intenção, o que significa que desejamos para se conectar a todos os provedores de conteúdo disponíveis para o dispositivo. Você pode adicionar a filtragem para esse propósito, especificando `CategoryOpenable`, que significa que somente o conteúdo que pode ser aberto (ou seja, acessível utilizável conteúdo) será retornado. KitKat também permite a filtragem de conteúdo com o `MimeType`. Por exemplo, o código abaixo filtros para resultados de imagem, especificando a imagem `MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

Chamando `StartActivityForResult` inicia a UI SAF, que o usuário pode procurar para escolher uma imagem:

[![Captura de tela de exemplo de um aplicativo usando a estrutura de acesso de armazenamento para a navegação para uma imagem](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

Depois que o usuário escolher uma imagem, `OnActivityResult` retorna o `Android.Net.Uri` do arquivo escolhido. O exemplo de código a seguir exibe a seleção da imagem do usuário:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == save_request_code) {
        imageView = FindViewById<ImageView> (Resource.Id.imageView);
        imageView.SetImageURI (data.Data);
    }
}
```

#### <a name="write-content-to-a-provider"></a>Gravar o conteúdo para um provedor

Além de carregar o conteúdo a SAF UI, KitKat também permite que você salve o conteúdo em qualquer `ContentProvider` que implementa o `DocumentProvider` API. Salvando conteúda usa um `Intent` com `ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

O exemplo de código acima da UI SAF, permitindo que o usuário altere o nome do arquivo e selecione um diretório para armazenar o novo arquivo é carregado:

[![Captura de tela do usuário alterando o nome do arquivo para NewDoc no diretório de Downloads](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Quando o usuário pressiona **salvar**, `OnActivityResult` é passado a `Android.Net.Uri` do arquivo criado recentemente, que pode ser acessado com `data.Data`. O uri pode ser usado para transmitir dados para o novo arquivo:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == write_request_code) {
        using (Stream stream = ContentResolver.OpenOutputStream(data.Data)) {
            Encoding u8 = Encoding.UTF8;
            string content = "Hello, world!";
            stream.Write (u8.GetBytes(content), 0, content.Length);
        }
    }
}
```

Observe que [ `ContentResolver.OpenOutputStream(Android.Net.Uri)` ](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.OpenOutputStream/(Android.Net.Uri)) retorna um `System.IO.Stream`, de modo que todo o processo de fluxo contínuo pode ser escrito em .NET.

Para obter mais informações sobre o carregamento, criar e editar conteúdo com o Framework de acesso de armazenamento, consultem o [documentação Android para o Framework de acesso de armazenamento](http://developer.android.com/guide/topics/providers/document-provider.html).

### <a name="printing"></a>Imprimindo

Conteúdo de impressão é simplificado em KitKat com a introdução do [serviços de impressão](https://developer.xamarin.com/api/namespace/Android.PrintServices/) e `PrintManager`. KitKat também é a primeira versão de API para aproveitar totalmente a [APIs do serviço de impressão de nuvem do Google](https://developers.google.com/cloud-print/) usando o [aplicativo Google nuvem impressão](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
A maioria dos dispositivos que são fornecidos com KitKat automaticamente baixar o aplicativo de impressão de nuvem do Google e o [plug-in de serviços de impressão HP](https://play.google.com/store/apps/details?id=com.hp.android.printservice)quando eles se conectam pela primeira vez para Wi-Fi. Um usuário pode verificar as configurações de impressão do seu dispositivo navegando pela **Configurações > sistema > impressão**:

[![Captura de tela de exemplo da tela de configurações de impressão](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> Embora as APIs de impressão são configuradas para trabalhar com a impressão de nuvem do Google por padrão, o Android ainda permite que os desenvolvedores preparar o conteúdo de impressão usando as novas APIs e enviá-lo para outros aplicativos para lidar com a impressão.



#### <a name="printing-html-content"></a>Conteúdo HTML de impressão

KitKat cria automaticamente um [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) para um modo de exibição da web com `WebView.CreatePrintDocumentAdapter`. Conteúdo da web de impressão é um esforço coordenado entre um [ `WebViewClient` ](https://developer.xamarin.com/api/type/Android.Webkit.WebViewClient/) que aguarda o conteúdo HTML para carregar e permite que a atividade deve para disponibilizar a opção de impressão no menu de opções e Actvity, que aguarda até que o usuário Selecione a opção de impressão e chamadas `Print`sobre o `PrintManager`. Esta seção aborda a configuração básica necessária na tela imprimir conteúdo HTML.

Observe que o carregamento e imprimir o conteúdo da web requer a permissão de Internet:

[![Definir permissão de Internet nas opções de aplicativo](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Item do Menu Imprimir

A opção de impressão normalmente aparece na atividade de [menu Opções](http://developer.android.com/guide/topics/ui/menus.html#options-menu).
O menu de opções permite que os usuários a executar ações em uma atividade. Ele está no canto superior direito da tela e tem esta aparência:

[![Captura de tela de exemplo de impressão exibida de item de menu no canto superior direito da tela](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


Itens de menu adicionais podem ser definidas no *menu*diretório em *recursos*. O código a seguir define um menu de exemplo chamado item [impressão](https://developer.xamarin.com/api/type/Android.Print.PrintManager/):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

Interação com o menu de opções na atividade ocorre por meio de `OnCreateOptionsMenu` e `OnOptionsItemSelected` métodos.
`OnCreateOptionsMenu` é o local para adicionar novos itens de menu, como a opção de impressão, a partir de *menu* diretório de recursos.
`OnOptionsItemSelected` escuta para o usuário selecionar a opção de impressão no menu e começa a ser impressa:

```csharp
bool dataLoaded;

public override bool OnCreateOptionsMenu (IMenu menu)
{
    base.OnCreateOptionsMenu (menu);
    if (dataLoaded) {
        MenuInflater.Inflate (Resource.Menu.print, menu);
    }
    return true;
}

public override bool OnOptionsItemSelected (IMenuItem item)
{
    if (item.ItemId == Resource.Id.menu_print) {
        PrintPage ();
        return true;
    }
    return base.OnOptionsItemSelected (item);
}
```

O código acima também define uma variável chamada `dataLoaded` para acompanhar o status do conteúdo HTML. O `WebViewClient` será definir essa variável como true quando todo o conteúdo tiver sido carregado, para que a atividade Saiba para adicionar o item ao menu de opções.

##### <a name="webviewclient"></a>WebViewClient

O trabalho do `WebViewClient` é garantir que os dados no `WebView` é totalmente carregado antes da opção de impressão é exibido no menu, que é feito com o `OnPageFinished` método. `OnPageFinished` aguarda a conclusão do carregamento de conteúdo web e informa a atividade para recriar seu menu de opções com `InvalidateOptionsMenu`:

```csharp
class MyWebViewClient : WebViewClient
{
    PrintHtmlActivity caller;

    public MyWebViewClient (PrintHtmlActivity caller)
    {
        this.caller = caller;
    }

    public override void OnPageFinished (WebView view, string url)
    {
        caller.dataLoaded = true;
        caller.InvalidateOptionsMenu ();
    }
}
```

`OnPageFinished` também define o `dataLoaded` valor `true`, portanto `OnCreateOptionsMenu` pode recriar o menu com a opção de impressão em vigor.

##### <a name="printmanager"></a>PrintManager

O exemplo de código a seguir imprime o conteúdo de um `WebView`:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print` usa como argumentos: um nome para o trabalho de impressão ("MyWebPage" neste exemplo), um [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) que gera o documento de impressão do conteúdo, e [ `PrintAttributes` ](https://developer.xamarin.com/api/type/Android.Print.PrintAttributes/) (`null` do exemplo acima). Você pode especificar `PrintAttributes` para ajudar a formatar conteúdo na página impressa, embora os atributos padrão devem lidar com a maioria dos scenarions.

Chamando `Print` carrega a interface do usuário impressão, que lista as opções para o trabalho de impressão. A interface do usuário fornece aos usuários a opção de impressão ou salvar o conteúdo HTML para PDF, conforme ilustrado pela capturas de tela abaixo:

[![Captura de tela de PrintHtmlActivity exibir o menu Imprimir](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Captura de tela de PrintHtmlActivity exibindo Salvar como menu PDF](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Hardware

KitKat adiciona várias APIs para acomodar os novos recursos do dispositivo. O mais notável deles são baseadas em Host emulação de cartão e o novo `SensorManager`.

### <a name="host-based-card-emulation-in-nfc"></a>Emulação de cartão baseado em host em NFC

Emulação de cartão baseado em host (HCE) permite que aplicativos se comportam como cartões de NFC ou leitores de cartão de NFC sem depender proprietárias seguro elemento da transportadora. Antes de configurar o HCE, certifique-se de HCE está disponível no dispositivo com `PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE requer que ambos os o recurso HCE e `Nfc` permissão ser registrado com o aplicativo `AndroidManifest.xml`:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![Configurando a permissão de NFC nas opções de aplicativo](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Para trabalhar, HCE deve poderá ser executado em segundo plano, e ele deve iniciar quando o usuário faz uma transação NFC, mesmo que o aplicativo usando HCE não está em execução. Podemos pode fazer isso ao escrever o código HCE como um `Service`. Implementa um serviço HCE o `HostApduService` interface, que implementa os métodos a seguir:

-  *ProcessCommandApdu* -unidade de dados de protocolo de aplicativo um (APDU) é o que é enviado entre o leitor de NFC e o serviço de HCE. Esse método consome um ADPU do leitor e retorna uma unidade de dados em resposta.

-  *OnDeactivated* - o `HostAdpuService` é desativado quando o serviço HCE não está se comunicando com o leitor de NFC.


Um serviço HCE também precisa ser registrado com o manifesto do aplicativo e decorada com as permissões adequadas, filtrar intencional e metadados. O código a seguir é um exemplo de um `HostApduService` registrado com o manifesto do Android usando o `Service` atributo (para obter mais informações sobre atributos, consulte o Xamarin [trabalhando com manifesto do Android](~/android/platform/android-manifest.md) guia):

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("andorid.nfc.cardemulation.host.apdu_service",
    Resource="@xml/hceservice")]

class HceService : HostApduService
{
    public override byte[] ProcessCommandApdu(byte[] apdu, Bundle extras)
    {
        ...
    }

    public override void OnDeactivated (DeactivationReason reason)
    {
        ...
    }
}
```

O serviço acima fornece uma maneira para que o leitor de NFC interagir com o aplicativo, mas o leitor NFC ainda não tem como saber se esse serviço está emulando a placa NFC precisa verificar. Para ajudar o leitor NFC identificar o serviço, podemos atribuir o serviço de uma única *ID do aplicativo (recurso)*. Podemos especificar um recurso, juntamente com outros metadados sobre o serviço de HCE, em um arquivo de recurso xml registrado com o `MetaData` atributo (veja o exemplo de código acima). Este arquivo de recurso Especifica um ou mais filtros de AUXÍLIO - identificador exclusivo cadeias de caracteres em formato hexadecimal que correspondem aos recursos de um ou mais dispositivos de leitor de NFC:

```xml
<host-apdu-service xmlns:android="http://schemas.android.com/apk/res/android"
    android:description="@string/hce_service_description"
    android:requireDeviceUnlock="false"
    android:apduServiceBanner="@drawable/service_banner">
    <aid-group android:description="@string/aid_group_description"
                android:category="payment">
        <aid-filter android:name="1111111111111111"/>
        <aid-filter android:name="0123456789012345"/>
    </aid-group>
</host-apdu-service>
```

Além dos filtros da Ajuda, o arquivo de recurso xml também fornece uma descrição de voltado ao usuário do serviço HCE, especifica um grupo de recurso (aplicativo de pagamento versus "outros") e, no caso de um aplicativo de pagamento, uma faixa de dp 260 x 96 para exibir para o usuário.

A configuração descrita acima fornece os blocos de construção básicos de um aplicativo que emula um cartão de NFC. NFC em si requer várias etapas adicionais e outros testes para configurar. Para obter mais informações sobre a emulação de cartão baseado em Host, consulte o [portal de documentação Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Para obter mais informações sobre como usar NFC com Xamarin, confira o [exemplos Xamarin NFC](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Sensores

KitKat fornece acesso aos sensores do dispositivo por meio de um [ `SensorManager` ](https://developer.xamarin.com/api/type/Android.Hardware.SensorManager/).
O `SensorManager` permite que o sistema operacional agendar a entrega de informações de sensor em um aplicativo em lotes, preservando a vida útil da bateria.

KitKat também é fornecido com dois novos tipos de sensor para acompanhar as etapas do usuário. Elas se baseiam no acelerômetro e incluem:

-  *StepDetector* -aplicativo é notificado/ativado quando o usuário executa uma etapa e o detector fornece um valor de hora de quando ocorreu a etapa.

-  *StepCounter* -mantém controle sobre o número de etapas que o usuário tenha feito desde que o sensor foi registrado *até a próxima reinicialização do dispositivo*.

Captura de tela abaixo mostra o contador de etapa em ação:

[![Captura de tela do aplicativo SensorsActivity exibindo um contador de etapa](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

Você pode criar um `SensorManager` chamando `GetSystemService(SensorService)` e converter o resultado como uma `SensorManager`. Para usar o contador de etapa, chame `GetDeafultSensor` sobre o `SensorManager`. Você pode registrar o sensor e escuta as alterações na contagem de etapa com a Ajuda do [ `ISensorEventListener` ](https://developer.xamarin.com/api/type/Android.Hardware.ISensorEventListener/) de interface, conforme ilustrado pelo exemplo de código abaixo:

```csharp
public class MainActivity : Activity, ISensorEventListener
{
    float count = 0;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        SensorManager senMgr = (SensorManager) GetSystemService (SensorService);
        Sensor counter = senMgr.GetDefaultSensor (SensorType.StepCounter);
        if (counter != null) {
            senMgr.RegisterListener(this, counter, SensorDelay.Normal);
        }
    }

    public void OnAccuracyChanged (Sensor sensor, SensorStatus accuracy)
    {
        Log.Info ("SensorManager", "Sensor accuracy changed");
    }

    public void OnSensorChanged (SensorEvent e)
    {
        count = e.Values [0];
    }
}
```

`OnSensorChanged` é chamada se a contagem de etapa de atualizações enquanto o aplicativo estiver em primeiro plano. Se o aplicativo entra em segundo plano, ou se o dispositivo estiver no estado de suspensão, `OnSensorChanged` não será chamado; no entanto, as etapas continuarão a serem contados até `UnregisterListener` é chamado.

Tenha em mente que *o valor de contagem de etapa é cumulativo em todos os aplicativos que registram o sensor*. Isso significa que, mesmo que você desinstale e reinstale o aplicativo e inicializa o `count` variável em 0 na inicialização do aplicativo, o valor reportado pelo sensor permanecerá o número total de etapas executadas enquanto o sensor foi registrado, por sua aplicativo ou outro. Você pode impedir que seu aplicativo de adição para o contador de etapa chamando `UnregisterListener` no `SensorManager`, conforme ilustrado pelo código a seguir:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

Reinicializar o dispositivo redefine a contagem de etapa para 0. Seu aplicativo exigirá código extra para garantir que ele está relatando uma contagem precisa para o aplicativo, independentemente de outros aplicativos que usam o sensor ou o estado do dispositivo.


> [!NOTE]
> Enquanto a API para a detecção de etapa e a contagem é fornecido com KitKat, nem todos os telefones equipados com o sensor. Você pode verificar se o sensor está disponível executando `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`, ou verifique se o valor retornado de `GetDefaultSensor` não `null`.


<a name="developer_tools" />

## <a name="developer-tools"></a>Ferramentas para Desenvolvedores

### <a name="screen-recording"></a>Gravação de tela

KitKat inclui a nova tela de gravação de recursos para que os desenvolvedores podem gravar aplicativos em ação. Gravação de tela está disponível por meio de [Android depurar ponte (ADB)](http://developer.android.com/tools/help/adb.html) cliente, que pode ser baixado como parte do SDK do Android.

Para registrar sua tela, conecte o dispositivo; em seguida, localize sua instalação do SDK do Android, navegue até o **ferramentas da plataforma** diretório e execute o **adb** cliente:

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

O comando acima será gravado um vídeo de 3 minutos padrão com a resolução padrão de 4 Mbps. Para editar o comprimento, adicione o *– limite de tempo* sinalizador.
Para alterar a resolução, adicione o *-taxa de bits* sinalizador. O comando a seguir registrará um vídeo de minuto longa em 8Mbps:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

Você pode encontrar o vídeo em seu dispositivo - ele aparecerá na Galeria quando a gravação for concluída.


## <a name="other-kitkat-additions"></a>Outras adições KitKat

Além das alterações descritas acima, KitKat permite que você:

-  *Usar a tela inteira* -KitKat apresenta um novo [modo imersivo](http://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) para conteúdo de navegação, jogos e executar outros aplicativos que podem se beneficiar de uma experiência de tela inteira.

-  *Personalizar notificações* -obter detalhes adicionais sobre as notificações do sistema com o [ `NotificationListenerService` ](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) . Isso lhe permite apresentar as informações de forma diferente dentro de seu aplicativo.

-  *Espelhar recursos Drawable* -Drawable recursos têm uma nova [ `autoMirrored` ](http://developer.android.com/reference/android/R.attr.html#autoMirrored) atributo que informa o sistema criar uma versão espelhada para imagens que exigem inversão de layouts da esquerda para a direita.

-  *Pausar animações* -pausar e retomar animações criadas com o [ `Animator` ](https://developer.xamarin.com/api/type/Android.Animation.Animator/) classe.

-  *Ler texto alterando dinamicamente* -denotar partes da interface do usuário que atualizam dinamicamente pelo novo texto como "regiões ao vivo" com o novo [ `accesibilityLiveRegion` ](http://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion) de atributo para o novo texto será lido automaticamente no modo de acessibilidade.

-  *Aprimorar a experiência de áudio* -Verifique controla o volume com o [ `LoudnessEnhancer` ](https://developer.xamarin.com/api/type/Android.Media.Audiofx.LoudnessEnhancer/) , encontre o horário de pico e o RMS de um fluxo de áudio com o [ `Visualizer` ](https://developer.xamarin.com/api/field/Android.Media.Audiofx.Visualizer.MeasurementModePeakRms/) classe e obter informações de um [timestamp áudio](https://developer.xamarin.com/api/type/Android.Media.AudioTimestamp/) para ajudar com a sincronização de vídeo de áudio.

-  *Sincronizar ContentResolver no intervalo personalizado* -KitKat adiciona uma variação para a hora em que uma solicitação de sincronização é executada. Sincronizar um `ContentResolver` em tempo personalizado ou intervalo chamando `ContentResolver.RequestSync` e passando um `SyncRequest`.

-  *Distinguir entre os controladores* -em KitKat, os controladores são atribuídos identificadores de inteiro exclusivo que podem ser acessados por meio do dispositivo `ControllerNumber` propriedade. Isso torna mais fácil dizer players separados em um jogo.

-  *Controle remoto* -com poucas alterações no lado de hardware e software, KitKat permite ligar um dispositivo equipado com um transmissor IR para um controle remoto usando o `ConsumerIrService`e interagir com dispositivos periféricos com o novo [ `RemoteController` ](https://developer.xamarin.com/api/type/Android.Media.RemoteController/) APIs.

Para obter mais informações sobre as alterações de API acima, consulte o Google [APIs do Android 4.4](http://developer.android.com/about/versions/android-4.4.html) visão geral.


## <a name="summary"></a>Resumo

Este artigo introduziu algumas das novas APIs disponíveis no Android 4.4 (API nível 19) e coberto práticas recomendadas ao realizar a transição de um aplicativo para KitKat. IT tiver alterações destacadas para as APIs que afetam o usuário, incluindo o *framework transição* e novas opções para *temas*. Em seguida, ele introduziu o *acesso de armazenamento Framework* e `DocumentsProvider` classe, bem como o novo *impressão APIs*. Ele explorou *emulação de cartão baseado em host NFC* e como trabalhar com *sensores de baixa energia*, incluindo dois novos sensores para controlar as etapas do usuário. Por fim, demonstrado demonstrações em tempo real de aplicativos com a captura *gravação de tela*e fornecia uma lista detalhada de adições e alterações de KitKat API.


## <a name="related-links"></a>Links relacionados

- [Exemplo de KitKat](https://developer.xamarin.com/samples/KitKat/)
- [Android 4.4 APIs](http://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](http://developer.android.com/about/versions/kitkat.html)
