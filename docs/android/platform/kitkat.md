---
title: Recursos do KitKat
description: O Android 4,4 (KitKat) vem carregado com um infinidade de recursos para usuários e desenvolvedores. Este guia destaca vários desses recursos e fornece exemplos de código e detalhes de implementação para ajudá-lo a tirar o máximo proveito do KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 6f3df1c7c4664f4138e0f399419ac95e15231916
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757527"
---
# <a name="kitkat-features"></a>Recursos do KitKat

_O Android 4,4 (KitKat) vem carregado com um infinidade de recursos para usuários e desenvolvedores. Este guia destaca vários desses recursos e fornece exemplos de código e detalhes de implementação para ajudá-lo a tirar o máximo proveito do KitKat._

## <a name="overview"></a>Visão geral

O Android 4,4 (API nível 19), também conhecido como "KitKat", foi lançado no final de 2013. O KitKat oferece uma variedade de novos recursos e aprimoramentos, incluindo:

- [Experiência do usuário](#user_experience) &ndash; Animações fáceis com a estrutura de transição, as barras de status e de navegação translúcidas e o modo de imersão em tela inteira ajudam a criar uma experiência melhor para o usuário.

- [Conteúdo do usuário](#user_content) &ndash; O gerenciamento de arquivos do usuário simplificado com a estrutura de acesso de armazenamento; imprimir imagens, sites e outros conteúdos é mais fácil com APIs de impressão aprimoradas.

- [Hardware](#hardware) do Transforme qualquer aplicativo em um cartão NFC com emulação de placa baseada em host NFC; execute sensores de baixa energia `SensorManager` com o. &ndash;

- [Ferramentas para desenvolvedores](#developer_tools) &ndash; Aplicativos de screencast em ação com o cliente Android Debug Bridge, disponível como parte do SDK do Android.

Este guia fornece diretrizes para migrar um aplicativo Xamarin. Android existente para o KitKat, bem como uma visão geral de alto nível do KitKat para desenvolvedores do Xamarin. Android.

## <a name="requirements"></a>Requisitos

Para desenvolver aplicativos Xamarin. Android usando o KitKat, você precisa do *xamarin. Android 4.11.0* ou superior e do Android 4,4 (API nível 19) instalado por meio do gerenciador de SDK do Android, conforme ilustrado pela seguinte captura de tela:

[![Selecionando Android 4,4 no Gerenciador de SDK do Android](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Migrando seu aplicativo para o KitKat

Esta seção fornece alguns itens de primeira resposta para ajudar a fazer a transição de aplicativos existentes para o Android 4,4.

### <a name="check-system-version"></a>Verificar a versão do sistema

Se um aplicativo precisar ser compatível com versões mais antigas do Android, não se esqueça de encapsular qualquer código específico do KitKat em uma verificação de versão do sistema, conforme ilustrado pelo exemplo de código abaixo:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>Envio em lote de alarme

O Android usa serviços de alarme para ativar um aplicativo em segundo plano em um horário especificado. O KitKat leva isso um passo adiante, colocando os alarmes em lote para preservar a potência. Isso significa que, no lugar de ativar cada aplicativo em um momento exato, o KitKat prefere agrupar vários aplicativos que estão registrados para despertar durante o mesmo intervalo de tempo e ativá-los ao mesmo tempo.
Para instruir o [`AlarmManager`](xref:Android.App.AlarmManager)Android a ativar um aplicativo durante um intervalo de tempo `SetWindow` especificado, chame no, passando o tempo mínimo e máximo, em milissegundos, que pode decorrer antes que o aplicativo seja ativados e a operação a ser executada na ativação.
O código a seguir fornece um exemplo de um aplicativo que precisa ser ativados entre uma meia hora e uma hora a partir da hora em que a janela é definida:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Para continuar a ativar um aplicativo em um momento exato, `SetExact`use, passando o tempo exato que o aplicativo deve ser ativados e a operação a ser executada:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

O KitKat não permite mais que você defina um alarme de repetição exato. Aplicativos que usam[`SetRepeating`](xref:Android.App.AlarmManager.SetRepeating*)
e exigir que os alarmes exatos funcionem agora, será necessário disparar cada alarme manualmente.

### <a name="external-storage"></a>Armazenamento externo

O armazenamento externo agora está dividido em dois tipos – armazenamento exclusivo para seu aplicativo e dados compartilhados por vários aplicativos. Ler e gravar no local específico do seu aplicativo no armazenamento externo não requer permissões especiais. A interação com dados no armazenamento compartilhado agora requer a `READ_EXTERNAL_STORAGE` permissão `WRITE_EXTERNAL_STORAGE` ou. Os dois tipos podem ser classificados da seguinte forma:

- Se você estiver obtendo um caminho de arquivo ou diretório chamando um método em `Context` -por exemplo,[`GetExternalFilesDir`](xref:Android.Content.Context.GetExternalFilesDir*)
  or[`GetExternalCacheDirs`](xref:Android.Content.Context.GetExternalCacheDirs)
  - seu aplicativo não requer permissões adicionais.

- Se você estiver obtendo um arquivo ou caminho de diretório acessando uma propriedade ou chamando um `Environment` método em, como[`GetExternalStorageDirectory`](xref:Android.OS.Environment.ExternalStorageDirectory)
  or[`GetExternalStoragePublicDirectory`](xref:Android.OS.Environment.GetExternalStoragePublicDirectory*)
  , seu aplicativo requer a `READ_EXTERNAL_STORAGE` permissão `WRITE_EXTERNAL_STORAGE` ou.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE`implica a `READ_EXTERNAL_STORAGE` permissão, portanto, você só precisará definir uma permissão.

### <a name="sms-consolidation"></a>Consolidação de SMS

O KitKat simplifica as mensagens para o usuário agregando todo o conteúdo do SMS em um aplicativo padrão selecionado pelo usuário. O desenvolvedor é responsável por tornar o aplicativo selecionável como o aplicativo de mensagens padrão e se comportar adequadamente no código e na vida se o aplicativo não estiver selecionado. Para obter mais informações sobre como fazer a transição de seu aplicativo SMS para KitKat, consulte o guia [preparando seus aplicativos do SMS para o KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) do Google.

### <a name="webview-apps"></a>Aplicativos WebView

O [WebView](xref:Android.Webkit.WebView) tem uma reformulação em KitKat. A maior alteração é a segurança adicional para carregar o conteúdo `WebView`em um. Embora a maioria dos aplicativos destinados a versões de API mais antigas deva funcionar conforme o esperado `WebView` , é altamente recomendável testar os aplicativos que usam a classe. Para obter mais informações sobre as APIs do WebView afetadas, consulte a documentação migração do Android [para o WebView no android 4,4](https://developer.android.com/guide/webapps/migrating.html) .

<a name="user_experience" />

## <a name="user-experience"></a>Experiência do Usuário

O KitKat vem com várias novas APIs para aprimorar a experiência do usuário, incluindo a nova estrutura de transição para manipular animações de propriedade e uma opção de interface do usuário translúcida para elas. Essas alterações são abordadas abaixo.

### <a name="transition-framework"></a>Estrutura de transição

A estrutura de transição torna as animações mais fáceis de implementar. O KitKat permite que você execute uma animação de propriedade simples com apenas uma linha de código ou personalize as transições usando *cenas*.

#### <a name="simple-property-animation"></a>Animação de propriedade simples

A nova biblioteca de transições do Android simplifica as animações de propriedade code-behind. A estrutura permite que você execute animações simples com o mínimo de código. Por exemplo, o exemplo de código a seguir usa[`TransitionManager.BeginDelayedTransition`](xref:Android.Transitions.TransitionManager.BeginDelayedTransition*)
para animar mostrando e ocultando `TextView`:

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

O exemplo acima usa a estrutura de transição para criar uma transição automática padrão entre os valores de propriedade de alteração. Como a animação é manipulada por uma única linha de código, você pode facilmente torná-la compatível com versões mais antigas do Android `BeginDelayedTransition` , encapsulando a chamada em uma verificação de versão do sistema. Consulte a seção [migrando seu aplicativo para KitKat](#Migrating_Your_App_to_KitKat) para obter mais informações.

A captura de tela abaixo mostra o aplicativo antes da animação:

[![Captura de tela do aplicativo antes do início da animação](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

A captura de tela abaixo mostra o aplicativo após a animação:

[![Captura de tela do aplicativo após a conclusão da animação](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Você pode obter mais controle sobre a transição com cenas, que são abordadas na próxima seção.

#### <a name="android-scenes"></a>Cenas do Android

As [cenas](xref:Android.Transitions.Scene) foram introduzidas como parte da estrutura de transição para dar ao desenvolvedor mais controle sobre as animações. As cenas criam uma área dinâmica na interface do usuário: você especifica um contêiner e várias versões, ou "cenas", para o conteúdo XML dentro do contêiner e o Android faz o restante do trabalho para animar as transições entre os bastidores. Os bastidores do Android permitem criar animações complexas com o mínimo de trabalho no lado do desenvolvimento.

O elemento estático da interface do usuário que hospeda o conteúdo dinâmico é um chamado de base de *contêiner* ou *cena*. O exemplo a seguir usa o designer Android para criar `RelativeLayout` um `container`chamado:

[![Usando o Designer Android para criar um contêiner RelativeLayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

O layout de exemplo também define um botão `sceneButton` chamado abaixo `container`de. Esse botão irá disparar a transição.

O conteúdo dinâmico dentro do contêiner requer dois novos layouts do Android. Esses layouts especificam apenas o código *dentro* do contêiner.
O código de exemplo abaixo define um layout chamado *Scene1* que contém dois campos de texto lendo "kit" e "Kat", respectivamente, e um segundo layout chamado *Scene2* que contém os mesmos campos de texto invertidos. O XML é o seguinte:

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

Uma cena é criada chamando [`Scene.GetSceneForLayout`](xref:Android.Transitions.Scene.GetSceneForLayout*), passando o objeto de contêiner, a ID de recurso do arquivo de layout da cena e a atual `Context`, conforme ilustrado pelo exemplo de código abaixo:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

Clicar no botão inverte entre as duas cenas, que o Android anima com os valores de transição padrão:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

A captura de tela abaixo ilustra a cena antes da animação:

[![Captura de tela do aplicativo antes do início da animação](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

A captura de tela abaixo ilustra a cena após a animação:

[![Captura de tela do aplicativo após a conclusão da animação](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)

> [!NOTE]
> Há um [bug conhecido](https://code.google.com/p/android/issues/detail?id=62450) na biblioteca de transições do Android que faz com que as `GetSceneForLayout` cenas criadas usando o sejam interrompidas quando um usuário navega por uma atividade na segunda vez. Uma solução alternativa de Java é descrita [aqui](http://www.doubleencore.com/2013/11/new-transitions-framework/).

##### <a name="custom-transitions-in-scenes"></a>Transições personalizadas em cenas

Uma transição personalizada pode ser definida em um arquivo de recurso XML no `transition` diretório em `Resources`, conforme ilustrado pela captura de tela abaixo:

[![Local do arquivo TRANSITION. xml no diretório de recursos/transição](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

O exemplo de código a seguir define uma transição que anima por 5 segundos e usa o [interpolador de sobretransmissão](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

A transição é criada na atividade usando o [TransitionInflater](xref:Android.Transitions.TransitionInflater), conforme ilustrado pelo código abaixo:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

Em seguida, a nova transição é adicionada `Go` à chamada que começa a animação:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Interface do usuário translúcida

O KitKat oferece mais controle sobre a ti de seu aplicativo com barras de status e de navegação opcionais. Você pode alterar o translucency dos elementos da interface do usuário do sistema no mesmo arquivo XML usado para definir seu tema do Android. KitKat apresenta as seguintes propriedades:

- `windowTranslucentStatus`-Quando definido como true, torna a barra de status superior translúcida.

- `windowTranslucentNavigation`-Quando definido como true, torna a barra de navegação inferior translúcida.

- `fitsSystemWindows`-Definir a barra superior ou inferior como transcluent desloca o conteúdo sob os elementos da interface do usuário transparente por padrão. Definir essa propriedade como `true` é uma maneira simples de impedir que o conteúdo se sobreponha aos elementos da interface do usuário do sistema translúcida.

O código a seguir define um tema com status translúcida e barras de navegação:

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

A captura de tela abaixo mostra o tema acima com as barras de status e de navegação translúcidas:

[![Exemplo de captura de tela do aplicativo com status translúcida e barras de navegação](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Conteúdo do usuário

### <a name="storage-access-framework"></a>Estrutura de acesso de armazenamento

A estrutura de acesso de armazenamento (SAF) é uma nova maneira para os usuários interagirem com conteúdo armazenado, como imagens, vídeos e documentos. Em vez de apresentar aos usuários uma caixa de diálogo para escolher um aplicativo para lidar com o conteúdo, o KitKat abre uma nova interface do usuário que permite aos usuários acessar seus dados em um local de agregação. Após a escolha do conteúdo, o usuário retornará ao aplicativo que solicitou o conteúdo e a experiência do aplicativo continuará normalmente.

Essa alteração requer duas ações no lado do desenvolvedor: primeiro, os aplicativos que exigem conteúdo de provedores precisam ser atualizados para uma nova maneira de solicitar conteúdo. Em segundo lugar, os aplicativos que gravam dados para `ContentProvider` a necessidade de serem modificados para usar a nova estrutura. Ambos os cenários dependem do novo[`DocumentsProvider`](xref:Android.Provider.DocumentsProvider)
API.

#### <a name="documentsprovider"></a>DocumentsProvider

No KitKat, as interações com `ContentProviders` são abstratas com a `DocumentsProvider` classe. Isso significa que o SAF não se importa onde os dados estão fisicamente, desde que possam ser acessados por meio da `DocumentsProvider` API. Provedores locais, serviços de nuvem e dispositivos de armazenamento externo usam a mesma interface e são tratados da mesma forma, fornecendo ao usuário e ao desenvolvedor um local para interagir com o conteúdo do usuário.

Esta seção aborda como carregar e salvar conteúdo com a estrutura de acesso de armazenamento.

#### <a name="request-content-from-a-provider"></a>Solicitar conteúdo de um provedor

Podemos dizer ao KitKat que desejamos escolher o conteúdo usando a interface do usuário `ActionOpenDocument` do SAF com a intenção, o que significa que queremos nos conectar a todos os provedores de conteúdo disponíveis para o dispositivo. Você pode adicionar alguns filtros a essa intenção especificando `CategoryOpenable`, o que significa que apenas o conteúdo que pode ser aberto (por exemplo, conteúdo acessível e utilizável) será retornado. O KitKat também permite a filtragem de conteúdo `MimeType`com o. Por exemplo, o código abaixo filtra os resultados da imagem especificando a imagem `MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

Chamar `StartActivityForResult` inicia a interface do usuário do amSAF, que o usuário pode procurar para escolher uma imagem:

[![Captura de tela de exemplo de um aplicativo usando a estrutura de acesso de armazenamento para navegar até uma imagem](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

Depois que o usuário escolher uma imagem, `OnActivityResult` retornará o `Android.Net.Uri` do arquivo escolhido. O exemplo de código abaixo exibe a seleção de imagem do usuário:

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

#### <a name="write-content-to-a-provider"></a>Gravar conteúdo em um provedor

Além de carregar o conteúdo da interface do usuário do amSAF, o KitKat também permite que `ContentProvider` você salve o `DocumentProvider` conteúdo em qualquer um que implemente a API. Salvar conteúdo usa um `Intent` com `ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

O exemplo de código acima carrega a interface do usuário do amSAF, permitindo que o usuário altere o nome do arquivo e selecione um diretório para armazenar o novo arquivo:

[![Captura de tela do usuário que altera o nome do arquivo para NewDoc no diretório de downloads](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Quando o usuário pressiona **salvar** `OnActivityResult` , é passado o `Android.Net.Uri` do arquivo recém-criado, que pode ser acessado com `data.Data`. O URI pode ser usado para transmitir dados para o novo arquivo:

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

Observe que[`ContentResolver.OpenOutputStream(Android.Net.Uri)`](xref:Android.Content.ContentResolver.OpenOutputStream*)
Retorna um `System.IO.Stream`, portanto, todo o processo de streaming pode ser escrito em .net.

Para obter mais informações sobre como carregar, criar e editar conteúdo com a estrutura de acesso de armazenamento, consulte a [documentação do Android para a estrutura de acesso de armazenamento](https://developer.android.com/guide/topics/providers/document-provider.html).

### <a name="printing"></a>Imprimindo

A impressão de conteúdo é simplificada no KitKat com a introdução dos serviços `PrintManager`de [impressão](xref:Android.PrintServices) e do. KitKat também é a primeira versão de API para aproveitar totalmente as [APIs do serviço de impressão em nuvem do Google](https://developers.google.com/cloud-print/) usando o [aplicativo de impressão do Google Cloud](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
A maioria dos dispositivos fornecidos com o KitKat baixa automaticamente o aplicativo de impressão do Google Cloud e o [plug-in do serviço de impressão HP](https://play.google.com/store/apps/details?id=com.hp.android.printservice)quando eles se conectam pela primeira vez ao wifi Um usuário pode verificar as configurações de impressão de seu dispositivo navegando até **configurações > sistema > impressão**:

[![Instantâneo de exemplo da tela de configurações de impressão](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)

> [!NOTE]
> Embora as APIs de impressão estejam configuradas para funcionar com a impressão em nuvem do Google por padrão, o Android ainda permite aos desenvolvedores preparar o conteúdo de impressão usando as novas APIs e enviá-la a outros aplicativos para lidar com a impressão.

#### <a name="printing-html-content"></a>Imprimindo conteúdo HTML

O KitKat cria automaticamente [`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter) um para uma exibição da `WebView.CreatePrintDocumentAdapter`Web com. Imprimir conteúdo da Web é um esforço coordenado [`WebViewClient`](xref:Android.Webkit.WebViewClient) entre um que aguarda o carregamento do conteúdo HTML e permite que a atividade saiba disponibilizar a opção de impressão no menu opções e a atividade, que aguarda que o usuário selecione a opção de impressão e chama `Print`no .`PrintManager` Esta seção aborda a configuração básica necessária para imprimir o conteúdo HTML na tela.

Observe que o carregamento e a impressão de conteúdo da Web exigem a permissão da Internet:

[![Configurando permissão de Internet nas opções do aplicativo](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Item de menu imprimir

A opção de impressão aparecerá normalmente no menu de [Opções](https://developer.android.com/guide/topics/ui/menus.html#options-menu)da atividade.
O menu Opções permite que os usuários executem ações em uma atividade. Ele está no canto superior direito da tela e tem a seguinte aparência:

[![Captura de tela de exemplo do item de menu de impressão exibido no canto superior direito do ecrã](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)

Itens de menu adicionais podem ser definidos no diretório de *menu*em *recursos*. O código abaixo define um item de menu de exemplo chamado [Print](xref:Android.Print.PrintManager):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

A interação com o menu de opções na atividade ocorre por `OnCreateOptionsMenu` meio `OnOptionsItemSelected` dos métodos e.
`OnCreateOptionsMenu`é o local para adicionar novos itens de menu, como a opção de impressão, no diretório de recursos de *menu* .
`OnOptionsItemSelected`Escuta o usuário selecionando a opção imprimir no menu e começa a impressão:

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

O código acima também define uma variável chamada `dataLoaded` para controlar o status do conteúdo HTML. O `WebViewClient` definirá essa variável como true quando todo o conteúdo for carregado, portanto, a atividade saberá adicionar o item de menu imprimir ao menu opções.

##### <a name="webviewclient"></a>WebViewClient

O trabalho do `WebViewClient` é garantir que os dados `WebView` no sejam totalmente carregados antes que a opção imprimir seja exibida no menu, o que faz com o `OnPageFinished` método. `OnPageFinished`Escuta o conteúdo da Web para concluir o carregamento e informa à atividade para recriar seu menu de opções `InvalidateOptionsMenu`com:

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

`OnPageFinished`também define o `dataLoaded` valor para `true`, de `OnCreateOptionsMenu` modo que o pode recriar o menu com a opção imprimir em vigor.

##### <a name="printmanager"></a>PrintManager

O exemplo de código a seguir imprime o `WebView`conteúdo de um:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print`usa como argumentos: um nome para o trabalho de impressão ("MyWebPage" neste exemplo), um[`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter)
Isso gera o documento de impressão do conteúdo e[`PrintAttributes`](xref:Android.Print.PrintAttributes)
(`null` no exemplo acima). Você pode especificar `PrintAttributes` para ajudar a dispor o conteúdo na página impressa, embora os atributos padrão devam lidar com a maioria dos cenários.

Chamar `Print` carrega a interface do usuário de impressão, que lista as opções para o trabalho de impressão. A interface do usuário fornece aos usuários a opção de imprimir ou salvar o conteúdo HTML em um PDF, conforme ilustrado pelas capturas de tela abaixo:

[![Captura de tela de PrintHtmlActivity exibindo o menu imprimir](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Captura de tela de PrintHtmlActivity exibindo o menu salvar como PDF](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Hardware

O KitKat adiciona várias APIs para acomodar novos recursos de dispositivo. Os mais notáveis são emulação de placa baseada em host e o novo `SensorManager`.

### <a name="host-based-card-emulation-in-nfc"></a>Emulação de placa baseada em host no NFC

A emulação de placa baseada em host (HCE) permite que os aplicativos se comportem como cartões NFC ou leitores de cartão NFC sem depender do elemento seguro proprietário da operadora. Antes de configurar o HCE, verifique se o HCE está disponível no `PackageManager.HasSystemFeature`dispositivo com:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE requer que tanto o recurso HCE quanto a `Nfc` permissão sejam registrados com o `AndroidManifest.xml`aplicativo:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![Definindo a permissão NFC nas opções do aplicativo](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Para funcionar, o HCE precisa ser executado em segundo plano e precisa iniciar quando o usuário faz uma transação NFC, mesmo que o aplicativo que usa o HCE não esteja em execução. Podemos fazer isso escrevendo o código HCE como um `Service`. Um serviço HCE implementa a `HostApduService` interface, que implementa os seguintes métodos:

- *ProcessCommandApdu* -uma APDU (unidade de dados de protocolo de aplicativo) é o que é enviado entre o leitor NFC e o serviço HCE. Esse método consome um ADPU do leitor e retorna uma unidade de dados em resposta.

- *OnActivated* – o `HostAdpuService` será desativado quando o serviço HCE não estiver mais se comunicando com o leitor NFC.

Um serviço HCE também precisa ser registrado com o manifesto do aplicativo e decorado com as permissões apropriadas, o filtro de intenção e os metadados. O código a seguir é um exemplo de `HostApduService` um registrado com o manifesto do Android `Service` usando o atributo (para obter mais informações sobre atributos, consulte o guia de manifesto do Xamarin [trabalhando com Android](~/android/platform/android-manifest.md) ):

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("android.nfc.cardemulation.host.apdu_service",
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

O serviço acima fornece uma maneira para o leitor NFC interagir com o aplicativo, mas o leitor NFC ainda não tem como saber se esse serviço está emulando a placa NFC que precisa verificar. Para ajudar o leitor NFC a identificar o serviço, podemos atribuir ao serviço uma *ID de aplicativo exclusiva (auxílio)* . Especificamos um auxílio, junto com outros metadados sobre o serviço HCE, em um arquivo de recurso XML registrado com `MetaData` o atributo (Veja o exemplo de código acima). Este arquivo de recurso especifica um ou mais filtros de auxílio – cadeias de caracteres de identificador exclusivo em formato hexadecimal que correspondem aos auxílios de um ou mais dispositivos de leitor NFC:

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

Além de auxiliar os filtros, o arquivo de recursos XML também fornece uma descrição voltada para o usuário do serviço HCE, especifica um grupo de AUXÍLIOs (aplicativo de pagamento versus "outros") e, no caso de um aplicativo de pagamento, uma faixa de DP 260x96 para exibir ao usuário.

A configuração descrita acima fornece os blocos de construção básicos para um aplicativo emulando um cartão NFC. O NFC em si requer várias etapas adicionais e outros testes a serem configurados. Para obter mais informações sobre emulação de placa baseada em host, consulte o [portal de documentação do Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Para obter mais informações sobre como usar o NFC com Xamarin, confira os [exemplos do XAMARIN NFC](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Sensores

O KitKat fornece acesso aos sensores do dispositivo por meio [`SensorManager`](xref:Android.Hardware.SensorManager)de um.
O `SensorManager` permite que o sistema operacional Agende a entrega de informações de sensor para um aplicativo em lotes, preservando a vida útil da bateria.

O KitKat também é fornecido com dois novos tipos de sensor para acompanhar as etapas do usuário. Elas se baseiam no acelerômetro e incluem:

- *StepDetector* -o aplicativo é notificado/ativados quando o usuário executa uma etapa e o detector fornece um valor de tempo para quando a etapa ocorreu.

- *StepCounter* -controla o número de etapas que o usuário levou desde que o sensor foi registrado *até a próxima reinicialização do dispositivo*.

A captura de tela abaixo descreve o contador de etapas em ação:

[![Captura de tela do aplicativo SensorsActivity exibindo um contador de etapas](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

Você pode criar um `SensorManager` chamando `GetSystemService(SensorService)` e convertendo o resultado como um `SensorManager`. Para usar o contador de etapas, `GetDefaultSensor` chame `SensorManager`no. Você pode registrar o sensor e ouvir as alterações na contagem de etapas com a ajuda do[`ISensorEventListener`](xref:Android.Hardware.ISensorEventListener)
interface, conforme ilustrado pelo exemplo de código abaixo:

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

`OnSensorChanged`será chamado se a contagem de etapas for atualizada enquanto o aplicativo estiver em primeiro plano. Se o aplicativo entrar no plano de fundo, ou se o dispositivo `OnSensorChanged` estiver suspenso, não será chamado; no entanto, as etapas continuarão `UnregisterListener` a ser contadas até que o seja chamado.

Tenha em mente que *o valor da contagem de etapas é cumulativo em todos os aplicativos que registram o sensor*. Isso significa que mesmo se você desinstalar e reinstalar seu aplicativo e inicializar a `count` variável em 0 na inicialização do aplicativo, o valor relatado pelo sensor permanecerá o número total de etapas realizadas enquanto o sensor foi registrado, seja pelo seu aplicativo ou outro. Você pode impedir que seu aplicativo adicione ao contador de etapas chamando `UnregisterListener` `SensorManager`no, conforme ilustrado pelo código abaixo:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

A reinicialização do dispositivo redefine a contagem de etapas como 0. Seu aplicativo exigirá código extra para garantir que ele esteja relatando uma contagem precisa para o aplicativo, independentemente de outros aplicativos que usam o sensor ou o estado do dispositivo.

> [!NOTE]
> Embora a API para a detecção e a contagem de etapas seja fornecida com o KitKat, nem todos os telefones são ajustados com o sensor. Você pode verificar se o sensor está disponível executando `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`ou verificar para garantir que o `GetDefaultSensor` valor retornado não seja `null`.

<a name="developer_tools" />

## <a name="developer-tools"></a>Ferramentas para Desenvolvedores

### <a name="screen-recording"></a>Gravação de tela

O KitKat inclui novos recursos de gravação de tela para que os desenvolvedores possam registrar aplicativos em ação. A gravação de tela está disponível por meio do cliente [Android Debug Bridge (ADB)](https://developer.android.com/tools/help/adb.html) , que pode ser baixado como parte do SDK do Android.

Para gravar sua tela, conecte seu dispositivo; em seguida, localize a instalação do SDK do Android, navegue até o diretório de **ferramentas de plataforma** e execute o cliente **ADB** :

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

O comando acima irá gravar um vídeo padrão de 3 minutos na resolução padrão de 4Mbps. Para editar o comprimento, adicione o sinalizador *--Time-Limit* .
Para alterar a resolução, adicione o sinalizador *--bit-rate* . O comando a seguir gravará um vídeo de minuto e longo em 8Mbps:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

Você pode encontrar seu vídeo em seu dispositivo-ele aparecerá na Galeria quando a gravação for concluída.

## <a name="other-kitkat-additions"></a>Outras adições de KitKat

Além das alterações descritas acima, o KitKat permite que você:

- *Use a tela inteira* -KitKat introduz um novo [modo de imersão](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) para procurar conteúdo, jogar e executar outros aplicativos que podem se beneficiar de uma experiência de tela inteira.

- *Personalizar notificações* -Obtenha detalhes adicionais sobre as notificações do sistema com o[`NotificationListenerService`](xref:Android.Service.Notification.NotificationListenerService)
  . Isso permite que você apresente as informações de uma maneira diferente dentro do seu aplicativo.

- *Recursos de espelhamento de espelho* -os recursos de desenho têm um novo[`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)
  o atributo que diz ao sistema para criar uma versão espelhada para imagens que exigem inversão para layouts da esquerda para a direita.

- *Pausar animações* – pausar e retomar animações criadas com o[`Animator`](xref:Android.Animation.Animator)
  .

- *Ler texto de alteração dinâmica* – denotam partes da interface do usuário que são atualizadas dinamicamente com o novo texto como "regiões dinâmicas" com o novo[`accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
  o atributo, portanto, o novo texto será lido automaticamente no modo de acessibilidade.

- *Aprimore a experiência de áudio* -torne as trilhas mais alto com o[`LoudnessEnhancer`](xref:Android.Media.Audiofx.LoudnessEnhancer)
  , localize o pico e o RMS de um fluxo de áudio com o[`Visualizer`](xref:Android.Media.Audiofx.Visualizer.MeasurementModePeakRms)
  e obter informações de um carimbo de [data/hora de áudio](xref:Android.Media.AudioTimestamp) para ajudar com a sincronização de vídeo em áudio.

- *Sincronizar ContentResolver retornem no intervalo personalizado* -o KitKat adiciona certa variabilidade à hora em que uma solicitação de sincronização é executada. Sincronize um `ContentResolver` em hora ou intervalo personalizado chamando `ContentResolver.RequestSync` e passando um `SyncRequest`.

- *Distinguir entre controladores* -no KitKat, os controladores recebem identificadores inteiros exclusivos que podem ser acessados por meio `ControllerNumber` da Propriedade do dispositivo. Isso facilita a separação de jogadores em um jogo.

- *Controle remoto* -com algumas alterações no lado do hardware e do software, o KitKat permite que você transforme um dispositivo com um transmissor de ir em um controle remoto usando o `ConsumerIrService`e interaja com dispositivos periféricos com o novo[`RemoteController`](xref:Android.Media.RemoteController)
  API.

Para obter mais informações sobre as alterações de API acima, consulte a visão geral das APIs do Google [Android 4,4](https://developer.android.com/about/versions/android-4.4.html) .

## <a name="summary"></a>Resumo

Este artigo introduziu algumas das novas APIs disponíveis no Android 4,4 (API nível 19) e as práticas recomendadas cobertas ao fazer a transição de um aplicativo para o KitKat. Ele descreveu alterações nas APIs que afetam a experiência do usuário, incluindo a *estrutura de transição* e as novas opções para *elas*. Em seguida, ele introduziu a *estrutura* e `DocumentsProvider` a classe de acesso ao armazenamento, bem como as novas APIs de *impressão*. Ele explorou a *emulação de placa baseada em host NFC* e como trabalhar com *sensores de baixa energia*, incluindo dois novos sensores para controlar as etapas do usuário. Por fim, ele demonstrou a captura de demonstrações em tempo real de aplicativos com *gravação de tela*e forneceu uma lista detalhada das alterações e das adições da API KitKat.

## <a name="related-links"></a>Links relacionados

- [Exemplo de KitKat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/kitkat)
- [APIs do Android 4,4](https://developer.android.com/about/versions/android-4.4.html)
- [KitKat Android](https://developer.android.com/about/versions/kitkat.html)
